# ZeroClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本：ZeroClaw (zeroclaw-labs/zeroclaw), OpenClaw v2026.3.23+

---

## 1️⃣ 核心定位

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **Repo** | `zeroclaw-labs/zeroclaw` | `openclaw/openclaw` |
| **設計理念** | **Zero overhead. Zero compromise.** — 極致效能、極致精簡 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | 純 Rust 單一靜態二進制，<5MB RAM、<10ms 冷啟動，為效能犧牲一切 | Node.js 生態、Gateway 架構、25+ 平台、原生 App、plugin 系統 |
| **語言** | Rust (2024 Edition) | TypeScript / Node.js |
| **Stars** | ⭐ 29,342 | ⭐ 35,000+ (參考) |
| **Forks** | 🍴 4,168 | 🍴 10,000+ |
| **License** | Apache-2.0 / MIT | Apache-2.0 |
| **二進制大小** | **~8.8MB** 單一靜態二進制 | npm 套件（含 node_modules ~數百 MB） |

### 定位圖譜

```
              效能導向 ◄─────────────────────────► 功能導向
              
ZeroClaw ██████████████░░░░░░░░░░░░░░░░
OpenClaw  ░░░░░░░░░░░░░░░░██████████████

              資源佔用 ◄─────────────────────────► 生態豐富

ZeroClaw ██████████████░░░░░░░░░░░░░░░░  (5MB RAM, 8.8MB binary)
OpenClaw  ░░░░░░░░░░░░░░░░░░░░██████████  (node_modules, V8 heap)

              嵌入式友好 ◄─────────────────────────► 桌面/雲端優先

ZeroClaw ████████████████████████░░░░░░  (ESP32/STM32/RPi 直上)
OpenClaw  ░░░░░░░░░░░░░░░░████████████  (需要完整 Node.js runtime)
```

---

## 2️⃣ 架構比較

### ZeroClaw 架構

```
┌─────────────────────────────────────────────────────────┐
│                    ZeroClaw Binary (~8.8MB)              │
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │ Channel  │  │ Core     │  │ Hands    │  │ SOPs    │ │
│  │ Manager  │→ │ Engine   │→ │ (Multi-  │→ │ (Event  │ │
│  │          │  │ (Rust)   │  │  Agent)  │  │ Driven) │ │
│  └──────────┘  └──────────┘  └──────────┘  └─────────┘ │
│       │              │              │            │       │
│       ▼              ▼              ▼            ▼       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │ 28       │  │ Sandbox  │  │ Hardware │  │ Web     │ │
│  │ Platforms│  │ Landlock │  │ Bridge   │  │ Dash    │ │
│  │          │  │ Bubblewrap│ │ ESP32/   │  │ React19 │ │
│  └──────────┘  └──────────┘  │ STM32/   │  └─────────┘ │
│                              │ Arduino  │               │
│                              │ RPi      │               │
│                              └──────────┘               │
└─────────────────────────────────────────────────────────┘
```

### OpenClaw 架構

```
┌─────────────────────────────────────────────────────────┐
│                     OpenClaw Gateway                     │
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │ Channel  │  │ Auth     │  │ Routing  │  │ Plugin  │ │
│  │ Adapters │→ │ & DM     │→ │ Engine   │→ │ Manager │ │
│  │ (25+)    │  │ Policy   │  │ Bindings │  │         │ │
│  └──────────┘  └──────────┘  └──────────┘  └─────────┘ │
│       │              │              │            │       │
│       ▼              ▼              ▼            ▼       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │ Per-Agent│  │ ACP      │  │ Tools    │  │ Native  │ │
│  │ Runtime  │  │ Sub-Agts │  │ Sandbox  │  │ Apps    │ │
│  │ Pi Agent │  │ + ACPX   │  │ Docker   │  │ macOS/  │ │
│  │          │  │ Harness  │  │ seccomp  │  │ iOS/And │ │
│  └──────────┘  └──────────┘  └──────────┘  └─────────┘ │
└─────────────────────────────────────────────────────────┘
```

### 核心差異對比

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **核心語言** | Rust 2024 Edition | TypeScript / Node.js |
| **編譯方式** | 單一靜態二進制（~8.8MB） | npm 套件 + node_modules（數百MB） |
| **記憶體佔用** | **<5MB RAM** | 需要 V8 堆，通常 100MB+ |
| **冷啟動** | **<10ms**（無需 JIT warmup） | 數百 ms ~ 秒級（Node 啟動 + require） |
| **GC 影響** | **無需 GC**（Rust ownership） | V8 GC pauses 可能影響即時性 |
| **部署大小** | 一個二進制檔案 `cargo install` / `brew install` | npm install + 依賴樹 |

---

## 3️⃣ 通訊平台

### ZeroClaw — 28 個平台（最多！）

| # | 平台 | 類型 | 備註 |
|---|------|------|------|
| 1 | WhatsApp | IM | **原生支援** |
| 2 | Telegram | IM | - |
| 3 | Slack | Workspace | - |
| 4 | Discord | Community | - |
| 5 | Signal | IM | 加密通訊 |
| 6 | iMessage | IM | Apple 生態 |
| 7 | Matrix | IM | 聯邦式 |
| 8 | IRC | Legacy | 經典聊天 |
| 9 | Email | IMAP/SMTP | 非即時 |
| 10 | Bluesky | 社群 | AT Protocol |
| 11 | Nostr | 社群 | 去中心化 |
| 12 | Mattermost | Workspace | 自架 Slack 替代 |
| 13 | Nextcloud Talk | 協作 | 自架 |
| 14 | 釘釘 (DingTalk) | 企業 | **中國市場** |
| 15 | 飛書 (Feishu) | 企業 | **中國市場** |
| 16 | QQ | IM | **中國市場** |
| 17 | Reddit | 社群 | - |
| 18 | LinkedIn | 社群 | - |
| 19 | X (Twitter) | 社群 | - |
| 20 | MQTT | IoT | 物聯網標準 |
| 21 | 企業微信 (WeCom) | 企業 | **中國市場** |
| 22 | WATI | 商務 | WhatsApp API |
| 23 | Mochat | 商務 | 微信 SCRM |
| 24 | Linq | 商務 | - |
| 25 | Notion | 協作 | - |
| 26 | WebSocket | 通訊 | 通用 |
| 27 | ClawdTalk | 自有 | ZeroClaw 協議 |
| 28 | CLI | 終端 | - |

### OpenClaw — 25+ 個平台（含原生 App）

| # | 平台 | 類型 | 備註 |
|---|------|------|------|
| 1-8 | WhatsApp/Telegram/Slack/Discord 等共通平台 | IM | 與 ZeroClaw 重疊 |
| 9 | Google Chat | Workspace | - |
| 10 | BlueBubbles | iMessage | - |
| 11 | IRC | Legacy | - |
| 12 | Microsoft Teams | Workspace | - |
| 13 | LINE | IM | 亞洲 |
| 14 | Nextcloud Talk | 協作 | - |
| 15 | Nostr | 社群 | - |
| 16 | Twitch | 直播 | - |
| 17 | Zalo | IM | 越南 |
| 18 | WeChat | IM | - |
| 19 | macOS App | 原生 App | 功能最完整 |
| 20 | iOS Node | 原生 App | Canvas + Voice Wake |
| 21 | Android Node | 原生 App | Voice Wake |
| 22+ | 其他 plugin 平台 | 擴充 | - |

### 平台對比總結

| 維度 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **平台總數** | **28 個** | ~25+ 個 |
| **原生 IM 數量最多** | ✅ WhatsApp/Signal/iMessage/Telegram/Slack/Discord | ✅ WhatsApp/Telegram/Signal/iMessage/Slack/Discord |
| **中國平台** | ✅ **釘釘/飛書/QQ/企業微信** | ❌ WeChat 僅透過 plugin |
| **IoT 協議** | ✅ **MQTT** | ❌ |
| **原生 App** | ❌ | ✅ **macOS / iOS / Android** |
| **社群平台** | ✅ Bluesky/Nostr/Reddit/LinkedIn/X | ✅ Nostr/Twitch/LINE/Zalo |
| **商務 IM** | ✅ WATI/Mochat/Linq | 較少 |
| **協作平台** | ✅ Nextcloud Talk/Notion/Mattermost | ✅ Google Chat/Teams/Nextcloud Talk |

---

## 4️⃣ 核心功能

### ZeroClaw 核心功能

#### Hands（多代理編排）
- 類似 OpenClaw 的 ACP 但更輕量，Rust native 實現
- 無需 IPC overhead，同程序內多 agent 協調
- 支援 agent 間事件傳遞、任務委派、結果聚合

#### SOPs（事件驅動流程）
- Standard Operating Procedures，聲明式定義自動化流程
- 事件觸發 → 條件評估 → 動作執行
- 適合監控告警、定期任務、IoT 自動化

#### Web Dashboard（React 19）
- 內建 Web UI，可於 localhost 遠端存取
- React 19 + Vite + Tailwind CSS
- 監控 agent 狀態、hands 運行、SOP 觸發歷史

#### 硬體集成（ESP32/STM32/Arduino/RPi）
- **ZeroClaw 最大特色**：直接連接嵌入式設備
- ESP32：Wi-Fi/BLE IoT 節點
- STM32：工業級 MCU
- Arduino：教育/原型
- Raspberry Pi：邊緣運算
- 透過 MQTT、串列埠、GPIO 控制硬體

#### OpenClaw 自動遷移
- 內建 `--migrate-from-openclaw` flag
- 自動轉換 channel 配置、auth credentials、SOPs
- 降低從 OpenClaw 遷移門檻

#### E-stop（緊急停止）
- 硬體/軟體雙重 E-stop 按鈕
- Kubernetes 式的 kill-switch，立即終止所有 agent 與硬體操作
- 適合工業、機器人高風險場景

#### 沙盒（Landlock/Bubblewrap）
- **Landlock**（Linux kernel >= 5.13）：eBPF-free LSM 檔案系統沙盒
- **Bubblewrap**：使用者命名空間容器，無需 root
- 比 Docker 更輕量，適合嵌入式設備

### OpenClaw 對應功能

| 功能 | ZeroClaw | OpenClaw | 備註 |
|------|----------|----------|------|
| 多 Agent | Hands (Rust native, 同程序) | ACP + ACPX Harness (JSON-RPC IPC) | ZeroClaw 更低延遲 |
| 事件驅動 | SOPs (聲明式) | CronService + Heartbeat | ZeroClaw 更適合 IoT 自動化 |
| Web Dashboard | ✅ React 19 | ✅ Control UI (localhost:18789) + WebChat | 同級 |
| 硬體集成 | ✅ **ESP32/STM32/Arduino/RPi** | ⚡ macOS/iOS/Android 原生 App | 完全不同的方向 |
| OpenClaw 遷移 | ✅ `--migrate-from-openclaw` | N/A | ZeroClaw 主動相容 |
| E-stop | ✅ 硬體/軟體雙重 | ❌ | ZeroClaw 獨有 |
| 沙盒 | Landlock + Bubblewrap (Linux, ~10MB) | Docker + seccomp + AppArmor | ZeroClaw 更輕，OpenClaw 更成熟 |
| 隧道 | Cloudflare/Tailscale/ngrok | 不適用 | ZeroClaw 可從 NAT 後直接暴露 |

---

## 5️⃣ 技術棧與部署

### 技術棧對比

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **語言** | Rust 2024 Edition | TypeScript / Node.js |
| **前端** | React 19 + Vite + Tailwind CSS | 內建 Control UI |
| **安裝方式** | `cargo install` / `brew install` / Scoop / AUR / crates.io | `npm install -g openclaw` |
| **隧道** | Cloudflare Tunnel / Tailscale / ngrok | 不內建 |
| **容器** | 原生靜態二進制（非容器） | Docker (sandbox mode) |
| **打包分發** | Homebrew + Scoop + AUR + crates.io | npm registry |
| **編譯依賴** | Rust toolchain (rustc + cargo) | Node.js >= 18 |
| **Cross-compile** | ✅ Rust target: aarch64/x86_64/armv7/ESP-IDF | ❌ Node.js 需對應架構 |
| **WebAssembly** | ✅ Rust → WASM 可嵌入瀏覽器 | ❌ |

### 部署場景

```
                    資源需求 ◄────────────────────► 功能豐富

邊緣/IoT ████████████████████████████████████████████████
  ESP32   │  ZeroClaw ✅ (<5MB RAM)  │  OpenClaw ❌ (無完整 Node)
  RPi Zero│  ZeroClaw ✅ (~8MB RAM)  │  OpenClaw ⚠️ (吃力)
  RPi 4   │  ZeroClaw ✅ (極佳)     │  OpenClaw ✅ (舒適)
  RPi 5   │  ZeroClaw ✅ (殺雞用牛刀)│  OpenClaw ✅ (流暢)
  x86 VPS │  ZeroClaw ✅ (<10MB RAM) │  OpenClaw ✅ (128MB+)
  開發機  │  ZeroClaw ✅            │  OpenClaw ✅ (最佳體驗)
```

---

## 6️⃣ API 與 LLM 整合

### ZeroClaw
- 透過 SOPs 和 Hands 呼叫外部 LLM API
- 可配置任意 OpenAI-compatible endpoint
- 無內建 provider routing（Rust 保持核心精簡）
- 適合搭配 lightweight local model (via ollama, llama.cpp)

### OpenClaw
- **Model catalog + allowlist**：精細的模型管理
- **Auth profiles per-agent**：每個 agent 獨立憑證
- **Fallback chains**：primary → fallback 自動容錯
- **OAuth 支援**：OpenAI Codex, Anthropic
- **Provider normalization**：lowercase + alias

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **多 Provider 切換** | ⚡ 需手動配置 | ✅ 動態 `/model` 指令 |
| **Fallback 鏈** | ⚡ 需 SOPs 實作 | ✅ 內建 |
| **OAuth** | ❌ | ✅ |
| **Model catalog** | ❌ | ✅ |
| **API 認證** | API key / env var | Auth profiles + SecretRef |
| **Provider routing** | ❌ | ✅ Smart routing + aliases |

---

## 7️⃣ 安全與沙盒

### ZeroClaw

- **Landlock**（Linux kernel >= 5.13）：檔案系統路徑白名單，eBPF-free
- **Bubblewrap**：使用者命名空間，無權限提升
- **E-stop**：緊急停止按鈕（軟硬體雙重）
- **單程序安全**：Rust 天然記憶體安全，無需 GC
- **最小攻擊面**：8.8MB vs Node.js 數百個 deps

### OpenClaw

- **Docker**：主要沙盒，含 seccomp/AppArmor profile
- **Zod schema 驗證**：未知 config key 直接拒絕
- **Tool Policy**：allow/deny/profile/byProvider
- **Tool Result Guard**：驗證工具輸出形狀
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `fix.ts`
- **Capability Model**：定義 channel/agent 操作權限

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **沙盒類型** | Landlock + Bubblewrap (輕量) | Docker + seccomp/AppArmor (成熟) |
| **記憶體安全語言** | ✅ Rust | ❌ TypeScript |
| **最小攻擊面** | ✅ 8.8MB, 零外部依賴 | ❌ node_modules 數百套件 |
| **E-stop** | ✅ 硬體+軟體 | ❌ |
| **Tool Policy** | ⚡ 透過 SOPs 控制 | ✅ allow/deny/profile/byProvider |
| **Config 驗證** | ⚡ Rust struct 強型別 | ✅ Zod (未知 key 拒絕) |
| **Security Audit** | ⚡ 內建 | ✅ audit.ts 全套 |

---

## 8️⃣ 社群與維護

| 項目 | ZeroClaw | OpenClaw |
|------|----------|----------|
| **開發者** | zeroclaw-labs (開源組織) | 開源社群 |
| **Stars** | ⭐ 29,342 | ⭐ 35,000+ |
| **Forks** | 🍴 4,168 | 🍴 10,000+ |
| **Open Issues** | 176 | ~500+ |
| **DeepWiki** | 13 章 | 多章節 |
| **License** | Apache-2.0 / MIT | Apache-2.0 |
| **安裝套件數** | **4 個** (Homebrew/Scoop/AUR/crates.io) | 1 個 (npm) |

---

## 9️⃣ 架構與流程圖解

### A. 核心流程對比

**ZeroClaw: 事件驅動單體**
```
Event (Msg/SOP/Hardware/GPIO)
   │
   ▼
[ZeroClaw Binary]
   ├── Channel Handler (Rust async)
   ├── SOP Engine (Event → Condition → Action)
   ├── Hands Orchestrator (Multi-Agent)
   │    ├── Sub-agent 1 (isolated)
   │    ├── Sub-agent 2 (isolated)
   │    └── Result Aggregator
   ├── Hardware Bridge (ESP32/STM32/Pi)
   └── Landlock Sandbox
```

**OpenClaw: 分散式 Gateway + Agent**
```
Inbound Message (WhatsApp/Telegram/Discord)
   │
   ▼
[Auth Layer → Routing Engine (Bindings)]
   │
   ▼
[Per-Agent Pi Runtime]
   ├── Context Bootstrap (AGENTS.md/SOUL.md)
   ├── LLM Call (Primary → Fallback)
   ├── Tool Policy Check → Sandbox (Docker)
   ├── ACP: sessions_spawn / ACPX: external CLI
   └── Result Guard → Reply
```

### B. 安全流程對比

**ZeroClaw Defense-in-Depth**
```
Command → Rust Type Check → Landlock Path Filter 
   → Bubblewrap Sandbox → Execute
   ↳ E-stop at any level → Immediate Halt
```

**OpenClaw Defense-in-Depth**
```
DM Policy → Tool Policy (allow/deny) → Docker Sandbox 
   → seccomp/AppArmor → SSRF Check → Result Guard
   ↳ Zod Schema: Reject unknown config keys at startup
```

---

## 🏛️ 10 總結對比

| 維度 | ZeroClaw 優勢 | OpenClaw 優勢 |
|------|---------------|---------------|
| **效能** | **<5MB RAM, <10ms 冷啟動, 8.8MB binary** | 功能完整但資源密集 |
| **語言** | **Rust 2024 — 記憶體安全, 無 GC** | TypeScript — 廣泛社群支援 |
| **平台數量** | **28 個（最多！）** | ~25+ 個 |
| **中國市場** | **釘釘/飛書/QQ/企業微信** | WeChat plugin |
| **IoT/嵌入式** | **ESP32/STM32/Arduino/RPi 直連** | ❌ 無法跑在嵌入式 |
| **硬體控制** | **GPIO/MQTT/Serial 原生支援** | ❌ |
| **部署** | **單一靜態二進制, 4 種套件管理器** | npm install + 大量依賴 |
| **E-stop** | ✅ 硬體+軟體雙重 | ❌ |
| **沙盒** | ✅ Landlock + Bubblewrap (輕量) | ✅ Docker + seccomp/AppArmor (成熟) |
| **多 Agent** | Hands (Rust native, 同程序) | ACP + ACPX Harness (靈活) |
| **原生 App** | ❌ | ✅ **macOS/iOS/Android** |
| **Voice** | ❌ | ✅ Voice Wake + Talk Mode |
| **Live Canvas** | ❌ | ✅ A2UI |
| **Provider 管理** | ⚡ 簡易 | ✅ Catalog/Fallback/OAuth |
| **Web UI** | ✅ React 19 | ✅ Control UI |
| **從 OpenClaw 遷移** | ✅ **自動遷移工具** | N/A |

---

## 🎯 選哪個？

| 需求 | 推薦 | 理由 |
|------|------|------|
| IoT / 嵌入式 / ESP32 | ✅ **ZeroClaw** | 唯一能在 <10MB RAM 設備上跑的 agent |
| <10ms 冷啟動需求 | ✅ **ZeroClaw** | 無需 JIT warmup |
| 最小資源佔用 | ✅ **ZeroClaw** | 5MB RAM vs OpenClaw 的 100MB+ |
| 中國企業通訊 (釘釘/飛書) | ✅ **ZeroClaw** | 原生支援 |
| MQTT / 工業物聯網 | ✅ **ZeroClaw** | 原生協議 |
| E-stop 緊急停止 | ✅ **ZeroClaw** | 工業安全需求 |
| macOS/iOS/Android 原生 App | ✅ **OpenClaw** | ZeroClaw 無此功能 |
| Voice Wake / Talk Mode | ✅ **OpenClaw** | ZeroClaw 不支援語音 |
| Live Canvas / A2UI | ✅ **OpenClaw** | ZeroClaw 不支援 |
| 多 AI CLI 委派 (ACPX) | ✅ **OpenClaw** | Claud Code/Codex/Gemini CLI 委派 |
| 最簡單的安裝體驗 | ✅ **ZeroClaw** | 一個二進制搞定 |
| 從 OpenClaw 遷移 | ✅ **ZeroClaw** | 內建自動遷移工具 |
| 開發者體驗 (TS 生態) | ✅ **OpenClaw** | 豐富的 Type/Plugin 系統 |
