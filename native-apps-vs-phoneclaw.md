# 📱 OpenClaw 原生 App vs PhoneClaw — 行動裝置能力深度比較

> **版本**：v1.0 | **日期**：2026-04-04  
> **用途**：詳細比較 OpenClaw 原生 iOS/macOS/Android App 與 PhoneClaw 能力差異  
> **資料來源**：DeepWiki 章節 6.2–6.4, GitHub READMEs, Mintlify 文檔

---

## 📑 目錄

- [1. 🍎 OpenClaw 原生 iOS / macOS App](#1--openclaw-原生-ios--macos-app)
- [2. 🤖 OpenClaw 原生 Android App](#2--openclaw-原生-android-app)
- [3. 📱 PhoneClaw (Android APK)](#3--phoneclaw-android-apk)
- [4. ⚔️ 核心差異對比](#4--核心差異對比)
- [5. 🎯 選哪個？場景決策](#5--選哪個場景決策)

---

## 1. 🍎 OpenClaw 原生 iOS / macOS App

**定位**：Gateway 的**原生設備節點 **(Device Node)。不是一個獨立的 Agent，而是 Gateway 延伸到手機/電腦的**感官與四肢**。

### 核心能力矩陣

| 能力 | 詳細說明 | 技術實現 |
|:---|:---|:---|
| **🎙️ Voice Wake **(語音喚醒) | iOS 常駐背景聽取 "Claw" 喚醒詞。 | `AVAudioEngine` + **Swabbie** 框架處理音訊。 |
| **🗣️ Talk Mode **(雙向語音對話) | 類似 Siri 的連續對話模式，支援推遲/連續/閒置三模式。 | `SFSpeechRecognizer` (STT) + `AVAudioPlayer` / `AVSampleBufferAudioRenderer` (TTS)。 |
| **📷 Camera **(相機控制) | Agent 可遠端指令手機「拍一張照」或「錄一段影片」。 | `CameraManager` 原生接口。 |
| **📍 Location **(位置資訊) | Agent 可查詢設備當前位置，支援情境感知 (如「我在哪？」)。 | `LocationManager` (iOS CoreLocation)。 |
| **🔔 Notifications **(通知) | Agent 可讀取/操作設備通知，也可推送通知。 | iOS `UserNotifications`。 |
| **🌐 A2UI Canvas **(渲染 UI) | **最強功能**：Agent 傳送指令後，App 會在 WebView 中渲染出**交互式 UI** (按鈕、圖表、表單)。 | `WKWebView` + A2UI 開放標準。支援 URL 跳轉。 |
| **📡 Gateway 探索與自動連接** | 在同一 Wi-Fi 下自動發現 Gateway，透過 **Bonjour/mDNS** 自動配對。 | TLS 憑證釘選 (TOFU)。 |
| **📱 Share Extension** | 可以從其他 App (如 Safari) 直接分享文字、URL、圖片給 Agent 處理。 | iOS Share Extension。 |
| **🏃 Activity Widget **(Live Activities) | 在鎖屏畫面或 Dynamic Island 顯示 Agent 任務進度（如「正在搜尋...」）。 | iOS `ActivityKit`。 |
| **⌚ watchOS 整合** | Apple Watch 可直接發送語音指令，由 iPhone 中繼轉發。 | `WatchConnectivity`。 |
| **🖥️ macOS 選單列整合** | 在 macOS 選單列顯示，提供 Gateway 設定與 Chat 視窗。 | Sparkle 框架自動更新。 |

### 架構邏輯

```
Agent (在伺服器上) 
    │
    ▼
[Device Node Protocol (WebSocket RPC)]
    │
    ├── Node Gateway (接收 AI 指令)
    ├── Operator Data (上傳音訊/影像)
    │
    ▼
iOS App (感官與四肢)
    ├── 相機 (拍一張照片傳給 Agent 看)
    ├── 麥克風 (聽覺 + 語音喚醒)
    ├── 螢幕 (渲染 Canvas UI)
    └── GPS (告訴 Agent 你在哪)
```

> **💡 總結**：iOS App 是一個「超級遙控器」+「感官擴展」。它讓在遠端執行的 Agent 能透過你的 iPhone 看到世界、聽到聲音、甚至在你手機上顯示操作介面。

---

## 2. 🤖 OpenClaw 原生 Android App

**定位**：與 iOS Node 相同，以 Device Node 的形式擴展 Android 能力。

### 能力對照表

| 能力 | 說明 | 對比 iOS |
|:---|:---|:---|
| **Voice Wake** | ✅ 支援 | 同 iOS |
| **Talk Mode** | ✅ 支援 | 同 iOS |
| **Camera / Screen** | ✅ 支援 | 同 iOS |
| **Location** | ✅ 支援 | 同 iOS |
| **Notifications** | ✅ 支援 (Android 通知欄) | 比 iOS 更開放 |
| **System Run **(Shell)| ✅ **Android 特有優勢** | **可直接在手機上執行 Shell 指令** |
| **App 側載** | 透過 APK 安裝 | - |

> **💡 差異點**：OpenClaw 的 Android App 在 **Device Node Protocol** 上比 iOS 少了一點隱私限制，因此 Agent 可以更自由地在手機上執行系統級指令。

---

## 3. 📱 PhoneClaw (Android APK)

**定位**：一個專注於 **Android 手機完全自動化** 的 OpenClaw 分支。  
**語言**：Kotlin | **Stars**：433 | **建立**：2026-02-10

### 核心能力

| 能力 | 說明 |
|:---|:---|
| **❌ 無需 Root** | 這是最大賣點：完全不需要 Root 即可全自動化操作手機。 |
| **🤖 全 App 控制** | 能自動操作 WhatsApp, YouTube, 瀏覽器, 相簿等任何已安裝 App。 |
| **📝 ClawScripts** | 專有腳本系統（如 `youtube_clawscript.txt`），定義自動化操作步驟。 |
| **📥 APK 側載** | 直接安裝 APK 作為獨立 App 運行。 |
| **🌐 ACP 整合** | 基於 OpenClaw 上遊，具備 AI Agent 推理能力。 |

### 實現原理

它利用 Android 的 **Accessibility Services (無障礙服務)** 接口，在背景模擬點擊、滑動與捲動，從而操作其他 App。

```
PhoneClaw Workflow
══════════════════

用戶指令: "幫我發一個 WhatsApp 訊息給媽媽"
   │
   ▼
AI Agent 解析意圖
   │
   ▼
觸發 Clawscript: "Open WhatsApp"
   │ (模擬點擊主畫面圖示)
   ▼
Clawscript: "Search 'Mom'"
   │ (模擬點擊搜尋欄 + 輸入文字)
   ▼
Clawscript: "Type 'Hello!'" & "Send"
   │ (模擬輸入與發送)
   ▼
任務完成
```

---

## 4. ⚔️ 核心差異對比：OpenClaw Node vs PhoneClaw

這是最常被混淆的兩個概念。簡單來說：**一個是「感官延伸」，一個是「雙手替代」**。

| 特性 | **OpenClaw 原生 App **(Node) | **PhoneClaw **(APK 側載版) |
|:---|:---|:---|
| **定位** | **眼睛、耳朵、GPS** | **雙手、手指 **(模擬點擊) |
| **能否控制其他 App** | ❌ 不行 (受 OS 沙盒限制) | ✅ **可以** (透過無障礙服務模擬操作) |
| **能否拍照/錄影** | ✅ **可以** (原生 API 直接存取) | ⚠️ 只能間接操作相機 App |
| **Voice Wake** | ✅ **有** (內建喚醒引擎) | ❌ 無 |
| **Talk Mode** | ✅ **有** (流暢雙向語音) | ❌ 無 |
| **Location** | ✅ **有** (原生 GPS) | ❌ 通常依賴 IP 或其他 App |
| **A2UI Canvas** | ✅ **有** (渲染網頁 UI) | ❌ 無 (純腳本操作) |
| **Root 需求** | ❌ 不需要 | ❌ 不需要 |
| **技術原理** | 官方 Device Node 協議 | 模擬使用者操作 (UI Automation) |
| **穩定性** | ⭐⭐⭐⭐⭐ (原生 API) | ⭐⭐ (依賴螢幕解析度與 App UI 結構) |
| **適用場景** | 情境感知 (Location)、語音助理、通知整合、拍照。 | **自動化 repetitive tasks **(發訊息、刷影片、排程)。 |

### 🧐 為什麼 OpenClaw Node 不直接做 PhoneClaw 的功能？

這是一個**平台限制**問題。
- **Android** 的 `Accessibility Services` 非常強大，但也需要使用者在系統設定中手動授權。OpenClaw 的核心 Node App 定位是**安全**的硬體橋接，過度依賴模擬操作可能會引發 Google 的審核問題。
- **PhoneClaw** 作為一個獨立分支，選擇專注於這個利基市場 (Niche Market)，專門解決「不想寫複雜腳本又想自動操控手機」的需求。

---

## 5. 🎯 選哪個？場景決策

| 你的需求 | 推薦方案 | 理由 |
|:---|:---|:---|
| 👁️ **我要 Agent 能「看到」我的世界** | ✅ OpenClaw iOS/Android App | 原生相機、位置、通知整合，穩定性最高。 |
| 🎙️ **我要語音喚醒 **(Hey Claw...) | ✅ OpenClaw iOS/Android App | 內建 Voice Wake 與 Talk Mode，體驗如 Siri。 |
| 🤖 **我要 Agent 幫我「操作」WhatsApp 發訊息給某人** | ✅ **PhoneClaw** | OpenClaw Node 受限於沙盒無法跨 App 點擊，PhoneClaw 是唯一解法。 |
| 📅 **我要自動排程每天看 10 個短影片** | ✅ **PhoneClaw** | ClawScripts 專為此類重複性 UI 任務設計。 |
| 🌐 **我要 Agent 幫我訂餐** (操作外送 App) | ✅ **PhoneClaw** | 只有它可以模擬點擊外送 App 的 UI 流程。 |
| 📱 **我要一個「完美搭配」OpenClaw Gateway 的遙控器** | ✅ OpenClaw Node App | 最完美的配對體驗，支持 Gateway Discovery 與 TOFU TLS。 |
| ⌚ **我要用 Apple Watch 下指令** | ✅ OpenClaw iOS App | 唯一支援 watchOS 的方案。 |

### 🤝 最佳組合

如果你同時擁有 Android 手機並安裝了 OpenClaw Gateway，你可以**兩者並存**：

1.  **安裝 OpenClaw Node App**：作為你的「主面板」，接收通知、使用語音喚醒、查看 Canvas UI。
2.  **安裝 PhoneClaw**(側載)：作為你的「執行引擎」。
    - 當你需要執行複雜手機操作時，Agent 透過 ACP 呼叫 PhoneClaw 來點擊螢幕。
    - 當你只是需要問 Agent 問題或接收提醒時，使用 Node App。

---

> **📄 報告版本**：v1.0 | **整理日期**：2026-04-04  
> **基於資料**：DeepWiki 6.2, 6.3, 6.4 章節 + PhoneClaw GitHub READMEs。
