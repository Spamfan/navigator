# LIVING DESIGN DOCUMENT: NAVIGATOR (PROJECT BIBLE V2)

## PART 0: PROJECT ROLES & WORKFLOW

### A. THE TEAM
1. **Human Developer (PL / Project Lead):**
   - Final authority on all architecture, design, and feature decisions.
   - Conducts testing on target environments, identifies bugs, and sets requirements.
   - Maintains this LDD.
2. **LLM Developer (LLM / You):**
   - Programmer and logic analyst.
   - Translates requests into working code, brainstorms lean solutions, and strictly follows the waterfall protocol.
   - Adheres strictly to the delivery and patching standards defined below.

### B. THE "GOLDEN RULE" (STRICT WATERFALL)
1. **The Workflow Cycle:**
   You must strictly adhere to this development loop for every single task:
   - **a. Sprint Plan (SP):** High-level logic, user flow, and scope.
   - **b. Approval:** Wait for my explicit "Yes" or confirmation.
   - **c. Tech Spec (TS):** Detailed technical blueprint and architecture of the changes.
   - **d. Approval:** Wait for my explicit "Yes" or confirmation.
   - **e. Delivery:** Generating the actual find/replace patches.

2. **The Trigger Protocol (CRITICAL):**
   - **MANDATORY STOP:** You must STOP after every single phase.
   - **Explicit Progression Only:** You are NEVER allowed to proceed to the next step without an explicit, case-by-case command from the PL.
   - **Streamlined Single-Word Triggers:** The LLM is explicitly authorized and encouraged to prompt the PL with suggested single-word confirmation commands at the conclusion of any waterfall step (e.g., *"If this looks good and you want me to proceed to the Tech Spec, reply 'yes' or 'proceed'"*).
   - **Binding Authorization:** An unambiguous affirmative single-word command from the PL (e.g., `"yes"`, `"proceed"`, `"patches"`) in response to an LLM prompt constitutes full, binding authorization.
   - **No Casual Triggers:** Conversational chatter or passive praise (e.g., *"looks good"*, *"cool"*, *"nice"*) does NOT authorize phase progression.
   - **Brevity Mandate:** All non-code LLM responses across all phases (including Sprint Plans, Tech Specs, and clarifications) must be kept strictly dense, concise, and focused purely on essential technical points.

3. **Versioning & Delivery Standards:**
   - **Semantic Versioning:** Every functional change must include a unique semantic version bump (e.g., v0.0.1, v0.2.4, v1.3.0) pinned in master config/app headers.
   - **Delivery Method:** Standard delivery is performed strictly via numbered markdown find-and-replace patches (`Patch X/Y`).
   - **Full File Rewrites:** Strictly prohibited unless starting a brand-new foundation or when explicitly ordered by the PL.
   - **Automated Strict Regex Patcher Notice (Rule 0):** The PL applies all patches directly using an automated regex application tool. The regex engine matches exact character slices and will fail on any whitespace, tab, or newline mismatch. Every line inside `Find`, `Replace with`, `Find start`, and `Find end` blocks must be a 100% literal, character-for-character slice of the target file.
   - **Patch Counter:** Every patch header must explicitly state its current index and total count (e.g., `Patch 1/2`, `Patch 2/2`).
   - **Contiguous Code Only:** Every `Find`, `Find start`, or `Find end` block must represent a single, contiguous, uninterrupted slice of literal code.
   - **Zero Code Bridging / Zero Abbreviations:** NEVER use ellipses (`...`), truncation, or placeholder comments (such as `// ... existing code ...`, `/* unchanged */`, or `<!-- rest of html -->`) inside any block. Every block must contain 100% literal code matching the file character-for-character.
   - **Multiple Edit Locations = Multiple Patches:** If modifying code in two disconnected locations of the same file, you MUST split them into separate numbered patches. Never bridge separate edits.

4. **Patch Formats (Standard vs. Range):**
   You are equipped with two distinct patching methods. You have full discretion to choose between them on a patch-by-patch basis to optimize context token efficiency:

   - **Method 1: Standard Exact Match (Best for small, localized changes of 1–15 lines)**
     Replaces an exact matching block of code.

Patch 1/2
Find
```html
<div class="status-box">
    <p>Loading...</p>
</div>
```
Replace with
```html
<div class="status-box">
    <p>Ready</p>
</div>
```

   - **Method 2: Range Match with Spatial Anchors (Best for modifying or refactoring larger sections)**
     Replaces everything from the beginning of `Find start` to the end of `Find end` inclusive. Use this whenever replacing a large block to save output tokens instead of echoing dozens of unchanged lines. Both anchors must be completely unique to avoid ambiguous matching.

Patch 2/2
Find start
```html
<div class="user-list">
```
Find end
```html
</div><!-- end user-list -->
```
Replace with
```html
<div class="user-list">
    <ul id="users"></ul>
</div><!-- end user-list -->
```

   - **Token Optimization Rule:** Choose the method that uses the fewest output tokens while maintaining 100% uniqueness and zero ambiguity. Use Standard for tight edits; use Range when replacing large spans.

---

## PART 1: INTRODUCTIONS & INITIAL PROTOCOL

1. **Introductions & Rule Acknowledgment:**
   When an LLM first receives this document at the start of a session:
   - Read the document thoroughly.
   - Confirm understanding in **1–2 concise sentences**.
   - Output **two sample patches in perfect markdown format** (one Standard patch and one Range patch) demonstrating full protocol compliance.
   - **Objective File Verification (Zero-Assumption Rule):** Inspect literal internal metadata of all attached code files (e.g., `<title>` tag, top header comments, exported version constants, or root keys) and verbatim quote each in the onboarding reply (e.g., `Attached file inspection: <title>Project Shell</title> [MATCH]` or `Attached file inspection: <title>Old Shell</title> [⚠️ MISMATCH: Expected Project Shell]`). If a mismatch is detected, explicitly flag it as a prominent warning.
   - Await explicit PL direction.

2. **Missing Media Protocol:**
   If the PL refers to media or attachments that should accompany a prompt (e.g., "see attached image", "look at this document", "review the attached mock"), but no such file is present, do NOT generate conversational filler or attempt to guess.
   - Respond strictly with: `error: you didn't attach media!`
   - This rule protects the context window and can only be overridden by explicit instruction from the PL.

---

## PART 2: ARCHITECTURE & SYSTEM DESIGN

### A. REPOSITORY TOPOLOGY & DEPLOYMENT
1. **Target Environment:** GitHub Pages root hosting (`index.html`).
2. **Repository Layout:**
   - `index.html`: Self-contained frontend application (HTML5, embedded CSS3, vanilla ES6+).
   - `repos.json`: Static fallback repository and file cache committed to repository root.
   - `.github/workflows/update.yml`: Scheduled GitHub Actions crawler.
3. **External Dependencies:** Zero (no CDNs, no external CSS or JavaScript libraries).

### B. SYSTEM DATA FLOW & NETWORK PROTOCOL
1. **Tier 1 (Instant Cache):** On page load, read `localStorage['wp_data_' + user]`. If present, render immediately with zero network requests.
2. **Tier 2 (Static Fallback):** If `localStorage` is empty and active user is `spamfan`, fetch static `repos.json` (zero API quota consumption).
3. **Tier 3 (Live API Fallback & Manual Sync):**
   - Triggered on username switch or explicit click of `[Check again now]`.
   - Queries `https://api.github.com/users/{user}/repos?per_page=100&sort=updated`.
   - Extracts `x-ratelimit-remaining` and updates quota indicator pill.
   - Saves normalized structure to `localStorage`.
4. **Tier 4 (On-Demand Sub-File Exploration):**
   - Expanding a repository drawer queries Git Trees API recursively: `https://api.github.com/repos/{user}/{repo}/git/trees/{branch}?recursive=1`.
   - Returns all file blobs in 1 request per repo. Blobs map directly to `https://raw.githubusercontent.com/{user}/{repo}/{branch}/{path}`.

---

## PART 3: DATA MODELS & PERSISTENCE

### A. PERSISTENCE CONTRACT (`repos.json` & `localStorage['wp_data_{user}']`)
- **Legacy Storage Prefix:** Local storage keys retain the legacy `wp_*` prefix (`wp_data_*`, `wp_prefs_*`, `wp_visits_*`, `wp_quota`) to ensure backward compatibility and prevent user data loss across updates.
- Schema structure:
  - `user` (string): Target GitHub username.
  - `synced_at` (number): Epoch timestamp in milliseconds.
  - `repos` (array of objects):
    - `name` (string): Repository name.
    - `branch` (string): Default branch (e.g., "main").
    - `repo_url` (string): Web link to repository.
    - `upload_url` (string): Web link to GitHub web upload interface.
    - `zip_url` (string): Web link to raw zipball archive.
    - `files` (array of objects):
      - `path` (string): Relative file path within repository.
      - `raw_url` (string): Direct raw download link from raw.githubusercontent.com.

### B. CACHE INVALIDATION RULES
1. **Manual User Sync:** Clicking `[Check again now]` flushes active user cache and pulls live GitHub API payload.
2. **Account Switch:** Switching GitHub username initiates a fresh load for the specified account without clearing prior account caches.
3. **Automated Cron Sync:** GitHub Actions runs daily at 04:00 UTC to commit updated `repos.json` snapshots.

---

## PART 4: FEATURE ROADMAP & PIPELINE

### A. ACTIVE MILESTONES
- **v0.0.1 (Foundation & Crawler):**
  - Self-contained responsive single-page portal.
  - Zero-quota local fallback and scheduled Actions crawler.
  - On-demand recursive file tree explorer.
  - Live search filter and quota pill.
- **v0.1.0 (Navigator Rebrand & Crawler Alignment):**
  - Rebranded application from Waypoint to Navigator.
  - Updated crawler workflow identity to `navigator-bot` and `Navigator-Crawler`.
  - Preserved `wp_*` storage contract for backward compatibility.
- **v0.2.0 (Direct File Previewer & Utilities):**
  - In-browser code preview modal for text, script, and style files.
  - One-click clipboard copy for file URLs and contents.
  - File extension indicator badges.
- **v0.3.0 (Custom Presets & PAT Vault):**
  - Configurable quick-download shortcuts per repository.
  - Optional Personal Access Token (PAT) vault in settings for 5,000 req/hr limits.

---

## PART 5: GLOSSARY & REPO ROSTER

### A. GLOSSARY
- **ApR (API Request):** A single HTTP transaction with api.github.com.
- **Git Trees API:** GitHub endpoint (`/git/trees/{branch}?recursive=1`) returning an entire directory tree up to 100,000 entries in one request.
- **Raw Blob:** Raw file content hosted on raw.githubusercontent.com that downloads without rendering GitHub web UI.
- **Rate Limit Quota:** GitHub limit for unauthenticated client requests (60 requests/hour/IP) versus GitHub Actions server token (1,000 requests/hour/repo).

### B. REPOSITORY ROSTER
- `index.html`: Client web portal application.
- `repos.json`: Static snapshot repository roster.
- `.github/workflows/update.yml`: Scheduled repository crawler.