# OpenClaw 生態系全貌 — 13 變體完整報告（最終版）

> 整理日期：2026-04-04 | 來源：agents-radar 每日生態日報 + agent-browser 逐頁驗證 + DeepWiki 章節提取 + GitHub API 完整調查

---

## 1️⃣ 生態系總覽

截至 2026-04-04，OpenClaw 生態系包含 **13 個相關專案**，總計：

| 指標 | 數值 |
|------|------|
| **總 Stars** | ~510,000+ |
| **總 Forks** | ~99,000+ |
| **語言分佈** | TypeScript (4), Rust (4), Python (2), Go (1), Zig (1), 其他 (1) |
| **創建時間** | 2026-01-29 (Moltis) 到 2026-02-24 (CoPaw) |
| **確認數量** | **13 個**（所有日期報告清單固定，無額外專案） |

---

## 2️⃣ 13 個變體完整詳細檔案

### ★★★ OpenClaw (核心)
`openclaw/openclaw` | ⭐346,980 | 🍴69,150 | TypeScript | MIT | 2025-11-24

| 項目 | 說明 |
|------|------|
| **定位** | 個人 AI 助理，任何 OS、任何平台 |
| **核心** | 25+ 通訊平台，ACP 多 agent，原生 App (macOS/iOS/Android)，Plugin 生態系 |
| **DeepWiki** | ✅ 10 章 62 節 |
| **今日活躍** | 每日 500+ Issues, 500+ PRs, 每日自動發佈 |
| **GitHub** | https://github.com/openclaw/openclaw |
| **官網** | https://openclaw.ai |

---

### ★★ NanoBot (輕量化 Python 版)
`HKUDS/nanobot` | ⭐37,801 | 🍴6,552 | Python | MIT | 2026-02-01

| 項目 | 說明 |
|------|------|
| **定位** | 🐈 Ultra-Lightweight Personal AI Assistant — 代碼量比 OpenClaw 少 99% |
| **安裝** | `pip install nanobot-ai` |
| **通訊平台** | 12 個：Telegram, Discord, WhatsApp, 微信, 飛書, 釘釘, Slack, Matrix, Email, QQ, 企業微信, Mochat |
| **核心功能** | Token-based 記憶系統, MCP, Cron, Subagents, Docker |
| **技術棧** | Python 3.11+, OpenAI/Anthropic SDK, MCP, Socket.IO |
| **Open Issues** | 927 | **Pull Requests** | 558 |
| **DeepWiki** | ✅ 9 章節 |
| **GitHub** | https://github.com/HKUDS/nanobot |

---

### ★★ ZeroClaw (Rust 全平台版)
`zeroclaw-labs/zeroclaw` | ⭐29,342 | 🍴4,168 | Rust | Apache-2.0/MIT | 2026-02-13

| 項目 | 說明 |
|------|------|
| **定位** | Zero overhead. Zero compromise. 純 Rust、<5MB RAM、<10ms 冷啟動 |
| **二進制大小** | ~8.8MB 單一靜態二進制 |
| **通訊平台** | **28 個**（最多！）：WhatsApp(原生), Telegram, Slack, Discord, Signal, iMessage, Matrix, IRC, Email, Bluesky, Nostr, Mattermost, Nextcloud Talk, 釘釘, 飛書, QQ, Reddit, LinkedIn, X, MQTT, 企業微信, WATI, Mochat, Linq, Notion, WebSocket, ClawdTalk, CLI |
| **核心功能** | Hands(多代理編排), SOPs(事件驅動), Web Dashboard(React 19), 硬體集成(ESP32/STM32/Arduino/RPi), OpenClaw 自動遷移, E-stop, Sandbox(Landlock/Bubblewrap) |
| **技術棧** | Rust 2024 Edition, React/Vite/Tailwind CSS, Cloudflare/Tailscale/ngrok 隧道, Homebrew/Scoop/AUR/crates.io |
| **DeepWiki** | ✅ 13 章節 |
| **Open Issues** | 176 |
| **GitHub** | https://github.com/zeroclaw-labs/zeroclaw |

---

### ★★ PicoClaw (IoT/微型 Go 版)
`sipeed/picoclaw` | ⭐27,275 | 🍴3,831 | Go | MIT | 2026-02-04

| 項目 | 說明 |
|------|------|
| **定位** | $10 硬體 · <10MB RAM · ms 級啟動 — 專為 IoT/嵌入式設計 |
| **開發者** | Sipeed (嵌入式/AIoT 知名公司) |
| **目標場景** | ESP32、Raspberry Pi 等邊緣設備 |
| **技術棧** | Go, Docker 支援 |
| **Open Issues** | 231 | **Pull Requests** | 113 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/sipeed/picoclaw |

---

### ★★ NanoClaw (容器化安全版)
`qwibitai/nanoclaw` | ⭐26,373 | 🍴10,390 | TypeScript | MIT | 2026-01-31

| 項目 | 說明 |
|------|------|
| **定位** | Each agent runs in isolated containers. Built on Anthropic's Agents SDK. |
| **通訊平台** | WhatsApp, Telegram, Slack, Discord, Gmail, 其他 |
| **核心功能** | 容器隔離、Memory, Scheduled Jobs, 安全模型：Security by container isolation |
| **Fork 率** | 39%（極高） |
| **Open Issues** | 586 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/qwibitai/nanoclaw |

---

### ★ CoPaw (中國 Python 版)
`agentscope-ai/CoPaw` | ⭐14,363 | 🍴1,894 | Python | Apache-2.0 | 2026-02-24

| 項目 | 說明 |
|------|------|
| **定位** | Your Personal AI Assistant — 易安裝、deploy locally or on cloud |
| **開發者** | AgentScope AI (阿里雲/清華 AgentScope 團隊) |
| **核心特色** | Memory/personalization fully under your control, 支援多個聊天 APP，易擴展 |
| **安裝** | PyPI 可安裝 (`pip install copaw`) |
| **Open Issues** | 671 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/agentscope-ai/CoPaw |

---

### ★ IronClaw (隱私安全 Rust 版)
`nearai/ironclaw` | ⭐11,361 | 🍴1,303 | Rust | Apache-2.0/MIT | 2026-02-03

| 項目 | 說明 |
|------|------|
| **定位** | OpenClaw inspired, focused on **privacy and security** |
| **開發者** | NEAR AI (區塊鏈/AI 團隊) |
| **通訊平台** | 4 個：REPL, HTTP Webhooks, WASM 通道(Telegram, Slack), Web Gateway |
| **核心功能** | WASM 沙箱、Credential Protection (AES-256-GCM)、Prompt Injection 防禦、PostgreSQL + pgvector、動態工具構建、Web Gateway (SSE/WS) |
| **技術棧** | Rust, PostgreSQL 15+, NEAR AI OAuth, WASM + Docker |
| **DeepWiki** | ✅ 14 章節 |
| **Open Issues** | 478 |
| **GitHub** | https://github.com/nearai/ironclaw |

---

### LobsterAI (網易有道 全場景版)
`netease-youdao/LobsterAI` | ⭐4,826 | 🍴716 | TypeScript | MIT | 2026-02-12

| 項目 | 說明 |
|------|------|
| **定位** | 24/7 all-scenario AI agent that gets work done for you |
| **開發者** | 網易有道 (NetEase Youdao) |
| **平台** | macOS | Windows | Linux | Mobile（全平台支援） |
| **核心功能** | Cowork mode（協作模式）— 執行工具，不僅是聊天 |
| **Open Issues** | 814 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/netease-youdao/LobsterAI |

---

### NullClaw (Zig 極速版)
`nullclaw/nullclaw` | ⭐7,024 | 🍴833 | Zig | MIT | 2026-02-16

| 項目 | 說明 |
|------|------|
| **定位** | Fastest, smallest, fully autonomous AI assistant written in Zig |
| **二進制** | ~678 KB, <2ms startup, ~1MB 峰值 RSS |
| **通訊平台** | 19 個 |
| **關聯專案** | nullhub (UI 安裝配置介面，beta) |
| **Open Issues** | 108 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/nullclaw/nullclaw |

---

### TinyAGI (個人公司 Agent 編排)
`TinyAGI/tinyagi` | ⭐3,462 | 🍴491 | TypeScript | MIT | 2026-02-09

| 項目 | 說明 |
|------|------|
| **定位** | **Agent teams orchestrator for One Person Company** |
| **品牌演變** | TinyClaw → TinyAGI (fka TinyClaw) |
| **核心功能** | Multi-agent, Multi-team, Multi-channel, 24/7 AI assistant; 隔離工作區 |
| **通訊平台** | Discord + WhatsApp + Telegram |
| **AI 供應商** | Anthropic, OpenAI, Google |
| **Open Issues** | 56（極低，成熟穩定） |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/TinyAGI/tinyagi |

---

### Moltis (Rust 可信任版)
`moltis-org/moltis` | ⭐2,479 | 🍴291 | Rust | MIT | 2026-01-29

| 項目 | 說明 |
|------|------|
| **定位** | A secure persistent personal agent server in Rust. One binary — sandboxed, secure, auditable. |
| **核心功能** | Single binary, Voice, Memory, MCP Tools, Multi-channel access built-in |
| **技術棧** | Rust, CI/CD, WASM sandbox |
| **Open Issues** | 83 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/moltis-org/moltis |
| **官網** | https://moltis.org |

---

### ZeptoClaw (Zig「最終形態」)
`qhkm/zeptoclaw` | ⭐556 | 🍴78 | Rust | Apache-2.0 | 2026-02-12

| 項目 | 說明 |
|------|------|
| **定位** | "Final form of claw family (Wannabe)" — Ultra-lightweight personal AI assistant |
| **核心功能** | longterm_memory, mascot Zippy |
| **狀態** | 早期實驗專案 |
| **Open Issues** | 35 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/qhkm/zeptoclaw |

---

### RivonClaw (EasyClaw → RivonClaw) — OpenClaw UI 擴展層
`gaoyangz77/rivonclaw` | ⭐252 | 🍴56 | TypeScript | — | 2026-02-07

| 項目 | 說明 |
|------|------|
| **定位** | Built **ON TOP of OpenClaw** — easy-mode runtime + UI layer |
| **核心特色** | Desktop app + 系統托盤 + 本地 Web 面板，自然語言規則 + 反饋演進 |
| **差異化** | RivonClaw wraps OpenClaw，無需手動配置 skills/workflows，Agent 可自行進化 |
| **語言** | English + 中文 |
| **Open Issues** | 4 |
| **DeepWiki** | ✅ 存在 |
| **GitHub** | https://github.com/gaoyangz77/rivonclaw |

---

## 3️⃣ 13 變體對比總表

| 變體 | Stars | 語言 | 平台數 | DeepWiki | 核心定位 |
|------|-------|------|--------|----------|----------|
| OpenClaw | 346,980 | TypeScript | 25+ | ✅ 10 章 62 節 | 全能核心 |
| NanoBot | 37,801 | Python | 12 | ✅ 9 章 | 超輕量 Python |
| ZeroClaw | 29,342 | Rust | **28** | ✅ 13 章 | 全平台+硬體 |
| PicoClaw | 27,275 | Go | - | ✅ | IoT/嵌入式 |
| NanoClaw | 26,373 | TypeScript | - | ✅ | 容器安全 |
| CoPaw | 14,363 | Python | - | ✅ | 中國雲部署 |
| IronClaw | 11,361 | Rust | 4 | ✅ 14 章 | 隱私安全 |
| NullClaw | 7,024 | Zig | 19 | ✅ | Zig 極速 |
| LobsterAI | 4,826 | TypeScript | 全平台 | ✅ | 網易全場景 |
| TinyAGI | 3,462 | TypeScript | 3 | ✅ | 個人公司 |
| Moltis | 2,479 | Rust | - | ✅ | Rust 單一執行檔 |
| ZeptoClaw | 556 | Rust | - | ✅ | Zig 最終形態 |
| RivonClaw | 252 | TypeScript | - | ✅ | OpenClaw UI 層 |

---

## 4️⃣ DeepWiki 狀態

**全部 13 個變體都有 DeepWiki！✅**

| 變體 | DeepWiki 連結 | 章節數 |
|------|--------------|--------|
| OpenClaw | https://deepwiki.com/openclaw/openclaw | 10 章 62 節 |
| NanoBot | https://deepwiki.com/HKUDS/nanobot | 9 章 |
| ZeroClaw | https://deepwiki.com/zeroclaw-labs/zeroclaw | 13 章 |
| IronClaw | https://deepwiki.com/nearai/ironclaw | 14 章 |
| NanoClaw | https://deepwiki.com/qwibitai/nanoclaw | ✅ |
| NullClaw | https://deepwiki.com/nullclaw/nullclaw | ✅ |
| PicoClaw | https://deepwiki.com/sipeed/picoclaw | ✅ |
| LobsterAI | https://deepwiki.com/netease-youdao/LobsterAI | ✅ |
| TinyAGI | https://deepwiki.com/TinyAGI/tinyagi | ✅ |
| CoPaw | https://deepwiki.com/agentscope-ai/CoPaw | ✅ |
| Moltis | https://deepwiki.com/moltis-org/moltis | ✅ |
| ZeptoClaw | https://deepwiki.com/qhkm/zeptoclaw | ✅ |
| RivonClaw | https://deepwiki.com/gaoyangz77/rivonclaw | ✅ |

---

## 5️⃣ 按語言分佈

| 語言 | 專案數 | 專案 |
|------|--------|------|
| **TypeScript** | 4 | OpenClaw, NanoClaw, LobsterAI, TinyAGI, RivonClaw |
| **Rust** | 4 | ZeroClaw, IronClaw, Moltis, ZeptoClaw |
| **Python** | 2 | NanoBot, CoPaw |
| **Go** | 1 | PicoClaw |
| **Zig** | 1 | NullClaw |

---

## 6️⃣ 按定位分類

| 分類 | 專案 | 差異化 |
|------|------|--------|
| **全能核心** | OpenClaw | 最完整功能和平台覆蓋 |
| **輕量替代** | NanoBot (Python), NanoClaw (容器) | 降低入門門檻 |
| **極致效能** | ZeroClaw (Rust), NullClaw (Zig), ZeptoClaw, PicoClaw (Go) | 速度、體積、零依賴 |
| **IoT/嵌入式** | PicoClaw (Sipeed) | $10 硬體、<10MB RAM |
| **隱私安全** | IronClaw (NEAR AI), Moltis | WASM 沙箱、可審計 |
| **中國市場** | LobsterAI (網易), CoPaw (阿里/清華), NanoBot (微信/飛書/釘釘) | 本地化 |
| **個人公司** | TinyAGI | Agent Teams 編排 |
| **UI 擴展** | RivonClaw | OpenClaw 之上的易用層 |

---

## 7️⃣ 核心差異矩陣

| 功能 | OpenClaw | NanoBot | ZeroClaw | IronClaw | NanoClaw | CoPaw |
|------|----------|---------|----------|----------|----------|-------|
| 通訊平台 | 25+ | 12 | **28** | 4 | 6 | - |
| 硬體支持 | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| 容器沙箱 | ❌ | ✅ Docker | ✅ Landlock | ✅ WASM+Docker | ✅ 容器 | ❌ |
| Web UI | ✅ | ❌ | ✅ | ✅ | ❌ | ❌ |
| 資料庫 | ❌ | ❌ | ❌ | ✅ PostgreSQL | ❌ | ❌ |
| DeepWiki | ✅ 62 節 | ✅ 9 節 | ✅ 13 節 | ✅ 14 節 | ✅ | ✅ |

---

## 8️⃣ 結論

1. **13 個 = 全部** — 所有 OpenClaw 生態日報（從 2026-02-28 到 2026-04-03）都列出完全相同的 13 個
2. **OpenClaw 是絕對核心** (347K stars, 佔總 stars ~68%)
3. **全部 13 個變體都有 DeepWiki** ✅
4. **ZeroClaw 最全面** — 28 個通訊平台（最多），含硬體支持和 Web Dashboard
5. **Rust 是最受歡迎語言** — 4 個 Rust 專案（ZeroClaw, IronClaw, Moltis, ZeptoClaw）
6. **中國專案** — NanoBot (12 平台含微信/飛書/釘釘), LobsterAI (網易有道), CoPaw (阿里/清華)
7. **最有趣變體**：RivonClaw (唯一基於 OpenClaw 的擴展層，自然語言配置 AI 管家)

> **agents-radar** 是生態系的「雷達圖」—— 每日自動生成報告，覆蓋 13 個專案的 Issues/PRs/Releases，是了解 OpenClaw 生態系的最佳單一來源。
