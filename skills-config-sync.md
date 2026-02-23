# 配置同步技能 — 凭证&配置管理

**触发:** 更新API密钥/凭证/同步配置

## 架构
**唯一真实来源:** Supabase表
**本地缓存:** `.env.pete`(从Supabase同步)
仅`SUPABASE_SERVICE_KEY`需本地存在→其余从Supabase同步

## Supabase表

### pete_secrets — API密钥/凭证
| 列 | 类型 | 说明 |
|----|------|------|
| key | text | 凭证名(如`ANTHROPIC_API_KEY`) |
| value | text | 密钥值 |
| provider | text | 服务名(如`anthropic`) |

### pete_config — 设置/偏好
| 列 | 类型 | 说明 |
|----|------|------|
| key | text | 设置名 |
| value | text | 设置值 |
| category | text | 分组(如`agent`,`gateway`) |

## 获取密钥
```bash
source /root/clawd/.env.pete
curl -s "$URL/rest/v1/pete_secrets?key=eq.ANTHROPIC_API_KEY&select=value" \
  -H "apikey: $SUPABASE_SERVICE_KEY" -H "Authorization: Bearer $SUPABASE_SERVICE_KEY"
```

## 更新凭证
```bash
curl -X PATCH "$URL/rest/v1/pete_secrets?key=eq.ANTHROPIC_API_KEY" \
  -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" -d '{"value":"sk-ant-new-key"}'
```

## 添加新凭证
```bash
curl -X POST "$URL/rest/v1/pete_secrets" -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" \
  -d '{"key":"NEW_KEY","value":"secret","provider":"service"}'
```

## ⚠ 注意
- ∅提交`.env.pete`到git
- ∅记录凭证值
- 始终使用Supabase作真实来源
- 仅ANTHROPIC_API_KEY用于Pete(见COST.md)
