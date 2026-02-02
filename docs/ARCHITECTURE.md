# docs/ARCHITECTURE.md
# Architecture Overview

## High-level
This project contains 3 runtime components:

1) Frontend (Static Web)
- Served as static files (or via server static)
- Location: `public/`
- JS modules: `public/src/*`

2) Node Backend (REST API)
- Express server
- Location: `server/src/*`
- Provides history, neo, and other APIs
- Connects to DB via `server/src/db.js`

3) Python FastAPI (LLM/RAG)
- Location: `app.py`
- Exposes LLM endpoint used by frontend AI popup
- May support system_prompt + messages payload

---

## Frontend module map
### Core UI shell
- `public/src/ui/menu.js` : menu bar + panels
- `public/src/ui/modal.js`: modal system used by tools/AI
- `public/src/ui/render.js`: UI rendering helpers

### App logic
- `public/src/app/constants.js`: endpoints, configs
- `public/src/app/api.js`: fetch wrappers for history/chat_log etc
- `public/src/app/historyTool.js`: History modal open logic
- `public/src/app/aiPopup.js`: AI chat UI + tool routing
- `public/src/app/neoClient.js`: Neo interaction client
- `public/src/app/llmChat.js`: LLM chat helper
- `public/src/app/matrixTool.js`: "Matrix" tool level helpers
- `public/src/app/utils.js`: shared helpers

### Features
- `public/src/features/history.js` : history view logic
- `public/src/features/ocoCalc.js` : OCO calculator modal
- `public/src/features/calculator.js` : calculator modal
- etc.

---

## Neo (virtual human) architecture
### Backend (authoritative)
- `server/src/neo/neoEngine.js`: main "brain" logic
- `server/src/neo/neoRepo.js`  : persistence/data access
- `server/src/neo/neoRouter.js`: Express routes for Neo
- `server/src/neo/index.js`    : module glue

### Frontend (client)
- `public/src/app/neoClient.js`: calls Neo API / manages request/response

---

## AI/LLM architecture (recommended)
### Routing layers
1) Local intent router (frontend): deterministic tool selection
2) LLM intent-only: classify tool vs normal chat
3) Full chat_log analysis: answer based on logs + cite ids

### Why
- Natural language variation (자산현황/자산 상태/자산현황 어때/내 돈 얼마야)
  is best handled by local router + test cases.
- LLM is used for "analysis", not for "button clicking".

---

## Data sources
- `chat_log` (DB table) or `public/data/chatlog.json` (seed)
- `price.json` (used by the UI world)
- RAG index built from PDF(s) via `ingest_*`

---

## Non-goals
- No background jobs inside frontend.
- No "silent" UI action without explicit user intent.