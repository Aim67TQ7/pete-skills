# Docker部署技能

**触发:** 创建新Web服务/API/站点 | 部署应用 | 管理运行服务

## 命令
```bash
bash skills/docker-deploy/deploy.sh build <name> <context-dir>
bash skills/docker-deploy/deploy.sh run <name> [host-port] [container-port]
bash skills/docker-deploy/deploy.sh stop <name>
bash skills/docker-deploy/deploy.sh remove <name>
bash skills/docker-deploy/deploy.sh list
bash skills/docker-deploy/deploy.sh logs <name>
bash skills/docker-deploy/deploy.sh caddy-add <name> <domain> <port>
```

## 规则
- 所有容器前缀`oc-` → `openclaw-net` Docker网络
- 部署追踪于`openclaw` PostgreSQL数据库
- `caddy-add`→通过HTTPS暴露外部服务
- 容器绑定127.0.0.1 → 外部访问via Caddy
