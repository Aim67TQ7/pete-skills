# 轻量AI Cron技能 — 低成本AI自动化

**触发:** 创建AI驱动cron, ∅需Pete推理能力

## 核心原则
使用**Gemini Flash**执行简单AI任务:
- 按计划运行 → 做一个聚焦工作 → 输出到Supabase
- 如Pete需行动→创建inbox任务
- ∅直接调用Pete

## 批准模型

| 用途 | 模型 | 成本 |
|------|------|------|
| 摘要 | gemini-2.0-flash | ~$0.0001/1K tokens |
| 任务提取 | gemini-2.0-flash | ~$0.0001/1K tokens |
| TTS | Google Cloud TTS | ~$4/1M chars |

**∅在cron中使用Opus/Sonnet** → 如需→创建inbox任务给Pete

## Cron 1: 新闻摘要(2x/日)
**计划:** `0 7,18 * * *` | **脚本:** news-digest.py

```python
#!/usr/bin/env python3
"""news-digest.py — 摘要新闻+TTS | 2x/日 | Gemini Flash"""
import os, json, httpx
from google import genai

SUPABASE_URL = os.environ.get("SUPABASE_URL", "https://ezlmmegowggujpcnzoda.supabase.co")
SUPABASE_KEY = os.environ["SUPABASE_SERVICE_KEY"]

def fetch_news():
    # RSS/JSON源获取标题
    pass

def summarize(headlines):
    client = genai.Client(api_key=os.environ["GOOGLE_API_KEY"])
    response = client.models.generate_content(
        model="gemini-2.0-flash",
        contents=f"Summarize for mfg exec. Focus: AI/manufacturing/supply chain.\n{json.dumps(headlines)}")
    return response.text

def save(summary):
    httpx.post(f"{SUPABASE_URL}/rest/v1/news_digests",
        headers={"apikey": SUPABASE_KEY, "Authorization": f"Bearer {SUPABASE_KEY}",
                 "Content-Type": "application/json"},
        json={"summary": summary})
```

## Cron 2: 思绪解析器(每小时)
**计划:** `0 * * * *` | **脚本:** thought-parser.py
**输入:** parking_lot表 → **输出:** tasks表(inbox)

```python
#!/usr/bin/env python3
"""thought-parser.py — 解析语音备忘→任务卡 | 每小时 | Gemini Flash"""
import os, json, httpx
from google import genai

def fetch_unprocessed():
    # GET parking_lot?processed=eq.false
    pass

def extract_tasks(thought):
    client = genai.Client(api_key=os.environ["GOOGLE_API_KEY"])
    response = client.models.generate_content(
        model="gemini-2.0-flash",
        contents=f"Extract actionable tasks from:\n{thought['content']}\nReturn JSON array.")
    # 解析JSON响应
    return json.loads(response.text)

def create_task(task_data, source_id):
    # POST tasks → status=inbox
    pass

def mark_processed(thought_id):
    # PATCH parking_lot?id=eq.{id} → processed=true
    pass
```

## 所需Supabase表
```sql
CREATE TABLE parking_lot (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content TEXT NOT NULL, source TEXT,
  processed BOOLEAN DEFAULT FALSE,
  processed_at TIMESTAMPTZ, created_at TIMESTAMPTZ DEFAULT NOW());

CREATE TABLE news_digests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  summary TEXT NOT NULL, audio_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW());
```

## 成本估算

| Cron | 频率 | Tokens/次 | 日成本 |
|------|------|-----------|--------|
| news-digest | 2x/日 | ~2K | ~$0.0004 |
| thought-parser | 24x/日 | ~500 | ~$0.0012 |
| **合计** | | | **~$0.05/月** |

## 规则
1. 仅Gemini Flash — ∅Opus ∅Sonnet
2. 输出到Supabase — ∅Board消息
3. 创建inbox任务 — Pete后续处理
4. ∅Pete调用 — 独立运行
5. 记录一切 — 调试+成本追踪
