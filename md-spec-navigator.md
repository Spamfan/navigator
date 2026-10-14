# NAVIGATOR
### Hub & Suite Orchestrator
### Living Design Document (LDD) V5

---

### 1. WATERFALL PROTOCOL & DUAL DELIVERY

#### The Loop
1. **Sprint Plan (SP):** Scope & target files.  
   *End prompt:* "Reply 'yes' to proceed to Tech Spec."  
2. **Tech Spec (TS):** Functions & logic diffs.  
   *End prompt:* "Reply 'yes' to proceed to Execution."  
3. **Execution:** Authorized delivery via Mode A or Mode B.  

#### Gate Rules
* **Prompted Handshake:** Gates advance *only*  
  in response to an active LLM gate prompt.  
  Unprompted "yes" never advances a gate.  
* **Triggers:** `yes`, `proceed`, `do it`, `patches`.  
* **Hard Stop:** Halt after every phase.  
  Never combine phases in one turn.  
* **Scope & Tone:** Bullets only. No fluff.  
  Declare file paths. No unasked refactors.  
* **Mobile Wrap (Mode B Only):**  
  In Mode B planning & chat turns, break  
  lines manually (<35-40 chars) to prevent  
  mobile clipping. Mode A (chat planning  
  and patch code blocks) is exempt.  
* **Pathway Declaration:** At session start /  
  Turn 1 Inbound Check, the LLM must declare  
  the active delivery pathway (Mode A default  
  or Mode B) and state it will proceed under  
  that mode until explicitly redirected.  
* **LDD Version Integer Bump:** Any edit to a  
  Living Design Document strictly increments  
  its integer version (`V(N)` → `V(N+1)`).  
  The root filename remains unversioned  
  (`md-spec-navigator.md`).  
* **Version Ceiling Guard:** Submodules must  
  remain within pre-1.0 (`v0.x.x`). LLMs are  
  strictly barred from bumping any version  
  to `v1.0.0+` without explicit PL sign-off.  

#### Delivery Pathways (Mode A vs Mode B)

##### Mode A: Traditional Patch Delivery (Chat LLM)
* **Automated Regex Engine Target:** Patches are parsed directly by an automated software patcher. Byte-for-byte fidelity is mandatory. Any whitespace, indentation, or character hallucination causes catastrophic failure.
* **Unified Multi-File Delivery:** Deliver all patches across all modified files in a single unified markdown response.
* **Target File Demarcation & Ordering:** Every file segment MUST begin with:
  `### TARGET FILE: path/to/file.ext`
  or for dynamic renaming during patching:
  `### TARGET FILE: path/to/old_file.ext -> path/to/new_file.ext`
  In multi-file deliveries, target file segments must be ordered strictly alphabetically by target source filename.
* **Strict Sequential Patch Headers:** Explicitly state patch index and total count: `Patch X/Y`.
* **Literal Contiguous Matching (Zero Shortcuts):** Matches are strictly applied via character-by-character regex slices. `Find`, `Find start`, and `Find end` blocks must be uninterrupted, contiguous slices of literal code. Never bridge code with ellipses (`...`, `...and...`, or `// rest of code`).
* **Nested Code Fence Guard:** When delivering markdown changes containing code examples, never place raw triple backticks inside a triple-backtick block. Escape them (` ` ` `) or use 4-space indentation to prevent terminating the patch block prematurely.
* **Patch Methods (Standard vs. Range):**
  * **Method 1: Standard Exact Match (Mandatory Default, 1–15 lines):** Replaces exact matching block using `Find` and `Replace with`. This is the required default for all code updates.
  * **Method 2: Range Match with Spatial Anchors (Strictly for Complete Block Rewrites):** Reserved exclusively for total block rewrites or deletions. Must NEVER be used to bridge distant edits.
    * **Engine Cursor Mechanics:** The patcher locates `Find start`, sets search pointer to `cursor = start_match.index + start_match.length`, and searches forward for `Find end`. Replaces `[start_match.start() ... end_match.end()]` inclusive.
    * **Zero-Intersection Rule:** `Find end` must exist strictly downstream of `Find start`. `Find end` (and any substring thereof) must NEVER appear inside `Find start`.
    * **Minimal Boundary Span (2–4 Lines Mandate):** `Find start` and `Find end` are spatial boundary anchors only, NOT code payloads. Each anchor must be strictly 2–4 unique lines. NEVER paste the replacement body inside `Find start`.
* **Anti-Drift Patch Constraints (Zero Tolerance):**
  * **Surgical Atomicity Mandate:** Disconnected edits separated by >3 lines must be split into separate micro-patches (`Patch 1/N`, `Patch 2/N`). Bridging untouched lines across disconnected edits is prohibited.
  * **Engine First-Match Defense:** JavaScript patch engines use `String.prototype.replace()`, replacing only the first occurrence. `Find` blocks MUST include 1–2 unique enclosing lines of context (function headers, distinct parent statements) to prevent collision with identical lines earlier in the file.
  * **Verbatim Grounding & Whitespace Fidelity:** Anchors must be copied byte-for-byte from intake files. Zero whitespace, tab, or indentation reformatting.
  * **Single-Scope Boundary Mandate:** Every patch must be scoped strictly to a single function, method, object, or CSS rule set.
  * **Zero Unmodified Pass-Through:** A patch payload (`Replace with`) must NEVER contain untouched sibling code simply to bridge two separate changes. Split into sequential patches instead.
  * **Payload Ceiling:** Standard matches must remain 1–15 lines; Range match replacement bodies must never exceed 40 lines.
* **Post-Patch Manifest Declaration (Strictly Alphabetical):** Every delivery must conclude with:
  1. Target declaration: `These edits apply to: [list]` (alphabetical)
  2. Alphabetical list of updated files & versions.
  3. Active Manifest Roster in strict alphabetical order marked `[UPDATED]` or `[unchanged]`.

###### Mode A Template Reference

*Sample Standard Patch Format:*

    Patch 1/2
    Find
    ` ` `html
    <div class="old-header">Old Header</div>
    ` ` `
    Replace with
    ` ` `html
    <div class="new-header">New Header</div>
    ` ` `

*Sample Range Patch Format:*

    Patch 2/2
    Find start
    ` ` `html
    <div class="card-container">
    ` ` `
    Find end
    ` ` `html
    </div><!-- end card -->
    ` ` `
    Replace with
    ` ` `html
    <div class="card-container">
        <p>New Card Content</p>
    </div><!-- end card -->

##### Mode B: Spark Agent (Workspace & Drive)
* **Zero Chat Patches:** Mode A and Mode B are mutually exclusive. Zero code patches, blocks, or full file dumps in chat.
* **Workspace Execution:** Synthesize files directly in sandbox; validate headlessly (`node --input-type=module -c`).
* **Google Drive Isolation Boundary:** Restricted **strictly** to folder `just Gemini stuff`. Never mutate anything outside.
* **Zip Deliverable:** Package modified files into `just Gemini stuff/update_vX.X.X.zip`.
* **Chat Output Limits:** Restricted strictly to direct Drive link, test status, and Manifest Roster.

---

### 2. INBOUND CHECKS & MEDIA RULES

* **Universal Inbound Check:** Trigger on  
  *any* upload at *any* turn. Inspect file  
  header/version *before* planning/diagnosing.  
* **Stacked Status Tags:** Emoji first:  
  `✅ [MATCH] file.ext`  
  `↳ vX.X.X verified`  
  `⚠️ [STALE] file.ext`  
  `↳ found vX.X.X`  
  `⚠️ [UNKNOWN] file.ext`  
  `↳ not in baseline`  
* **Missing Media Rule:** If PL mentions media  
  ("see attached", "screenshot") and it is  
  not attached, reply ONLY with:  
  `error: you didn't attach media!`  

---

### 3. WORKSPACE, DRIVE & EXECUTION SIGN-OFF

* **Drive Isolation (Mode B Only):** The agent  
  is authorized **exclusively** to create files  
  inside the Google Drive folder: `just Gemini stuff`.  
  Touching or modifying anything outside is forbidden.  
* **Surgical Edits:** Targeted lines only.  
  Unchanged lines must remain byte-for-byte  
  identical across both delivery modes.  
* **Execution Sign-Off Requirements:**  
  * Every delivery turn must conclude with:  
    1. Target file declaration: `These edits apply to: [list]` (alphabetical)  
    2. Alphabetical list of updated files & bumped versions.  
    3. Mode A: Applied patches ready for extraction.  
       Mode B: Direct Drive link to `update_vX.X.X.zip`.  
    4. Active Manifest Roster in strict alphabetical order showing all modules marked:  
       `[UPDATED]`, `[unchanged]`, or `[unchanged - floor]`.  

---

### 4. GLOBAL UI & NAVIGATION

* **Design System / Palette:**
  * Background: `--bg: #0d1117`
  * Card Surfaces: `--card-bg: #161b22`
  * Borders: `--border: #30363d`
  * Text Elements: `--text: #c9d1d9`, `--text-muted: #8b949e`, `--text-bright: #f0f6fc`
  * Accent & Status: `--accent-blue: #388bfd`, `--blue: #238636`, `--purple: #8957e5`, `--danger: #f85149`
* **Component Tokens:**
  * User Switcher: Inline editable badge in status bar.
  * Status & Quota Pills: Fixed-metric indicators with live ticker updates.
  * Repo Cards: Dual-row layout with primary action buttons (`Go to repo`), quick links (`Repo ↗`), star favorites, hide controls, and dynamic action buttons (Upload main, up to 3 subfolder uploads, ZIP all, index.html download).
  * Drag Handles: Active for favorited repos (`⋮⋮`) with pointer reorder mechanics.
  * Files Drawer: Collapsible recursive file explorer per repository.
  * Toast Alerts: Bottom-centered snackbar with swipe dismissal and single-click undo.
* **Global Hotkey:** Pressing `/` focuses the search filter input.

---

### 5. SYSTEM & ARCHITECTURE CONSTRAINTS

* **Display & Target Environment:** GitHub Pages static root hosting (`index.html`). Zero external CDN dependencies.
* **Storage Schema Contract:** Local storage keys strictly adhere to legacy `wp_*` namespace:
  * `wp_data_{user}`: Cached repository metadata and tree arrays.
  * `wp_prefs_{user}`: User preferences (favorites, hidden items, sort orders).
  * `wp_visits_{user}`: Timestamped repository visit history.
  * `wp_quota`: Cached API rate limits (`remaining`, `limit`, `reset`).
* **GitHub Rate Limits:**
  * Unauthenticated client limit: 60 requests/hour/IP.
  * Authenticated crawler limit: 1,000 requests/hour.
* **Network Failover Hierarchy:**
  1. Instant render from `localStorage['wp_data_' + user]`.
  2. Zero-quota static fallback from root `repos.json`.
  3. Live REST API fetch with automatic rate limit tracking.
  4. On-demand recursive Trees API querying for repository files.

---

### 6. BASELINE FLOOR & FILE TREE

#### Version Floor
* **App (NAVIGATOR):** `v0.3.2`  
* **`index.html`:** `v0.3.2`  
* **`md-spec-navigator.md`:** `v5.0.0`  
* **`repos.json`:** `synced_at` snapshot  

#### File Tree
* `.github/workflows/update.yaml`
* `index.html`
* `md-spec-navigator.md`
* `repos.json`

---

### 7. SUB-SPOKE DIRECTORY & CONTEXT GUARD

#### Hub & Spoke Suite Architecture
Navigator operates as the central cockpit and discovery hub across all repositories in the user workspace:
* **Direct Top Navigation Links:**
  * `patcher` -> `https://{user}.github.io/patcher/` (Automated regex patch application tool)
* **Target Launch Links:**
  * User Pages: `https://{user}.github.io/`
  * Project Pages: `https://{user}.github.io/{repo}/`
* **Direct Source Links:**
  * Web Editor / Upload: `https://github.com/{user}/{repo}/upload/{branch}`
  * Head Zipball: `https://github.com/{user}/{repo}/archive/refs/heads/{branch}.zip`
  * Raw File Blobs: `https://raw.githubusercontent.com/{user}/{repo}/{branch}/{path}`

#### Automated Sync & Fallback Pipeline
    [GitHub Actions (Daily 04:00 UTC)]
           │
           ▼
    [Commit fresh repos.json to main]
           │
           ▼
    [User visits Navigator (index.html)]
           ├── (1) Cache present? ───────► Render wp_data_{user} (0 network hits)
           ├── (2) Cache missing / old? ─► Fetch repos.json (0 API quota used)
           └── (3) Sync requested? ──────► Query api.github.com/users/{user}/repos

#### Context Guard
* **Pre-Flight File Lockout:** If target files are missing or requested, reply ONLY with the required intake file list and halt.  
* **Crawler & Suite Context Guard:** When modifying subsystem bridge code or crawler workflows, ensure related specifications are attached:  
  * `.github/workflows/update.yaml` -> Requires `repos.json` structure reference.  
  * Cross-app links in `index.html` -> Requires active username state verification.  
* **Downstream Consumer Intake:** Whenever a sub-spoke bump occurs, mandate intake upload of `index.html` to guarantee end-to-end cache busting.