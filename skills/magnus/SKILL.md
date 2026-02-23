---
name: magnus
description: "Bunting Magnetics设备专家 — 路由到MAGNUS代理(端口8401)"
metadata:
  openclaw:
    emoji: "🧲"
---

# MAGNUS — Bunting设备专家

## 描述
将Bunting Magnetics设备问题委派给MAGNUS专家代理

## 触发条件
涉及以下任何主题时激活:
- 磁铁/磁分离/磁设备
- 金属杂质去除/污染物检测
- 物料处理/输送机/给料机
- 金属探测器(METRON系列)
- 涡流分离器
- 板磁/悬挂磁/抽屉磁/格栅磁/鼓磁
- 头滚轮/内联磁/子弹磁/液体陷阱
- 行业: 食品/回收/采矿/塑料/制药/乳品
- 设备选型/尺寸/推荐
- Bunting/BFR Group/bfrgroup.com
- 高斯评级/磁场强度/温度限制
- HACCP/FSMA/CCP/PCP合规
- 外部销售设备咨询

## 使用方法
```bash
curl -s -X POST http://127.0.0.1:8401/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "CUSTOMER QUESTION HERE"}'
```

**响应JSON:** `answer`(专家回复+产品链接) + `files_consulted` + `question`

## 响应处理
1. 直接转发MAGNUS回答→用户
2. 回答已含bfrgroup.com产品链接
3. 含资格评分(适用时)
4. ∅修改/∅摘要 — MAGNUS回复已销售就绪

## 健康检查
```bash
curl -s http://127.0.0.1:8401/health
```
