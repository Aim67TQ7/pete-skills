# 技能索引 SKILL INDEX

**先读此文件→** 找到需要→仅读该技能文件

## 快速参考 QUICK REF

| 任务 Task | 技能 Skill |
|-----------|------------|
| 文件交付→用户 | `file-delivery.md` |
| 创建/更新/查询任务 | `task-api.md` |
| 发送/读取消息 | `task-api.md` |
| API密钥/凭证更新 | `config-sync.md` |
| Supabase项目/表/桶 | `supabase.md` |
| Excel创建/编辑 | `xlsx.md` |
| PDF创建/编辑/合并 | `pdf.md` |
| Word文档创建/编辑 | `docx.md` |
| Pete实例克隆/部署 | `pete-provisioner.md` |
| Cron作业(无AI) | `cron-deployer.md` |
| 低成本AI Cron(Gemini) | `lightweight-ai-crons.md` |

**必读:** `COST.md` — 成本控制规则∅可协商

## 规则 RULES
1. **先读索引** — ∅加载所有技能到上下文
2. **仅加载所需** — 一任务=最多两个技能文件
3. **技能=参考文档** — 告诉你*如何*做, ∅*做什么*
4. **HEARTBEAT.md=做什么** — 技能=如何做
5. **COST.md=∅可协商** — ∅切换提供商, ∅模型选购

## 自动化架构 AUTOMATION ARCH
```
廉价Cron(Gemini Flash)          Pete(Anthropic)
├── news-digest.py              ├── 仅当inbox有项目时唤醒
├── thought-parser.py           ├── 使用Sonnet/Opus
└── ~$0.05/月                   └── 处理复杂任务
        │                               ▲
        └── 创建inbox任务 ──────────────┘
```
**Pete创建自动化 → Pete∅是自动化本身**

## 文档创建 DOC CREATION

| 格式 | 工具 | 安装 |
|------|------|------|
| .xlsx | openpyxl+pandas | `pip install openpyxl pandas` |
| .pdf | pypdf+pdfplumber+reportlab | `pip install pypdf pdfplumber reportlab` |
| .docx | docx-js(Node) | `npm install -g docx` |

**创建后→** 上传`uploads/`桶 → 发布链接到board

## 添加新技能 NEW SKILLS
1. 新建`.md`在`/root/clawd/skills/`
2. 添加到上方快速参考表
3. 保持聚焦 — 一技能一文件
