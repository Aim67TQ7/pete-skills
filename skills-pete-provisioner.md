# Pete配置器技能 — 克隆&部署Pete实例

**触发:** 在Hostinger VPS上配置新Pete实例, 密钥存储于Supabase vault

## 架构
```
Supabase(Pete生态)                VPS(89.116.157.23)
├── tenants                      ├── /opt/pete-ecosystem/
├── pete_instances               ├── /opt/pete-instances/
├── task_queue                   │   └── pete-{slug}/
├── vault.secrets(加密)          │       └── pete_worker.py(∅.env)
└── execution_logs               └── Systemd服务
```
**安全:** 密钥仅在Supabase → Worker运行时获取→仅存内存 → ∅VPS文件系统凭证

## 前置条件

### Supabase Vault设置(一次)
```sql
CREATE SCHEMA IF NOT EXISTS vault;
CREATE TABLE vault.secrets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id UUID REFERENCES tenants(id) ON DELETE CASCADE,
  name TEXT NOT NULL, secret TEXT NOT NULL,
  UNIQUE(tenant_id, name));
ALTER TABLE vault.secrets ENABLE ROW LEVEL SECURITY;
-- 全局密钥(tenant_id=NULL)
INSERT INTO vault.secrets (tenant_id, name, secret) VALUES
  (NULL, 'GOOGLE_API_KEY', '...'),
  (NULL, 'ANTHROPIC_API_KEY', '...'),
  (NULL, 'SUPABASE_SERVICE_KEY', '...');
```

### pete_instances表
```sql
CREATE TABLE pete_instances (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id UUID REFERENCES tenants(id),
  slug TEXT NOT NULL UNIQUE,
  vps_host TEXT DEFAULT '89.116.157.23',
  port_assigned INTEGER,
  supported_models TEXT[] DEFAULT ARRAY['gemini-2.0-flash'],
  status TEXT DEFAULT 'provisioning',
  last_heartbeat TIMESTAMPTZ,
  consecutive_failures INTEGER DEFAULT 0);
```

## 配置步骤

### 1. 创建租户
```sql
INSERT INTO tenants (name, slug, email, status)
VALUES ('Acme', 'acme', 'billing@acme.com', 'active') RETURNING id;
```

### 2. 注册实例
```sql
SELECT COALESCE(MAX(port_assigned), 9000) + 1 FROM pete_instances;
INSERT INTO pete_instances (tenant_id, slug, port_assigned, supported_models)
VALUES ('uuid', 'acme', 9001, ARRAY['gemini-2.0-flash','claude-3.5-sonnet']);
```

### 3. 部署Worker
```bash
ssh -p 40546 root@89.116.157.23
SLUG="acme"; mkdir -p /opt/pete-instances/${SLUG}-pete
# 创建pete_worker.py → FastAPI + 运行时密钥获取
```

**Worker核心:** startup时从vault获取密钥→内存保存→`/health`+`/process`端点

### 4. Systemd服务
```bash
cat > /etc/systemd/system/pete-${SLUG}.service << EOF
[Unit]
Description=Pete Worker - ${SLUG}
[Service]
Environment="PETE_SUPABASE_URL=https://ezlmmegowggujpcnzoda.supabase.co"
Environment="PETE_PORT=${PORT}"
ExecStart=/usr/bin/python3 pete_worker.py
Restart=always
[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload && systemctl enable --now pete-${SLUG}
```

### 5. 验证
```bash
systemctl status pete-${SLUG}
curl http://localhost:${PORT}/health
```

## 端口分配
| 范围 | 用途 |
|------|------|
| 9000 | 主Pete桥(现有) |
| 9001-9099 | 租户Pete实例 |
| 9100-9199 | 专用模型Worker |

## 取消配置
```bash
systemctl stop pete-${SLUG} && systemctl disable pete-${SLUG}
rm /etc/systemd/system/pete-${SLUG}.service && systemctl daemon-reload
rm -rf /opt/pete-instances/${SLUG}-pete
```

## SSH访问
```bash
ssh -p 40546 root@89.116.157.23
```
