# Hermes Agent vs OpenClaw —— 完整比較

> 整理日期：2026-04-03 | 版本：Hermes (v最新), OpenClaw v2026.3.23+

---

## 1️⃣ 核心定位

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **開發者** | Nous Research (開源) | 開源社群 |
| **設計理念** | 通用型 AI Agent，多平台 gateway + 自我學習 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | Provider-agnostic，任何 LLM 都能用，跨平台整合 | 內建 LLM runtime + ACP 架構委派外部 CLI agent |
| **主要使用場景** | 研究、開發、系統管理、內容創作、智慧家庭、多平台通訊 | 程式開發、自動化工作流、多 agent 協作、預測市場整合 |

---

## 2️⃣ 架構比較（經 DeepWiki 確認）

### Hermes Agent（DeepWiki 1.1 + 1.2）

#### 三層架構
1. **User Interface Layer**: CLI (`cli.py`)、Messaging Gateway (`gateway/run.py`)、Batch Runner (`batch_runner.py`)
2. **Core Agent Layer**: AIAgent (`run_agent.py`)、ContextCompressor、MemoryStore、HonchoManager、Auxiliary Client
3. **Tool & Execution Layer**: ToolRegistry + pluggable toolsets + isolated execution backends

#### AIAgent 核心
- **`run_conversation()`**: 迭代式 LLM → tool-calling → respond 循環
- **Iteration Budget**: 限制每輪 LLM 呼叫次數，防止無限循環 (可設定)
- **Tool dispatch**: `handle_function_call()` → `registry.dispatch()`
- **System prompt assembly**: `_build_system_prompt()` 動態組裝
- **Auxiliary Client**: 次要 LLM Client 用於 vision、web extraction 等 side tasks，不佔用 primary model budget

#### 專案結構（DeepWiki 1.2）
| 目錄 | 功能 |
|------|------|
| `agent/` | Prompt builder, compression, display adapters |
| `tools/` | 一檔案一個 tool + `registry.py` |
| `gateway/` | 訊息平台 + `platforms/` + `session.py` |
| `environments/` | 執行後端（local/docker/ssh/modal/singularity） |
| `hermes_cli/` | CLI subcommands, config, setup |
| `cron/` | 排程器 |
| `skills/` | 內建技能 |

---

### OpenClaw（DeepWiki 1.3）

#### Gateway + Agent Runtime 分離
- **Gateway**: WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**: Pi embedded runner (`runEmbeddedPiAgent`)，獨立於 Gateway 的 AI 執行引擎
- **Multi-agent routing**: 依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**: channels, models, tools, skills, speech, image gen 都可透過 plugin 擴展

#### 平台架構特點
- **Node 系統**: macOS/iOS/Android 原生 app 作為 Device Node
- **Device Node Protocol**: WebSocket-based RPC，暴露 camera/screen/location/notification
- **Live Canvas (A2UI)**: Agent 可渲染交互式 UI canvas
- **Voice Wake + Talk Mode**: always-on 語音喚醒（macOS/iiOS/Android）

---

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **核心架構** | 三層：UI/Core/Tools | Gateway + Agent Runtime + Extensions |
| **Agent 引擎** | AIAgent (run_agent.py) | Pi embedded runner (runEmbeddedPiAgent) |
| **Plugin 系統** | ❌ 工具內建 | ✅ Channels/Models/Tools/Skills 可 plugin |
| **輔助模型** | ✅ Auxiliary Client (Vision/Web 用便宜模型) | ❌ |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **Device Node** | ❌ | ✅ Camera/Screen/Location |
| **Gateway UI** | ❌ | ✅ Control UI + WebChat (localhost:18789) |

## 3️⃣ LLM Provider 支援

### Hermes Agent（經 DeepWiki 10.2 確認）

#### Provider Runtime Resolution（DeepWiki 10.2）
- **動態 Provider 切換**: 在執行時期確定使用哪個 LLM provider，不需重啟
- **Per-task overrides**: 每個任務可指定不同 provider/model
- **Automatic fallback chains**: failover 無硬編碼依賴
- **Credential pooling**: `auth.json` 管理多 API Key，exhaustion 狀態追蹤
- **OAuth 支援**: `hermes login --provider nous/openai-codex` 支援 OAuth flow
- **Custom endpoint**: `model.base_url` + `model.api_key` 可自訂
- **API Key / Token**: 支援 API Key、OAuth Token、GitHub Copilot Token 多種認證

#### Smart Model Routing
- **簡單任務自動降級**: short messages → cheap model
- **複雜關鍵字過濾**: 36 個關鍵字（debug/error/implement/refactor...）防止降級

#### Auxiliary Client（DeepWiki 4.5）
- **次要 LLM Client**: 用於 vision、web extraction 等 side tasks
- **Cost savings**: 用便宜模型處理輔助任務，保留 Primary model 給核心推理

---

### OpenClaw（經 DeepWiki 3.3 確認）

#### Model Providers（DeepWiki 3.3）
- **Auth Profiles**: 每個 agent 有自己的 `auth-profiles.json`
- **Provider synchronization**: `openclaw.json` ↔ `models.json` 自動同步
- **Credential hierarchy**: 環境變數 → config → auth-profiles 層級解析
- **OAuth 支援**: OpenAI Codex subscription (OAuth), Anthropic (API key / setup-token)
- **Model catalog / allowlist**: `agents.defaults.models` 定義可用模型（含 aliases）
- **Fallback chains**: primary → fallbacks（依順序），provider auth failover 在移動到下一個 model 前

#### Provider Normalization（DeepWiki 3.3.1）
- Model refs 正規化為 lowercase (`provider/model` 格式)
- Provider aliases (`z.ai/*` → `zai/*`)

---

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **Provider 數量** | 18+ (OpenRouter, Anthropic, OpenAI, Nous, DeepSeek, HF, GitHub Copilot...) | 多家（透過 OAuth + API Key） |
| **動態切換** | ✅ 無需重啟 | ✅ 透過 /model command |
| **憑證池** | ✅ 多重 API Key 自動輪轉 | ❌ Auth Profile per-agent |
| **智慧路由** | ✅ Smart Model Routing | ❌ |
| **Auxiliary Client** | ✅ Vision/web 用便宜模型 | ❌ |
| **Fallback chains** | ✅ | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ config.yaml | ✅ Custom providers in models.json |
| **Model allowlist** | ❌ | ✅ agents.defaults.models |
| **Provider normalization** | ❌ | ✅ lowercase + alias |

## 4️⃣ 記憶與學習機制

### Hermes Agent（經 DeepWiki 確認）

#### Memory 系統（DeepWiki 4.3: Memory and Sessions）
- **MEMORY.md / USER.md**：基於 Markdown 檔案的持久化記憶，由 `MemoryStore` 在 `hermes_state.py` 管理
- **SQLite-based Session Store**：`gateway/session.py` 管理對話上下文，`hermes_state.py` 處理標題生成和記憶體
- **Memory 注入**：每輪自動注入 `~/.hermes/memory/` 的事實到 system prompt
- **Session 鍵值結構**：`sessionKey` 結合 channel/peer，支援 DM 和群組隔離
- **Context Compression**：`agent/context_compressor.py` 在接近 token limit 時自動壓縮對話歷史
- **Honcho Integration**：可選的外部 Memory provider，支援 dialectic 推理循環

#### Session 檢索
- **`session_search`**：內建 FTS5 全文搜尋，可查詢歷史對話
- **Session Title Generation**：`agent/title_generator.py` 自動生成會話標題

#### Skills 系統（DeepWiki 8.2: Skills Hub）
- **SKILL.md 格式**：每個 skill 是一個目錄，包含 `SKILL.md`（Markdown 指令檔）
- **Skills Hub**：用戶驅動的發現/安裝系統，支援官方、GitHub、`skills.sh` 市集
- **自動匹配**：description 匹配時自動 `skill_view()` 載入
- **用戶驅動**：Skills Hub **僅限用戶操作**，agent 不能自主安裝技能
- **Per-agent/Per-platform**：skills 可按 agent 和平台獨立啟用/停用

#### Context Compression（DeepWiki 10.1）
- **自動觸發**：接近 token limit 時自動壓縮
- **Preserves continuity**：保留對話連續性和任務狀態
- **Config**：`threshold: 0.50`, `target_ratio: 0.20`

---

### OpenClaw（經 DeepWiki 確認）

#### Memory 系統（DeepWiki 3.4.3: Memory & Search）
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Embedding provider selection**：可配置外部 embedding provider
- **Hybrid search**: 向量 + FTS（全文搜尋）
- **Sync strategies**：記憶體與 workspace 同步策略
- **Per-agent `agentDir`**：每個 agent 的 auth-profiles.json 和 model registry

#### Context Compaction（DeepWiki 3.6）
- **`compactEmbeddedPiSession`**：自動壓縮長對話歷史
- **Token 限制管理**：當 token count 接近 context window 時自動總結
- **Preserves tool results**：保留工具執行結果的連續性

#### Skills System（DeepWiki 5.2）
- **Directory-based skills**：每個 skill 是 `SKILL.md` + 可選腳本/模板
- **Environment injection**：skill 可注入環境變數、依賴檢查
- **Per-agent vs shared**：`skills/` 在 workspace 是 per-agent，`~/.openclaw/skills` 是共享
- **Auto-discovery**：agent 啟動時自動發現可用 skills

---

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **Memory 類型** | ✅ SQLite + Markdown (MEMORY.md/USER.md) | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ✅ FTS5 (session_search) | ✅ FTS (hybrid) |
| **每輪自動注入** | ✅ 自動注入 memory/ | ✅ 需 sync triggers |
| **Session 持久化** | ✅ SQLite `hermes_state.db` | ✅ JSONL transcript files |
| **Context 壓縮** | ✅ ContextCompressor (threshold/ratio) | ✅ compactEmbeddedPiSession |
| **Honcho/Mem0 集成** | ✅ 可選外部 backend | ✅ Plugin 生態 |
| **Skill 格式** | ✅ SKILL.md (Markdown) | ✅ SKILL.md (Markdown + env vars) |
| **Skills Hub** | ✅ 多來源安裝 (GitHub/skills.sh) | ✅ ClawHub + npm |
| **Agent 自主安裝** | ❌ 僅用戶可操作 | ✅ 可透 plugin |
| **Per-agent 隔離** | ✅ via Profiles | ✅ via workspace agentDir |
## 5️⃣ 工具與擴展（經 DeepWiki 確認）

### Hermes Agent

#### Toolsets（DeepWiki 5.1）
18 內建 toolsets：web, browser, terminal, file, code_execution, vision, image_gen, tts, skills, memory, session_search, delegation, cronjob, webhook, clarify, homeassistant, moa, mcp

#### Execution Backends（DeepWiki 6.2）
| Backend | 說明 |
|---------|------|
| **local** | 本機執行，最快 |
| **Docker** | 容器化隔離 |
| **SSH** | 遠端伺服器 |
| **Modal** | 雲端 serverless sandbox |
| **Singularity** | HPC/Apptainer 容器 |
| **Daytona** | 雲端開發環境 |

#### Cron（DeepWiki 10.3）
- **自然語言 job 定義**：`prompt` 字段描述任務
- **Schedule types**：interval (30m, every 2h), cron (`0 9 * * *`), once
- **Delivery**：local / origin / telegram / discord / slack / whatsapp / email 等
- **Repeat count**：可設定重複次數
- **Skill preloading**：job 可指定 skills 在執行前載入
- **Storage**：`~/.hermes/cron/jobs.json`，輸出存於 `~/.hermes/cron/output/`

#### Voice（DeepWiki 11.1 + 11.2）
- **TTS**：Edge/ElevenLabs/OpenAI/Kokoro/Fish Audio
- **STT**：faster-whisper(local) / Groq / OpenAI
- **Discord voice channel**：支援語音頻道
- **CLI recording**：`AudioRecorder` 用 sounddevice 捕捉 16kHz mono PCM
- **Format conversion**：Telegram 用 ffmpeg 轉 Opus OGG

#### Subagent Delegation（DeepWiki 5.7）
- **`delegate_task`**：平行最多 3 個子 agent
- **Isolated context**：各自有獨立 terminal、toolset、cwd
- **Progress reporting**：CLI 和 Gateway 環境支援進度回報
- **Batch mode**：可一次性委派多個任務
- **Iteration budget**：子 agent 獨立上限（預設 50）

---

### OpenClaw

#### Tools System（DeepWiki 3.4）
- **內建工具**：exec/process, code_execution, browser, web_search/x_search/web_fetch, read/write/edit, apply_patch, message, canvas, nodes, cron/gateway, image/image_generate, sessions_*/agents_list
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes, group:openclaw
- **Plugin tools**：Lobster(typed workflow), LLM Task(structured JSON), Diffs, OpenProse

#### Automation & Cron（DeepWiki 3.7）
- **CronService**：持久化 job storage
- **Session scopes**：main / isolated / current session
- **Heartbeat**：定期 agent interaction
- **Webhook**：可配置 webhook trigger
- **Pre-execution hooks**：job 前可跑 hook

#### Image & Media（DeepWiki 3.4.6）
- **image_generate**：openai/fal/google provider
- **Audio/video**：Whisper transcription
- **PDF processing**
- **TTS/Voice**：OpenAI TTS, ElevenLabs streaming

#### ACP & Sub-Agents（DeepWiki 3.8）
- **ACP**：Agent Control Protocol — parent-child session 隔離 + delivery dispatch
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派給 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Async reporting**：子 agent 非同步回報結果

#### Sandboxing（DeepWiki 7.3）
- **Docker containers**：主要 sandbox 機制
- **seccomp/AppArmor**：進程級隔離
- **Filesystem rules**：workspace-only access (sandbox mode)
- **Network rules**：URL allowlists/deny lists
- **Per-session workspaces**：非主 session 獨立 workspace

---

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **工具數量** | 18 toolsets | 12+ 內建 + plugins |
| **工具控制** | tool enable/disable per-platform | allow/deny/profile/byProvider + groups |
| **Execution 後端** | 6 種 (local/Docker/SSH/Modal/Singularity/Daytona) | Docker(seccomp/AppArmor)/SSH/native |
| **Cron** | 自然語言 + skill preload + 多平台 delivery | CronService + session scopes + heartbeat |
| **TTS/STT** | 5 TTS + 3 STT provider | OpenAI/11Labs TTS + Whisper STT |
| **圖片生成** | image_gen | image_generate(openai/fal/google) |
| **Sub-agents** | delegate_task(3 max, isolated) | ACP + ACPX(external CLI), async |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Agent-to-Agent** | ❌ | ✅ agentToAgent tool (可啟用) |
| **Voice Wake** | ❌ | ✅ 原生 App always-on |
| **Canvas A2UI** | ❌ | ✅ |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |

## 6️⃣ 多 Agent 架構（Agent Control Protocol / Sub-Agents）

### Hermes Agent（DeepWiki 5.7 確認）

#### `delegate_task`
- **三種模式**:
    1. **Single-task**: 單一目標子任務
    2. **Batch**: 平行最多 3 個獨立子 agent
    3. **Progress reporting**: 支援 CLI 和 Gateway 進度回報
- **隔離**: 各自有獨立 dialogue, terminal session, cwd, environment variables
- **Budget**: `max_iterations` 可設定（預設 50），防止耗盡資源
- **工具限制**: 明確**禁止**呼叫 `delegate_task`, `clarify`, `memory`, `execute_code`, `cronjob`
- **Context Relay**: 子 agent 無父 agent 對話上下文，需手動透過 goal/context 傳遞
- **Spawning**: `hermes chat -q` / `tmux` 可啟動獨立 Agent 進程（非 delegate_task）

### OpenClaw（DeepWiki 3.8, 2.5 確認）

#### ACP (Agent Control Protocol)
- **Parent-Child Session**: 支持子會話生命周期管理（`sessions_spawn` / `sessions_yield` / `agents_list`）
- **Sub-agent registry**: 註冊中心管理運行中的子代理
- **Delivery Dispatch**: 專門的傳遞系統處理子代理結果回傳
- **Multi-agent routing**: 依 channel/account/peer/guild/team 路由到隔離 agent
- **agentToAgent**: 可選工具讓 agent 之間互相發送訊息（預設關閉）
- **Cross-agent QMD**: `memorySearch.qmd.extraCollections` 允許 Agent A 搜尋 Agent B 的會話
- **ACPX Harness**: 委派任務給外部 AI CLI (Claude Code, Codex, Gemini, Kiro, Cursor)
    - 每個外部 CLI 有獨立的 auth、tool use、LLM API
    - 透過 stdin/stdout JSON-RPC 通訊

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **子任務委派** | `delegate_task` — 最多 3 平行子 agent | ACP 協議 — sessions_spawn/yield + ACPX 委派 |
| **外部 CLI 委派** | ❌ | ✅ ACPX Harness (Claude/Codex/Gemini/Kiro/Cursor) |
| **子會話管理** | 簡單 tool result 回收 | ✅ sessions_spawn / sessions_yield / agents_list |
| **Agent Registry** | ❌ | ✅ ACP sub-agent registry |
| **遞迴保護** | ✅ 子 agent 不能 delegate_task | ✅ 由外部 CLI / ACP 控制 |
| **Context 隔離** | ✅ 無父 agent 上下文 | ✅ parent-child session |
| **Multi-Agent 路由** | ❌ | ✅ 依 channel/account/peer 自動分流 |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool (可選) |
| **跨 Agent 搜尋** | ❌ | ✅ Cross-agent QMD search |
| **Delivery Dispatch** | 直接返回結果 | ✅ ACP managed delivery |
| **完整進程模式** | `hermes chat -q` / tmux PTY | Claude Code / Codex / Gemini CLI 進程 |
| **Git Worktree** | ✅ `hermes -w` | ✅ 透過外部 CLI |

## 7️⃣ 安全性與沙盒

### Hermes Agent（經 DeepWiki 5.4 + 7.4 確認）

#### 防禦縱深架構（Defense-in-Depth）
```
ExecutionEntry → ScanningLayer (Pattern + Tirith) → StateAndPolicy → ApprovalUI/CUI
```
- **Dangerous Command Detection**：`detect_dangerous_command()` 使用 regex 掃描高風險操作（`rm -r`、`rm /`、`chmod 777`、`curl\|sh`、`DROP TABLE`、`pkill hermes` 等）
- **Command Normalization**：掃描前 strip ANSI escapes、Unicode NFKC 正規化，防止 bypass
- **Tirith 整合**：`tirith_security.py` 自動安裝並驗證 SHA-256，執行 `scan` 返回 block/warn/allow
- **URL Safety & SSRF Protection**：`url_safety.py` 解析 hostname → IP，阻擋 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16、127.0.0.1、169.254.169.254、100.64.0.0/10、`metadata.google.internal`
- **Website Blocklist**：`config.yaml` → `security.website_blocklist`，支援 `*.domain.com` wildcard
- **Container Isolation**：Docker / Modal 模式**自動 bypass 審核提示**（sandbox 本身就隔離）

#### 審核狀態管理（tools/approval.py）
| 範圍 | 說明 |
|------|------|
| **Once** | 本次批准 |
| **Session** | `_session_approved`: 依 session_key 記錄已批准 pattern |
| **Permanent** | `_permanent_approved`: 從 `config.yaml` 載入的永久白名單 |
| **Pending Queue** | Gateway 模式下提交待審佇列 |

#### Gateway 安全與配對（DeepWiki 7.4）
- **DM Policy**：`pairing` / `allowlist` / `open` / `disabled`
- **Allow-All Override**：`DISCORD_ALLOW_ALL_USERS=true`
- **Platform-specific**: 每個 platform adapter 有自己的 DM policy
- **Allowlist Startup Check**：啟動時檢查 allowlist 配置

#### 其他安全機制
- **Filesystem Checkpoints**：`/rollback` 還原快照
- **Python 沙盒**：Foreground-only, 50KB stdout, 5 分鐘
- **子代理限制**：禁止 delegate/clarify/memory/execute_code
- **Credential Pool**：多 API key 輪轉 + exhaustion 追蹤
- **Iteration Budget**：子 agent 獨立預算上限（預設 50），防止耗盡父 agent 資源
- **YOLO Mode**：`HERMES_YOLO_MODE` 可關閉所有危險指令審核

---

### OpenClaw（經 DeepWiki + `src/security/` 確認）

#### Sandbox 架構
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **嚴格檔案系統和網路規則**

#### 安全機制
- **DM Policy**：`pairing` / `allowlist` / `open` / `disabled`（預設 pairing）
- **Tool Policy**：`tools.allow` / `tools.deny`（deny 優先）+ `tools.profile` + `tools.byProvider`
- **Tool Result Guard**：`guardSessionManager` 驗證工具輸出形狀與策略
- **SecretRef**：敏感資料動態解析，encrypted auth-profile
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `audit-extra.ts` / `fix.ts`
- **Zod Schema 驗證**：嚴格的 config 驗證，未知 key 直接拒絕啟動
- **Capability Model**：定義 channel/agent 允許的操作

---

### 對比總結

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **Sandbox 後端** | ✅ local / Docker / SSH / Modal / Singularity / Daytona | ✅ Docker / SSH / 原生 OS |
| **危險指令 Regex 掃描** | ✅ 7 種 pattern（刪除/權限/遠端執行/SQL/自我終止） | ❌ |
| **Tirith 整合** | ✅ 自動安裝、SHA-256 驗證、block/warn/allow | ❌ |
| **Command Normalization** | ✅ ANSI strip + Unicode NFKC | ❌ |
| **SSRF Protection** | ✅ 阻擋 6 個私有/CIDR 範圍 + metadata hostnames | ✅ URL allowlists/deny lists |
| **Website Blocklist** | ✅ wildcard `*.domain.com` | ❌ |
| **審批範圍** | ✅ Once / Session / Permanent | ❌ |
| **審批待審佇列** | ✅ Gateway 模式下 `submit_pending()` | ❌ |
| **Sandbox 自動 bypass** | ✅ Docker/Modal 時跳過審核 | ❌ |
| **Container Isolation** | ✅ 6 種後端 | ✅ seccomp/AppArmor |
| **Seccomp/AppArmor** | ❌ | ✅ profiles |
| **Tool Policy** | ❌ | ✅ allow/deny/profile/byProvider |
| **Tool Result Guard** | ❌ | ✅ 驗證輸出形狀 |
| **DM Policy** | ✅ pairing/allowlist/open/disabled | ✅ pairing/allowlist/open/disabled |
| **Allowlist Startup Check** | ✅ 啟動時檢查 | ❌ |
| **Security Audit** | ❌ | ✅ audit.ts / audit-fs.ts / fix.ts |
| **SecretRef** | ✅ `.env` + auth.json | ✅ SecretRef + encrypted auth-profile |
| **Filesystem Checkpoints** | ✅ `/rollback` | ❌ |
| **Zod Schema Validation** | ❌ | ✅ 拒絕未知 key 啟動 |
| **Capability Model** | ❌ | ✅ 定義操作權限 |
| **Iteration Budget** | ✅ 子 agent 獨立上限 | ❌ |
| **YOLO Mode** | ✅ `HERMES_YOLO_MODE` | ❌ |
| **Credential Rotation** | ✅ 憑證池 | ✅ Auth Profile rotation + failover |

## 8️⃣ 平台與 Gateway（經原始碼與官方 README 確認）

### Hermes Agent (`gateway/platforms/*.py`) — 共 17 個平台

| 平台 | 檔案 | 兩者都有？ |
|------|------|-----------|
| **Discord** | `discord.py` | ✅ 兩者都有 |
| **Telegram** | `telegram.py`, `telegram_network.py` | ✅ 兩者都有 |
| **Slack** | `slack.py` | ✅ 兩者都有 |
| **WhatsApp** | `whatsapp.py` | ✅ 兩者都有 |
| **Signal** | `signal.py` | ✅ 兩者都有 |
| **Matrix** | `matrix.py` | ✅ 兩者都有 |
| **Mattermost** | `mattermost.py` | ✅ 兩者都有 |
| **Feishu (飛書)** | `feishu.py` | ✅ 兩者都有 |
| **DingTalk (釘釘)** | `dingtalk.py` | ⚡ **Hermes 獨有** |
| **WeCom (企業微信)** | `wecom.py` | ⚡ **Hermes 獨有** |
| **Email (IMAP/SMTP)** | `email.py` | ⚡ **Hermes 獨有** |
| **SMS** | `sms.py` | ⚡ **Hermes 獨有** |
| **Home Assistant** | `homeassistant.py` | ⚡ **Hermes 獨有** |
| **API Server** | `api_server.py` | ⚡ **Hermes 獨有** |
| **Webhook** | `webhook.py` | ✅ 兩者都有 |
| **Open WebUI** | 文檔支援 | ⚡ **Hermes 獨有** |
| **WebChat** | 內建 | ✅ 兩者都有 |

### OpenClaw (官方 README 列出的 Channels) — 共 25+ 個平台

| 平台 | Hermes 有？ | 備註 |
|------|-----------|------|
| **WhatsApp** | ✅ | Baileys |
| **Telegram** | ✅ | grammY |
| **Slack** | ✅ | Bolt |
| **Discord** | ✅ | discord.js |
| **Signal** | ✅ | signal-cli |
| **Matrix** | ✅ | - |
| **Mattermost** | ✅ | - |
| **Feishu** | ✅ | - |
| **WebChat** | ✅ | 內建 |
| **Google Chat** | ❌ | Chat API |
| **BlueBubbles (iMessage)** | ❌ | 推薦的 iMessage 方案 |
| **iMessage (legacy)** | ❌ | 舊版 |
| **IRC** | ❌ | - |
| **Microsoft Teams** | ❌ | - |
| **LINE** | ❌ | - |
| **Nextcloud Talk** | ❌ | - |
| **Nostr** | ❌ | - |
| **Synology Chat** | ❌ | - |
| **Tlon** | ❌ | - |
| **Twitch** | ❌ | - |
| **Zalo / Zalo Personal** | ❌ | 越南通訊 |
| **WeChat** | ❌ | @tencent-weixin |
| **macOS App** | ❌ | 菜單列控制平面 |
| **iOS Node** | ❌ | Canvas, Voice Wake, Talk Mode |
| **Android Node** | ❌ | Voice Wake |

### 總結摘要

| 維度 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **平台總數** | ~17 | ~25+ (含原生 App) |
| **兩者共有的平台** | Discord, Telegram, Slack, WhatsApp, Signal, Matrix, Mattermost, Feishu, Webhook, WebChat | 同左 |
| **Hermes 獨有** | Email, SMS, DingTalk, WeCom, Home Assistant, API Server, Open WebUI | — |
| **OpenClaw 獨有** | — | Google Chat, BlueBubbles, iMessage, IRC, Teams, LINE, Nextcloud Talk, Nostr, Synology Chat, Tlon, Twitch, Zalo, WeChat, macOS/iOS/Android App |
| **原生 App** | ❌ | ✅ macOS / iOS / Android |
| **特色功能** | API Server, Home Assistant | Live Canvas, Voice Wake, Talk Mode |

---

## 9️⃣ Profiles & 多實例 (Multi-Instance)

**Hermes: 隔離配置檔 (Profiles)**
Hermes 透過 Profiles 提供完全隔離的獨立實例，每個 Profile 是一個獨立的 `HERMES_HOME`。
- **路徑**: `~/.hermes/profiles/<name>/`
- **包含**: 獨立的 `config.yaml`、`.env` (API Keys)、`memory/` (memories)、`sessions/`、`skills/`、`cron/`。
- **管理命令**: 完整 CLI (`create/clone/clone-all/use/show/delete/export/import/rename/alias`)
- **Wrapper alias**: 建立 `~/.local/bin/<name>` wrapper script，可直接 `coder chat`
- **Sticky default**: `hermes profile use <name>` 設定 sticky default
- **Daemon 隔離**: 每個 profile 有獨立 launchd/systemd service
- **Export/Import**: 支援 `tar.gz` 打包
- **驗證**: Profile name 必須匹配 `[a-z0-9][a-z0-9_-]{0,63}`
- **保留名稱**: `hermes`, `default`, `test` + 所有 Hermes subcommands

**OpenClaw: Multi-Agent Routing**
OpenClaw 透過單一 Gateway 管理多個隔離 Agent。
- **agents.list**: 定義多個 Agent，每個有獨立 `workspace` + `agentDir` (auth/model registry)
- **Bindings 路由**: 依 channel/account/peer/guild/team 自動分流
- **Per-agent auth**: 每個 agent 讀取 `~/.openclaw/agents/<id>/agent/auth-profiles.json`
- **Cross-agent QMD**: 可配置 `memorySearch.qmd.extraCollections` 讓 Agent A 搜尋 Agent B 的會話
- **Tools agentToAgent**: 可選啟用 agent-to-agent 通訊（預設關閉）
- **限制**: 無手動切換概念 — 完全自動分流，基於入口訊息匹配

| 項目 | Hermes Agent | OpenClaw |
|------|--------------|----------|
| **隔離方式** | ✅ Profiles (獨立目錄) | ✅ Multi-Agent (獨立 Workspace/Auth) |
| **切換機制** | ✅ 手動 (`hermes -p`) / Alias wrapper | ✅ 自動路由 (Bindings) |
| **憑證隔離** | ✅ 獨立 `.env` / `auth.json` | ✅ 獨立 `auth-profiles.json` |
| **Gateway 隔離** | ✅ 獨立 daemon service | ✅ 單一 Gateway 多 Agent |
| **Skill 隔離** | ✅ Profile 獨立 skills/ | ✅ Per-agent workspace skills/ |
| **Export/Import** | ✅ tar.gz | ❌ |

## 9.5 架構與流程圖解

### A. 核心架構對比

**Hermes: 單體架構 (Monolithic)**
```
Input (CLI/Telegram/Discord/Webhook/Cron) → Gateway → AIAgent → LLM(Call + Smart Routing) → Tool Dispatch → Respond
```
- Tool Dispatch → terminal(file/docker/ssh/modal) / browser / vision / tts / delegate(3 subagents)
- Memory: per-turn injection + skill matching

**OpenClaw: 分散式網關 (Gateway + Routing)**
```
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```
- Per-agent: workspace + auth + sessions + skills
- Sub-agents: ACP (sessions_spawn) + ACPX Harness (external CLI)

### B. 安全流程對比

**Hermes Defense-in-Depth**
```
Command → Normalize(ANSI+NFKC) → Pattern Scan → Tirith Scan → URL Safety/SSRF → Approval(Once/Session/Permanent) → Execute
```
- Docker/Modal 自動 bypass 審核
- YOLO 模式可關閉所有 Guard

**OpenClaw Defense-in-Depth**
```
DM Policy → Tool Policy(allow/deny/profile) → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → SecretRef
```
- zod schema: 拒絕未知 key 啟動

### C. 記憶流程對比

**Hermes: Fact Memory + Skill Matching**
```
User Input → Session Context → Memory Injection(facts) → Skill Matching(description) → Prompt Assembly → LLM
```

**OpenClaw: Workspace + Memory Plugin**
```
User Input → Session Context → Bootstrap Files(AGENTS.md/SOUL.md) → Memory Plugin(Vector Query → BM25 → Rerank) → Prompt Assembly → LLM
```

## 🏛️ 9.5 架構與流程圖解 (Architecture & Flow Diagrams)

### A. 核心架構對比 (Core Architecture)

**Hermes: 單體架構 **(Monolithic)
```text
Input (CLI / Telegram / Discord / Webhook / Cron)
   │
   ▼
[Hermes Gateway] ──► Session Manager (SQLite)
   │
   ▼
[Agent Core Loop]
   ├── 1. Build Context ──► Inject Memory (Facts) + Skills (Workflows)
   ├── 2. LLM Call ───────► Smart Routing (Cheap vs Strong Model)
   ├── 3. Tool Dispatch:
   │    ├── [Toolsets] ─▶ terminal / file / browser / vision / tts
   │    ├── [Delegation] ─▶ Subagent (Isolated, 3 max)
   │    └── [Extension] ─▶ MCP Servers / Webhooks
   └── 4. Update Memory & Respond
```

**OpenClaw: 分散式網關 + Agent 路由 **(Gateway + Routing)
```text
Input (Channels: WhatsApp / Telegram / Discord / Signal / iMessage)
   │
   ▼
[Auth Layer] (DM Policy: Pairing / Allowlist / Open)
   │
   ▼
[Routing Engine] ──► Match Binding (Channel / Account / Peer / Guild)
   │
   ▼
[Per-Agent Runtime] (Workspace + Auth + Sessions)
   ├── 1. Context: Workspace (AGENTS.md, SOUL.md) + Memory Plugin
   ├── 2. Model: Primary → Fallback (Auth Rotation)
   ├── 3. Tool Policy: tools.allow/deny → Sandbox Check → Exec
   ├── 4. Sub-Agents: ACP (sessions_spawn) + ACPX Harness (External CLI)
   └── 5. Result Guard (Validate Output) ──► Reply
```

### B. 多實例隔離對比 (Multi-Instance Flow)

| 特性 | Hermes: Profiles | OpenClaw: Multi-Agent Routing |
|------|------------------|-------------------------------|
| **概念** | 完全獨立的 "分身" | 同一 Gateway 下的不同 "大腦" |
| **路徑** | `~/.hermes/profiles/<name>/` | `~/.openclaw/workspace-<id>/` |
| **切換** | 使用者手動切換 (`hermes -p <name>`) | Gateway 自動路由 (基於 Channel/Peer) |

**Hermes Profile Flow**:
```text
User Command: "coder chat"
   │
   ▼
Alias `coder` ──► `hermes -p coder chat`
   │
   ▼
Load Profile `coder`
   ├── ~/.hermes/profiles/coder/config.yaml
   ├── ~/.hermes/profiles/coder/memory/
   ├── ~/.hermes/profiles/coder/skills/
   └── ~/.hermes/profiles/coder/sessions/
```

**OpenClaw Agent Routing Flow**:
```text
Inbound WhatsApp Message (+1555... from Work Acct)
   │
   ▼
Gateway: `agents.list` + `bindings`
   │
   ├── Match: { channel: whatsapp, accountId: work }
   │
   ▼
Target Agent: `work-agent`
   ├── Workspace: ~/.openclaw/workspace-work
   ├── Auth: ~/.openclaw/agents/work/agent/auth-profiles.json
   └── Sessions: ~/.openclaw/agents/work/sessions/
```

### C. 工具執行路徑對比 (Tool Execution Path)

**Hermes **(Toolsets)
- 依賴 Python 函數呼叫，後端可替換 (Local, Docker, SSH, Modal)。
- 由 Tool Definition 控制權限。

```text
LLM Output: call terminal(command="ls")
   │
   ▼

---

## 📊 總結對比

| 維度 | Hermes Agent 優勢 | OpenClaw 優勢 |
|------|------------------|--------------|
| **靈活性** | 18+ Provider，憑證池，Smart Routing，**Auxiliary Client (省錢)** | ACP + ACPX 委派多個專業外部 agent |
| **跨平台** | 17 通訊平台，Email/SMS/Home Assistant | **25+ 通訊平台**，原生 App (macOS/iOS/Android) |
| **工具控制** | 18 內建 toolsets，6 種執行後端 (local, docker, ssh, modal...) | Tool allow/deny/profile/byProvider，Lobster workflow DSL |
| **安全** | **Tirith 整合 + SSRF Protection + Audit Queue** | **seccomp/AppArmor + Security Audit + Tool Result Guard** |
| **記憶** | SQLite + Markdown (每輪注入)，Session FTS5 | MemoryIndexManager + **向量搜尋 + Hybrid search** |
| **壓縮** | ContextCompression (threshold/ratio) | compactEmbeddedPiSession |
| **多 Agent** | delegate_task (3 max, isolated) | **ACP 協議 + ACPX + sessions_spawn + multi-agent routing** |
| **開發者** | CLI 完整，~3000 tests，**Profiles 系統** | Plugin SDK，Zod 驗證，Control UI |
| **適合誰** | 研究、開發、需要 Email/SMS、**多環境隔離** | 最多通訊平台、**原生 App**、**安全審計** |

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| 最多元通訊平台 (WhatsApp/Telegram/Discord/Signal/iMessage/Teams/LINE/WeChat...) | ✅ OpenClaw |
| Email/SMS/Home Assistant 整合 | ✅ Hermes |
| macOS/iOS/Android 原生 App + Voice Wake | ✅ OpenClaw |
| iMessage 整合 | ✅ OpenClaw |
| 向量長記憶 + 多階段評分 | ✅ OpenClaw |
| 簡單設定 + 完整文檔 | ✅ Hermes |
| 開發 + 系統管理 + API 控制 | ✅ Hermes |
| 多 AI 模型協作 (Claude + Codex + Gemini) | ✅ OpenClaw |
| Smart Model Routing + **Auxiliary Client** 省錢 | ✅ Hermes |
| Live Canvas + Voice Wake | ✅ OpenClaw |
| 中國企業通訊 (DingTalk/WeCom) | ✅ Hermes |
| 安全審計 + Tool Result Guard | ✅ OpenClaw |
| Profiles 獨立環境 | ✅ Hermes |
| 從 OpenClaw 遷移 | ✅ `hermes claw migrate` |

