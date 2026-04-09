# NemoClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-09 | 版本參考：NemoClaw (NVIDIA/NemoClaw), OpenClaw v2026.3.23+ | Repo: NVIDIA/NemoClaw ⭐ 18,810 | JS/TS | Apache-2.0

---

## 1️⃣ 核心定位

| 項目 | NemoClaw | OpenClaw |
|------|----------|----------|
| **開發者** | NVIDIA（企業級安全堆疊） | 開源社群 |
| **設計理念** | **在 NVIDIA OpenShell 內安全運行 OpenClaw**（安全隔離 + 受控推理） | **全能個人 AI 助理**（多平台通訊 + Plugin 生態） |
| **核心差異** | **安全優先**：OpenShell runtime + sandbox + 管理式推理 | **功能完整**：多 channel + 多 Agent + Workflows |
| **語言** | JavaScript / TypeScript | TypeScript (Node.js) |
| **Stars** | 18,810 ⭐ | 347K ⭐ |
| **Forks** | 2,266 | ~14,000+ |
| **License** | Apache-2.0 | GPL-3.0 |
| **狀態** | ⚠️ Alpha 預覽（2026/03/16 起） | ✅ 成熟穩定 |
| **主要使用場景** | **企業/安全敏感**環境、沙盒化 OpenClaw | 多平台通訊、開發自動化、個人/團隊助理 |

---

## 2️⃣ 架構比較

### NemoClaw 架構（重點）
- **OpenShell Runtime**：以 NVIDIA OpenShell 作為安全基座，提供更嚴格的隔離與權限控制。
- **Sandbox / Blueprint**：透過 Blueprint + policy 驅動啟動與行為限制。
- **Managed Inference**：導向受控推理路徑（安全與可追蹤性）。
- **Onboard 安裝**：提供 guided onboarding，創建沙盒與配置策略。

### OpenClaw 架構（重點）
- **Gateway + Agent Runtime 分離**：Gateway 控制面 + Pi embedded runner。
- **多 Agent Routing**：依 channel/account/peer 路由隔離 agent。
- **Plugin 生態**：channels / models / tools / skills / speech / image gen。
- **Device Node**：macOS/iOS/Android 作為本地行動節點。

---

## 3️⃣ 安全與治理

| 項目 | NemoClaw | OpenClaw |
|------|----------|----------|
| **安全定位** | **強安全預設**（OpenShell + policy） | 安全功能完整但偏自由化 |
| **沙盒隔離** | ✅（Sandbox Blueprint） | ✅（Docker / sandbox 可選） |
| **合規/審計** | ✅（企業導向） | ✅（安全審計與工具輸出防護） |
| **供應鏈風險** | 受控（企業級框架） | 社群依賴較多 |

---

## 4️⃣ 平台與部署

- **NemoClaw**
  - **Linux**：Docker 測試主路徑
  - **Windows (WSL2)**：Docker Desktop + WSL2（官方標示有條件支援）
  - **macOS**：Docker/Colima 可用但受限
  - **安裝方式**：`curl -fsSL https://www.nvidia.com/nemoclaw.sh | bash`

- **OpenClaw**
  - **macOS/Linux/Windows (WSL2)** 支援
  - **npm / pnpm / docker / macOS app**

---

## 5️⃣ 一句話結論
- **NemoClaw**：如果你是 **安全敏感 / 企業治理** 導向，選它。
- **OpenClaw**：如果你要 **全通路能力 + 最大生態**，選它。

---

**Key Principle**：
> NemoClaw 不是取代 OpenClaw，而是把 OpenClaw 放進更安全的「企業級安全盒子」。
