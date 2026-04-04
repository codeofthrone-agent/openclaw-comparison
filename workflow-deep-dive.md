# 🔄 OpenClaw 生態系 — Workflow 實作指南 (Deep Dive)

> **版本**：v1.0 | **日期**：2026-04-04  
> **用途**：詳細解答「每個框架如何處理自動化/工作流？」  
> **相關報告**：[教學報告 V1.0](./teaching-report-v1.md), [生態總結](./SUMMARY-report.md)

---

## 📑 目錄

- [1. 🏆 OpenClaw 核心：Lobster DSL (宣告式之王)](#1--openclaw-核心lobster-dsl-宣告式之王)
- [2. 🪆 RivonClaw：Easy Mode Workflow (Lobster + UI)](#2--rivonclaweasy-mode-workflow-lobster--ui)
- [3. ⚡ ZeroClaw：SOPs 事件驅動 (資源受限下的極速)}(#3--zeroclawsops-事件驅動-資源受限下的極速)
- [4. 🐍 Python 系 (NanoBot/NanoClaw)：Code-as-Workflow](#4--python-系-nanobotnanoclawcode-as-workflow)
- [5. 🏢 TinyAGI：Orchestrator (多代理編排)](#5--tinyagiorchestrator-多代理編排)
- [6. 🛡️ IronClaw & Moltis：極簡主義 (無內建引擎)](#6--ironclaw--moltis極簡主義-無內建引擎)
- [7. 📊 總對比矩陣與決策指南](#7--總對比矩陣與決策指南)
- [附錄：各框架完整 Workflow 配置範例](#附錄各框架完整-workflow-配置範例)

---

## 1. 🏆 OpenClaw 核心：Lobster DSL (宣告式之王)

**定位**：最完整的聲明式工作流引擎 (Declarative Workflow Engine)。  
**語言**：YAML  
**核心特色**：型別安全 (Typed)、與 A2UI Canvas 整合、支援 LLM 結構化輸出驗證。

### 架構邏輯

OpenClaw 的 Lobster 將工作流拆解為三個明確的階段：

```
[Trigger 觸發]
  │
  ├── Cron (定時：0 9 * * *)
  ├── Webhook (外部 POST 請求)
  ├── Event (系統事件、新訊息)
  └── Manual (手動觸發)
       │
       ▼
[Step 步驟列表] ——→ 依序執行 (或平行)
  │
  ├── exec (終端命令)
  ├── web_fetch (網頁抓取)
  ├── code_execution (沙盒代碼)
  ├── llm_task (AI 推理與總結)
  ├── canvas (渲染 UI)
  └── message (發送通知)
       │
       ▼
[Output 輸出]
  ├── JSON Schema (Zod 驗證)
  ├── Markdown (報告)
  └── 交付 (Delivery: Slack/Telegram/Discord)
```

### 配置範例：每日技術新聞摘要 + 圖片生成

```yaml
name: daily-tech-digest
description: "每日抓取 HackerNews 前 10 名，生成摘要與封面圖，發到 Slack"

trigger:
  cron: "0 8 * * 1-5"   # 週一至週五 8:00 AM

steps:
  # Step 1: 抓取資料
  - name: fetch-news
    tool: web_fetch
    url: "https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty"
    output_format: json

  # Step 2: AI 進行總結分析
  - name: summarize
    tool: llm_task
    prompt: |
      閱讀以下 JSON 中的技術新聞標題，選出最重要的 3 條，
      每條用 100 字繁體中文總結，並給出一個「影響指數」(1-10)。
      輸出必須符合 JSON Schema。
    data: "{{ steps.fetch-news.output }}"
    output_format: json
    schema:
      type: object
      properties:
        news:
          type: array
          items:
            type: object
            properties:
              title: { type: string }
              summary: { type: string }
              impact: { type: integer, minimum: 1, maximum: 10 }

  # Step 3: 生成視覺化圖片
  - name: generate-banner
    tool: image_generate
    prompt: >
      A futuristic tech news banner featuring: 
      {% for item in steps.summarize.news %}
      {{ item.title }}, 
      {% endfor %}
    provider: openai
    model: dall-e-3

  # Step 4: 發送報告
  - name: send-report
    tool: message
    channel: slack
    channel_id: "#tech-news"
    content: |
      📰 每日技術摘要 — {{ date('Y-m-d') }}

      {% for item in steps.summarize.news %}
      - **{{ item.title }}** (影響指數: {{ item.impact }})
        {{ item.summary }}
      {% endfor %}
    attachments:
      - image: "{{ steps.generate-banner.output.url }}"
```

### Lobster 的核心優勢

| 優勢 | 詳細說明 |
|:---|:---|
| 🧩 **變數傳遞** | 透過 `{{ steps.<name>.output }}` 語法，Step A 的輸出可直接成為 Step B 的輸入。 |
| 🛡️ **Zod 驗證** | `output_format: json` 搭配 `schema:`，確保 LLM 輸出的格式 100% 嚴格符合預期。錯誤會觸發重试。 |
| 🎨 **Canvas (A2UI)** | 不只是發訊息，Lobster 可以讓 Agent 渲染出 **交互式 UI (Canvas)** 給用戶看。 |
| 🔄 **Session Scope** | 支援 `main`, `isolated`, `current`。使用 `isolated` 可以避免 workflow 污染主對話上下文。 |

---

## 2. 🪆 RivonClaw: Easy Mode Workflow (Lobster + UI)

**定位**：Lobster 的「自然人言」入口。  
**核心邏輯**：**繼承 OpenClaw 核心**，但透過 Desktop App + Web Panel 提供 GUI。

### 如何工作？

RivonClaw 不需要你寫 YAML。你只需在 UI 中用自然語言描述：

> **用戶輸入**：「每天早上 9 點幫我檢查 GitHub 的 Star 數，如果有增長就發 Line 通知我。」

**RivonClaw 後台處理**：
1. AI 將這句話翻譯成 Lobster YAML。
2. 自動設定 Trigger (Cron) 和 Step。
3. 你只需點擊「確認啟用」。

### 適用人群
- **非工程師 / PM / 運營人員**：不想看 YAML，只想說人話。
- **快速配置**：適合簡單、標準的自動化需求。

---

## 3. ⚡ ZeroClaw: SOPs 事件驅動 (資源受限下的極速)

**定位**：專為嵌入式/IoT 設計的標準作業程序 (SOP)。  
**語言**：JSON / YAML  
**語言環境**：Rust (原生編譯，非解釋執行)。

### 與 Lobster 的根本差異

Lobster 是一個**解釋型引擎** (Node.js 解析 YAML 執行)。  
ZeroClaw 的 SOPs 是**事件驅動** (Event-Driven) 的。

```
事件源 (Event Source)
  │
  ├── 新訊息到達 (Channel Event)
  ├── GPIO 狀態改變 (硬體觸發)
  ├── MQTT 訊息到達 (IoT)
  └── 定時器 (Timer/Timer Wheel，非 Cron 解析)
       │
       ▼
[匹配規則 (Rule Matching)]
  │
  ├── 如果 訊息包含 "緊急"
  ├── 如果 CPU 溫度 > 80
  ├── 如果 門被打開
       │
       ▼
[執行動作 (Action Execution)]
  ├── 執行本地二進制指令
  ├── 發送警報訊息 (Telegram/Slack)
  └── 觸發硬體開關 (GPIO Relay)
```

### 配置範例：IoT 設備溫度監控 SOP

```json
{
  "sops": [
    {
      "id": "temp-alert",
      "description": "溫度過高警報 SOP",
      "trigger": {
        "type": "timer",
        "interval_seconds": 60
      },
      "condition": "sensor.temperature > 75",
      "actions": [
        {
          "type": "message",
          "channel": "telegram",
          "text": "⚠️ 警告：設備溫度已達 {{ sensor.temperature }}°C！"
        },
        {
          "type": "exec",
          "command": "/usr/bin/fan_controller --speed 100"
        }
      ]
    }
  ]
}
```

### ZeroClaw SOPs 優勢

| 優勢 | 詳細說明 |
|:---|:---|
| ⚡ **超高效能** | <10ms 觸發延遲，適合需要毫秒級響應的場景 (如安全警報)。 |
| 🛡️ **Rust 安全性** | SOP 邏輯在 Rust 環境下執行，不會有 Node.js 記憶體洩漏或腳本注入風險。 |
| 🔌 **硬體整合** | 唯一支援 GPIO/MQTT 原生觸發的框架。 |

---

## 4. 🐍 Python 系 (NanoBot/NanoClaw): Code-as-Workflow

**定位**：Python 原生腳本鏈。  
**適用對象**：開發者。  
**核心邏輯**：不設計 DSL，直接用 Python 寫 Task Chain。

### 為何選用 Code-as-Workflow？

對於 Python 程式師來說，學習 YAML 語法（如 Lobster）是多餘的。直接用 Python 程式碼可以獲得無限的靈活性。

### 實作機制：Python Async Task Chain

```python
import asyncio
import aiohttp
from openai import AsyncOpenAI

# 模擬 NanoBot 的 Workflow 框架
class DailyReportWorkflow:
    def __init__(self):
        self.ai = AsyncOpenAI(api_key="...")

    async def run(self):
        print("[1/3] 正在抓取數據...")
        data = await self.fetch_metrics()

        print("[2/3] AI 正在生成報告...")
        report = await self.ai_analyze(data)

        print("[3/3] 發送通知到 Telegram...")
        await self.send_telegram(report)
        print("✅ 工作流完成！")

    async def fetch_metrics(self):
        # 使用 aiohttp 非同步抓取 API
        async with aiohttp.ClientSession() as session:
            async with session.get("https://api.github.com/repos/...") as resp:
                return await resp.json()

    async def ai_analyze(self, data):
        response = await self.ai.chat.completions.create(
            model="gpt-4o",
            messages=[{"role": "user", "content": f"分析數據: {data}"}]
        )
        return response.choices[0].message.content

    async def send_telegram(self, report):
        # Telegram Bot API 發送邏輯
        print(f"已發送 Telegram: {report[:50]}...")

if __name__ == "__main__":
    asyncio.run(DailyReportWorkflow().run())
```

### Code-as-Workflow 優缺點

| 優勢 | 缺點 |
|:---|:---|
| 🚀 **無限靈活**：可以寫任何複雜的 `if/else`, `while loop`, 錯誤處理。 | 📝 **維護成本高**：腳本多了之後，難以像 YAML 那樣一目了然。 |
| 🐍 **無需新語法**：開發者只要會 Python 就能用。 | ⚠️ **不穩定**：腳本中的 Runtime error (如網路斷線) 如果沒寫好 try-except，整個 Workflow 會直接崩潰。 |
| 🧪 **易於單元測試**：可以用 `pytest` 測試每個步驟。 | 🧩 **變數傳遞**：需要手動管理變數 (`self.data = ...`)，不如 Lobster 的 `{{ steps.x }}` 直覺。 |

---

## 5. 🏢 TinyAGI: Orchestrator (多代理編排)

**定位**：一人公司 (One Person Company) 的任務分派中心。  
**核心邏輯**：不是跑腳本，而是**管理一個「團隊」**。

TinyAGI 的 Workflow 是面向「人/角色」的。你不需要定義「先做 A 再做 B」，而是定義**「誰負責什麼」**。

### 架構邏輯

```
[Incoming Task]
  │
  ├── "請幫我寫一篇部落格並發布"
  │
  ▼
[Orchestrator (調度中心)]
  │
  ├── 🔍 任務解析：這需要寫作 + 發布
  │
  ▼
📦 子任務分派
  ├── 任務 A: 寫作 (Agent: "Writer")
  │   └── 執行完畢 → 交付文章草稿
  ├── 任務 B: SEO 優化 (Agent: "SEO Expert")
  │   └── 依據草稿 → 產出關鍵字
  └── 任務 C: 發布 (Agent: "Publisher")
      └── 收到文章 + 關鍵字 → 上傳 WordPress
          ↓
[完成回報] → "任務已完成：文章已發布"
```

### TinyAGI 工作流優勢

| 維度 | 說明 |
|:---|:---|
| 🧩 **角色隔離** | 每個 Team Member (Agent) 有獨立的 Skills 和 Context。Writer 不需要知道 Publisher 的帳號密碼。 |
| 🌐 **多頻道** | Task 可以來自 Discord，結果回報到 Slack，非常適合跨平台協作。 |
| 🤖 **高階抽象** | 適合「目標導向」的任務。你不需要教 Agent 怎麼做（不像 Lobster 需要寫步驟），只需要告訴它目標，Orchestrator 會自動規劃路徑。 |

---

## 6. 🛡️ IronClaw & Moltis: 極簡主義 (無內建引擎)

**定位**：安全第一，功能第二。  
**現狀**：**沒有內建的 Workflow 引擎。**

對於這些強調安全 (Security/Audit) 的變體，引入動態的腳本執行引擎會增加攻擊面。

### 如何解決？

如果你使用 IronClaw 或 Moltis 且需要自動化：

1.  **系統層級 Cron**:
    使用 Linux/macOS 本身的 `crontab` 來定期呼叫 OpenClaw CLI 的 API 或指令。
    ```bash
    # crontab -e
    0 9 * * * /usr/bin/openclaw-cli run-task daily-report
    ```
2.  **外部 Webhook**:
    配置外部服務 (如 Zapier, Make.com) 發送 Webhook 到 IronClaw/Moltis 的 Web Gateway，觸發特定指令。

---

## 7. 📊 總對比矩陣與決策指南

### 工作流機制全對比

| 特性 | **OpenClaw** (Lobster) | **ZeroClaw** (SOPs) | **Python** (NanoBot) | **TinyAGI** (Orchestrator) | **IronClaw** (極簡) |
|:---:|:---|:---|:---|:---|:---|
| **類型** | 宣告式 DSL | 事件驅動 | 程式碼 (Code) | 多代理編排 | 外部觸發 |
| **格式** | YAML | JSON/YAML | Python | 配置 (Config) | 系統 Cron |
| **維護者** | 全棧工程師/IAM | 嵌入式工程 | 開發者 | PM / 營運商 | DevOps |
| **Token 消耗** | ⭐ (極低) | ⭐⭐ (低) | ⭐⭐⭐ (中/高) | ⭐⭐ (視規劃) | ⭐ (無) |
| **靈活性** | ⭐⭐⭐⭐ (中高) | ⭐⭐ (低) | ⭐⭐⭐⭐⭐ (最高) | ⭐⭐⭐⭐⭐ (極高) | ⭐ (無) |
| **除錯難度** | ⭐⭐⭐ (中) | ⭐ (易) | ⭐⭐⭐⭐⭐ (易) | ⭐⭐⭐⭐ (難) | ⭐⭐ (中) |
| **容錯力** | 低 (遇錯中斷) | 低 | 高 (可寫 try/catch) | 高 (Agent 可重試) | N/A |

### 決策指南：你的自動化屬於哪一種？

| 你的需求情境 | 推薦框架機制 |
|:---|:---|
| 我需要一個 **穩定的每日報表** (固定格式)。 | ✅ OpenClaw (Lobster YAML) |
| 我有一個 **IoT 設備警報** 需要 <10ms 反應。 | ✅ ZeroClaw (SOPs) |
| 我有一個 **複雜的數據處理流程** (需解析/判斷)。 | ✅ NanoBot/NanoClaw (Python 腳本) |
| 我需要管理 **整個公司流程** (寫作→審核→發布)。 | ✅ TinyAGI (Orchestrator) |
| 我要求 **最高安全**，不想跑動態腳本。 | ✅ IronClaw/Moltis (系統 Cron) |
| 我是非技術人員，想用自然語言設定。 | ✅ RivonClaw (Easy Mode) |

---

## 附錄：各框架完整 Workflow 配置範例

### A. OpenClaw (Lobster)

```yaml
# workflows/secure-backup.yaml
name: secure-backup
trigger:
  cron: "0 3 * * 0"
steps:
  - name: check-space
    tool: exec
    command: "df -h / | tail -1 | awk '{print $5}' | sed 's/%//'"
  
  - name: decide
    tool: llm_task
    prompt: "空間剩餘 {{ steps.check-space.output }}%。如果低於 20%，停止流程。"
    output_format: boolean
  
  - name: backup
    if: "{{ steps.decide.output == true }}"
    tool: exec
    command: "bash /scripts/backup.sh"
```

### B. ZeroClaw (SOPs)

```json
{
  "sops": [
    {
      "id": "daily-reboot",
      "trigger": { "type": "timer", "at": "04:00" },
      "actions": [
        { "type": "exec", "cmd": "systemctl restart openclaw-service" },
        { "type": "log", "msg": "Service restarted." }
      ]
    }
  ]
}
```

### C. NanoBot (Python Workflow)

```python
import schedule
import time

def job():
    print("Running NanoBot Workflow...")
    # 調用 LLM
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": "Generate daily summary"}]
    )
    # 發送通知
    bot.send_message(chat_id=123, text=response.text)

schedule.every().day.at("09:00").do(job)

while True:
    schedule.run_pending()
    time.sleep(60)
```

### D. TinyAGI (Orchestrator Config)

```json
{
  "project": "Blog Production",
  "teams": [
    {
      "name": "Writer",
      "skills": ["writing", "research"],
      "inputs": ["topic_from_discord"]
    },
    {
      "name": "Editor",
      "skills": ["proofreading", "seo"],
      "inputs": ["output_from_writer"]
    },
    {
      "name": "Publisher",
      "skills": ["wordpress-api"],
      "inputs": ["final_draft"]
    }
  ]
}
```

---

> **📄 報告版本**：v1.0 | **整理日期**：2026-04-04  
> **基於資料**：GitHub Repo 代碼結構分析、DeepWiki 架構文檔、變體對比報告。
