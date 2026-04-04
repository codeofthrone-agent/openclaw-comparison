# PicoClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本：PicoClaw (sipeed/picoclaw), OpenClaw v2026.3.23+

---

## 1️⃣ 核心定位

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **Repo** | `sipeed/picoclaw` | `openclaw/openclaw` |
| **設計理念** | **$10 硬體 · <10MB RAM · ms 級啟動** — 專為 IoT/嵌入式設計 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **開發者** | **Sipeed** (嵌入式/AIoT 知名公司) | 開源社群 |
| **核心差異** | Go 實作、極小資源預算、邊緣計算原生、Docker 支援 | Node.js 生態、Gateway 架構、25+ 平台、原生 App |
| **語言** | Go | TypeScript / Node.js |
| **Stars** | ⭐ 27,275 | ⭐ 35,000+ (參考) |
| **Forks** | 🍴 3,831 | 🍴 10,000+ |
| **License** | MIT | Apache-2.0 |
| **Open Issues** | 231 | ~500+ |
| **Open PRs** | 113 | ~200+ |
| **二進制** | Go 編譯單一執行檔 + Docker 支援 | npm 套件 + node_modules |

### 定位圖譜

```
              嵌入式/IoT 優先 ◄──────────────────► 通用/雲端優先

PicoClaw ███████████████████████████████░░░  (ESP32 / RPi / $10 HW)
OpenClaw  ░░░░░░░░░░░░░░██████████████████  (Desktop / Server / Cloud)

              資源極簡 ◄─────────────────────► 功能豐富

PicoClaw █████████████████████░░░░░░░░░░░░░  (<10MB RAM, Docker OK)
OpenClaw  ░░░░░░░░░░░░░░░░░░░░███░░░░░░░░░  (需要完整 Node.js stack)

              硬體生態鏈 ◄──────────────────► 軟體生態鏈

PicoClaw ███████████████████████████████░░░  (Sipeed 硬體 + MAiX)
OpenClaw  ░░░░░░░░░░░░░░░░░░░░░░░░░██████  (npm + Plugin + Native App)
```

---

## 2️⃣ 開發者背景

### PicoClaw — Sipeed

**Sipeed** 是知名的嵌入式硬體/AIoT 公司：
- **MAiX** 系列：RISC-V AI 開發板 (ESP32-C3, K210, V853)
- **Longan** 系列：RISC-V MCU (GD32VF103)
- **Tang** 系列：FPGA 開發板 (GW1N)
- 活躍於 Maker/開源硬體社群
- 擅長將 AI 推理部署到邊緣設備

**開發者優勢**：
- 有硬體製造能力，可提供 $10 開發板搭配 PicoClaw
- 熟悉嵌入式 Linux、RISC-V、AI edge inference
- 社群在開源硬體領域活躍

### OpenClaw — 社群驅動
- 純軟體開源專案
- npm 生態系統
- Plugin 生態（ClawHub + npm）

| 項目 | PicoClaw (Sipeed) | OpenClaw |
|------|-------------------|----------|
| **硬體能力** | ✅ 自行設計/製造開發板 | ❌ 純軟體 |
| **AI Edge 經驗** | ✅ RISC-V / K210 / ESP32-S3 | ❌ |
| **開源社群** | ✅ Maker/Arduino 社群 | ✅ 軟體開發者社群 |
| **商業支援** | ✅ Sipeed 公司背後 | ❌ 社群 |
| **$10 方案** | ✅ 搭配自家硬體 | ❌ |

---

## 3️⃣ 架構比較

### PicoClaw 架構

```
┌─────────────────────────────────────────────────────────┐
│                   PicoClaw (Go Binary)                    │
│                                                         │
│  ┌──────────────┐  ┌────────────┐  ┌──────────────────┐ │
│  │ Input        │  │ Go Agent   │  │ Output           │ │
│  │ Channels     │→ │ Core       │→ │ Responses        │ │
│  │ (IoT/MQTT/   │  │ (goroutine │  │ (IoT/Message/    │ │
│  │  Serial)     │  │  based)    │  │  Hardware)       │ │
│  └──────────────┘  └────────────┘  └──────────────────┘ │
│        │                │                   │            │
│        ▼                ▼                   ▼            │
│  ┌──────────────┐  ┌────────────┐  ┌──────────────────┐ │
│  │ Hardware     │  │ LLM Bridge │  │ Docker Support   │ │
│  │ ESP32/Pi/    │  │ (OpenAI-   │  │ (容器化邊緣部署)  │ │
│  │ Sipeed 設備  │  │ compatible)│  │                  │ │
│  └──────────────┘  └────────────┘  └──────────────────┘ │
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

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **核心語言** | Go | TypeScript / Node.js |
| **編譯方式** | Go 編譯單一執行檔 (靜態連結) | npm 套件 + node_modules |
| **記憶體佔用** | **<10MB RAM** | V8 堆通常 100MB+ |
| **冷啟動** | **ms 級**（Go 啟動快，無需 JIT） | 數百 ms ~ 秒級 |
| **並行模型** | Go goroutines (輕量級線程) | Node.js event loop + workers |
| **GC 影響** | Go GC (stw pauses，但極短) | V8 GC (增量+並行，但停頓更多) |
| **部署方式** | 二進制 / Docker | npm install |
| **Docker 支援** | ✅ 原生支援（邊緣容器部署） | ✅ Docker sandbox mode |
| **硬體支援** | ✅ ESP32 / RPi / Sipeed 設備 | ❌ 無 |

---

## 4️⃣ 通訊平台

### PicoClaw
- 主要聚焦 **IoT/IoE 通訊**：
  - MQTT（物聯網標准）
  - Serial / UART（串列通訊）
  - GPIO 控制
  - WebSocket
  - 可能透過 Go 套件擴展到常見 IM

### OpenClaw
- 25+ 通訊平台（WhatsApp/Telegram/Discord/Signal 等）
- 原生 App（macOS/iOS/Android）

| 維度 | PicoClaw | OpenClaw |
|------|----------|----------|
| **通訊平台數量** | 較少，IoT 為主 | **25+ 個** |
| **IoT 協議** | ✅ MQTT/Serial/GPIO | ❌ |
| **即時 IM** | ⚡ 需擴展 | ✅ WhatsApp/Telegram/Signal/Discord |
| **社群平台** | ❌ | ✅ Nostr/Reddit/X/Bluesky |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |

---

## 5️⃣ 技術棧與部署

### 技術棧對比

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **語言** | Go | TypeScript / Node.js |
| **Runtime** | 無（編譯為原生二進制） | Node.js >= 18 |
| **Docker** | ✅ 支援（邊緣容器） | ✅ Sandbox mode |
| **Cross-compile** | ✅ `GOOS/GOARCH` 原生支援 | ❌ 需對應 Node.js |
| **目標架構** | arm64 / amd64 / armv7 / **RISC-V** | amd64 / arm64 |
| **安裝難度** | `go install` / 下載二進制 | `npm install -g openclaw` |
| **依賴大小** | Go 標準庫 + 少量套件 | npm 套件 + node_modules (數百 MB) |

### 部署場景

```
                    資源需求 ◄────────────────────► 功能豐富

邊緣設備 ██████████████████████████████████████████████
  ESP32   │  PicoClaw ✅/⚠️ (Go 太胖?) │  OpenClaw ❌
  RPi Zero│  PicoClaw ✅ (<10MB RAM)     │  OpenClaw ⚠️
  RPi 4   │  PicoClaw ✅ (完美)          │  OpenClaw ✅
  RPi 5   │  PicoClaw ✅                │  OpenClaw ✅ (最佳)
  x86 VPS │  PicoClaw ✅ (<20MB RAM)     │  OpenClaw ✅
  開發機  │  PicoClaw ✅                │  OpenClaw ✅ (最佳體驗)
  Sipeed  │  PicoClaw ✅ (官方適配)      │  OpenClaw ❌
  (MAiX)  │                              │
```

> ⚠️ **ESP32 注意**：Go 無法直接編譯到 ESP32（無標準庫），需 TinyGo 或 CGO。PicoClaw 可能在 ESP32 上需要特殊處理。

---

## 6️⃣ API 與 LLM 整合

### PicoClaw
- Go 原生 HTTP client 呼叫 LLM API
- 支援 OpenAI-compatible 端點
- 適合搭配本地輕量模型 (ollama, llama.cpp)
- Go 的 `net/http` 比 Node.js 的 fetch 更適合低資源環境

### OpenClaw
- Model catalog + allowlist
- Auth profiles per-agent
- Fallback chains（auto-failover）
- OAuth 支援

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **LLM 呼叫** | Go HTTP Client (低資源友善) | Node.js fetch |
| **多 Provider** | ⚡ 需手動配置 | ✅ 動態切換 |
| **Fallback** | ❌ 需自訂 | ✅ 內建 |
| **OAuth** | ❌ | ✅ |
| **Provider 管理** | ⚡ 簡單 | ✅ Catalog + Allowlist |
| **適合本地 Model** | ✅ **Go 低開銷更適合** | ⚡ V8 overhead 較大 |

---

## 7️⃣ 安全與沙盒

### PicoClaw
- **Docker** 容器化部署（邊緣設備上可選）
- **Go 型別系統**：編譯時確保類型安全
- **低資源 = 小攻擊面**：Go 標準庫 vs Node.js 數百套件
- **嵌入式安全考量**：硬體級安全（TrustZone/Secure Boot）

### OpenClaw
- **Docker + seccomp + AppArmor**
- **Zod schema 驗證**：未知 config key 直接拒絕
- **Tool Policy**：allow/deny/profile/byProvider
- **Tool Result Guard**：驗證工具輸出形狀
- **Security Audit**：全套審計工具

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **沙盒後端** | ✅ Docker (可選) | ✅ Docker + seccomp/AppArmor |
| **型別安全** | ✅ Go 編譯時檢查 | ✅ TypeScript/Node.js |
| **攻擊面** | ✅ 小（標準庫少） | ❌ npm 套件多 |
| **Config 驗證** | ⚡ Go struct tags | ✅ Zod (完整驗證) |
| **Security Audit** | ❌ | ✅ 全套 |
| **硬體安全** | ✅ TrustZone/Secure Boot | ❌ |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |

---

## 8️⃣ 社群與維護

| 項目 | PicoClaw | OpenClaw |
|------|----------|----------|
| **開發者** | **Sipeed** (嵌入式公司) | 開源社群 |
| **Stars** | ⭐ 27,275 | ⭐ 35,000+ |
| **Forks** | 🍴 3,831 | 🍴 10,000+ |
| **Open Issues** | 231 | ~500+ |
| **Open PRs** | **113** (活躍！) | ~200+ |
| **License** | MIT | Apache-2.0 |
| **社群特點** | 開源硬體/Maker/AIoT | 軟體開發/Automation |
| **商業支援** | ✅ Sipeed 公司 | ❌ 純社群 |
| **PR/Issue 比** | 113/231 = 49% | 200/500 = 40% (參考) |

> 💡 **PicoClaw 的 113 個 Open PRs 顯示社群非常活躍**，Sipeed 團隊積極整合社群貢獻。

---

## 9️⃣ 架構與流程圖解

### A. 核心流程對比

**PicoClaw: 嵌入式代理**
```
Sensor Input (GPIO/Serial/MQTT)
   │
   ▼
[PicoClaw Go Binary]
   ├── Event Loop (goroutine based)
   ├── Hardware Bridge (ESP32/Pi/Serial)
   ├── LLM Bridge (OpenAI-compatible API)
   └── Action Output (GPIO/MQTT/Message)
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
   └── Result Guard → Reply
```

### B. 安全流程對比

**PicoClaw Security**
```
Input → Go Type Validation → Docker Sandbox (if used)
   → Hardware Control (GPIO restrictions)
   → LLM API Call (authenticated) → Output
```

**OpenClaw Defense-in-Depth**
```
DM Policy → Tool Policy (allow/deny) → Docker Sandbox 
   → seccomp/AppArmor → SSRF Check → Result Guard
   ↳ Zod Schema: Reject unknown config keys at startup
```

---

## 🏛️ 10 總結對比

| 維度 | PicoClaw 優勢 | OpenClaw 優勢 |
|------|---------------|---------------|
| **開發者背景** | **Sipeed — 嵌入式/AIoT 專家** | 純軟體社群 |
| **資源佔用** | **<10MB RAM** | V8 堆通常 100MB+ |
| **硬體整合** | **ESP32/Pi/Sipeed MAiX 原生** | ❌ 無 |
| **IoT 協議** | **MQTT/Serial/GPIO** | ❌ |
| **Docker 邊緣部署** | ✅ 輕量容器 | ✅ 但較重 |
| **Cross-compile** | ✅ Go 原生，**含 RISC-V** | ❌ |
| **並行** | ✅ Goroutines (輕量) | ⚡ Event loop |
| **通訊平台** | ❌ IoT 為主 | **25+ 個 IM + 社群** |
| **原生 App** | ❌ | **✅ macOS/iOS/Android** |
| **Plugin 系統** | ❌ | ✅ ClawHub + npm |
| **多 Agent** | ❌ | **✅ ACP + ACPX Harness** |
| **Voice/Camera** | ❌ | **✅ Voice Wake + Canvas** |
| **安全審計** | ❌ | ✅ 全套 (audit.ts) |
| **Config 驗證** | ⚡ Go struct | ✅ Zod (嚴格) |
| **價格方案** | **$10 硬體 + 免費軟體** | 免費軟體 |

---

## 🎯 選哪個？

| 需求 | 推薦 | 理由 |
|------|------|------|
| 物聯網監控 (溫濕度/感測器) | ✅ **PicoClaw** | 原生 GPIO/MQTT 支援 |
| ESP32 / RISC-V 邊緣設備 | ✅ **PicoClaw** | Sipeed 官方適配 |
| $10 硬體方案 | ✅ **PicoClaw** | Sipeed 開發板 |
| <10MB RAM 設備 | ✅ **PicoClaw** | Go 編譯最小化 |
| 智慧家庭 IoT 中控 | ✅ **PicoClaw** | 硬體控制原生 |
| Docker 邊緣容器 | ✅ **PicoClaw** | Go 二進制更適合容器 |
| WhatsApp/Telegram/Discord | ✅ **OpenClaw** | 25+ 即時通訊 |
| macOS/iOS/Android App | ✅ **OpenClaw** | 原生應用程式 |
| 多 AI Agent 協作 | ✅ **OpenClaw** | ACP + ACPX |
| Voice Wake / 語音互動 | ✅ **OpenClaw** | 原生支援 |
| Live Canvas / A2UI | ✅ **OpenClaw** | 交互式 UI |
| 安全審計 | ✅ **OpenClaw** | 完整 audit 體系 |
| RISC-V / AI Edge | ✅ **PicoClaw** | Sipeed MAiX 系列 |
| 中國 AIoT 專案 | ✅ **PicoClaw** | Sipeed 在中國有資源 |
