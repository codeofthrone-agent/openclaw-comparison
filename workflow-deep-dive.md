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

## 6. 🐍 Hermes Agent: Agentic Workflow + Python (意圖驅動)

**定位**：不使用 DSL，由 Agent 自主推理執行步驟，或由使用者生成確定性腳本。  
**機制**：`SKILL.md` (自然語言 SOP) + `Cron` (自然語言指令) + `execute_code` (Python 代碼生成)。

### 為什麼 Hermes 沒有 Lobster？
Hermes 的核心設計哲學是 **"Provider-agnostic & Agent-Centric"**。它認為固定的 YAML 流程缺乏彈性。
- 如果網頁連線失敗？Lobster 可能會中斷腳本，但 Hermes Agent 會**自主嘗試** Google 搜尋或重試。

### Hermes 的 Workflow 實作方式

#### A. 🟢 Agentic Skills (自然語言 SOP)
在 `SKILL.md` 中寫下步驟，Agent 會自動讀取並執行。

```markdown
# SKILL.md: Daily Report
## 目標
每天早上 9 點生成工作報告。
## 步驟
1. 檢查 `calendar` 技能獲取今天的會議。
2. 檢查 `email` 技能獲取未讀信件。
3. 撰寫摘要。
4. 發送到 Telegram。
```

#### B. 🔵 Code-as-Workflow (最省 Token + 100% 格式固定)
這是在 Hermes 中實現「類 Lobster (確定性)」的最佳方法。**讓 Agent 寫 Python，然後執行 Python。**
- **優點**：
    1. Token 消耗為 0（腳本執行不需要 LLM）。
    2. 結果格式 100% 固定 (JSON/CSV)，沒有幻覺。
    3. 錯誤處理 (try/except) 由 Python 控制，極其穩定。

## 7. 📦 CoPaw & LobsterAI: 應用程式化工作流

這兩個變體採取了「App 化」的路線。

### CoPaw (阿里/清華 Python 版)
- **機制**：依賴 Python 生態系的 `schedule` 或 `cron` 庫。
- **Workflow**: 就像寫 Python 腳本一樣，直接呼叫 `copaw` 提供的 SDK 進行自動化。對於中國開發者來說，這是最符合直覺的方式。

### LobsterAI (網易有道全場景版)
- **機制**：**注意！雖然名字有 Lobster，但它不是 OpenClaw 的 DSL。**
- **Workflow**: 它依賴於 **「Cowork Mode (協作模式)」**。
    - 用戶透過 Desktop App 啟動一個 Work Session。
    - Agent 在這個 Session 中持續執行任務（如爬蟲、分析），直到用戶停止。
    - 這是一種**互動式工作流**，而非背景自動化腳本。

## 8. 🦀 NullClaw & ZeptoClaw: 極簡主義 (Minimalist)

### NullClaw (Zig)
- **Workflow**: **無內建引擎**。
- **解法**：由於它是 678KB 的單一靜態執行檔，設計哲學是「Do One Thing Well」。依賴外部工具觸發（如 `cron`），它只需要負責接收指令、執行並退出。

### ZeptoClaw (Zig/Rust 實驗版)
- **Workflow**: 目前處於實驗階段，僅支援基礎的 CLI 指令串接。不具備複雜的背景任務調度能力。

## 9. 📊 總結對比矩陣 (15 項全景)

| 專案 | Workflow 機制 | Token 消耗 | 確定性 (固定格式) | 靈活性 | 適合誰？ |
|:---|:---|:---:|:---:|:---:|:---|
| **OpenClaw** | **Lobster DSL (YAML)** | ⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | 需要穩定自動化腳本的人 |
| **RivonClaw** | **Easy Mode (自然語言)** | ⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | 不想寫 YAML 的非技術人員 |
| **Hermes** | **Agentic Skills / Python** | 🔵 0 - ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 需要確定性結果 或 高容錯的人 |
| **ZeroClaw** | **SOPs (事件驅動)** | ⭐ | ⭐⭐⭐⭐ | ⭐⭐ | IoT / 嵌入式 / 警報系統 |
| **TinyAGI** | **Team Orchestrator** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | 管理多個 Agent 團隊的 PM |
| **NanoBot** | **Python Code** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Python 開發者 |
| **NanoClaw** | **Anthropic SDK Task** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 需要容器化隔離任務的人 |
| **CoPaw** | **Python SDK** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 阿里/清華生態系開發者 |
| **LobsterAI** | **Cowork Mode (互動)** | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | 需要 AI 陪著一起工作的人 |
| **IronClaw** | **無 (系統 Cron)** | ⭐ | N/A | N/A | 安全敏感，禁止動態腳本 |
| **Moltis** | **無 (系統 Cron)** | ⭐ | N/A | N/A | 單一進程服務，依賴外部 |
| **NullClaw** | **外部觸發 (CLI)** | ⭐ | N/A | N/A | 極致資源受限 |
| **ZeptoClaw** | **無 (命令列)** | ⭐ | N/A | N/A | 嘗試新技術的實驗者 |

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

### E. 🔵 Hermes Agent (Code-as-Workflow / Agentic)

#### 模式 A: 自動生成代碼 (最穩定)
**情境**：每天早上 9 點產出格式固定的 JSON 報表。
**方法**：告訴 Agent 撰寫 Python 腳本，並用 `cron` 定期執行該腳本。

```python
# ~/hermes-workflows/daily_report.py
import json
import requests
from datetime import datetime

def run():
    # 1. 獲取數據 (API)
    data = requests.get("https://api.example.com/metrics").json()
    
    # 2. 處理並強制格式
    report = {
        "date": datetime.now().isoformat(),
        "status": "OK" if data["cpu"] < 80 else "WARNING",
        "metrics": data
    }
    
    # 3. 寫入本地檔案或發送 Webhook
    with open("/tmp/report.json", "w") as f:
        json.dump(report, f)
    
    print(json.dumps(report))

if __name__ == "__main__":
    run()
```
然後在 Hermes 使用：`cronjob create "每天 9 點執行 ~/hermes-workflows/daily_report.py" --schedule "0 9 * * *"`

#### 模式 B: Agentic Skill (高容錯)
**情境**：幫我研究一個主題，搜尋結果可能變化。
**方法**：寫在 `SKILL.md` 中，由 Agent 自主決定如何搜尋和寫報告。
*(參考正文 Section 6.1 的 Markdown 範例)*

### F. 📦 NanoClaw (Anthropic SDK 容器任務)

```python
import docker
from anthropic import Anthropic

client = Anthropic()

# 使用容器隔離來執行危險任務
def run_isolated_task(prompt):
    # 1. 讓 LLM 生成 Bash 代碼
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        system="You are a secure coding assistant.",
        messages=[{"role": "user", "content": prompt}]
    )
    code = response.content
    
    # 2. 在臨時容器執行 (Docker Isolation)
    docker_client = docker.from_env()
    docker_client.containers.run("python:3.11", code, remove=True, network_disabled=True)

run_isolated_task("Scrape and analyze this URL...")
```

### G. 🛡️ IronClaw / Moltis (系統級 Cron 外部觸發)

```bash
# 利用 Linux crontab 定期呼叫 API 或執行腳本
# crontab -e

# 0 3 * * *  每天早上 3 點執行安全審計腳本
0 3 * * * /home/user/scripts/ironclaw-audit.sh

# 每隔 1 小時透過 Webhook 觸發 Moltis
0 * * * * curl -X POST http://localhost:8080/api/workflows/heartbeat
```

---

> **📄 報告版本**：v1.0 | **整理日期**：2026-04-04  
> **基於資料**：GitHub Repo 代碼結構分析、DeepWiki 架構文檔、變體對比報告。
