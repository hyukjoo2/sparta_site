# docs/COPILOT.md
# Matrix Project — Copilot Guide (Ground Rules)

## Goal
This repo is a small "Matrix" web app:
- Frontend (static): `public/`
- Node backend (REST): `server/`
- Python FastAPI (LLM/RAG): `app.py`
- Neo (virtual human) lives in: `server/src/neo/*` + `public/src/app/neoClient.js`

Copilot: **Prefer deterministic behavior** for UI actions.
LLM is a fallback; never rely on LLM for mission-critical routing.

---

## What Copilot MUST follow
### 1) UI Action = deterministic
User asks like:
- "자산현황 보여줘", "자산 상태", "히스토리 열어", "기록 보여줘"
→ must open History modal (tool) **without** LLM guessing.

Tools:
- OPEN_HISTORY_MODAL
- OPEN_OCO_CALC
- OPEN_CALCULATOR

### 2) Local router first, LLM second
Flow:
1) Local intent router (keyword/regex scoring)
2) If unclear → call LLM intent-only
3) If still unclear → call full chat_log analysis
4) Always parse tool call robustly, then run tool.

### 3) LLM hallucination is forbidden
When using chat_log:
- If not in log → answer "로그에 없음"
- If possible cite log ids (#123).

---

## Key Entry Points
Frontend:
- `public/index.html`
- `public/src/main.js` (bootstraps app)
- `public/src/app/aiPopup.js` (AI UI + routing + retry + tool execution)
- `public/src/app/neoClient.js` (Neo client)
- `public/src/features/history.js` + `public/src/app/historyTool.js` (History modal)

Backend (Node):
- `server/src/app.js` (Express)
- `server/src/api/historyRouter.js`
- `server/src/neo/neoRouter.js` (Neo API)
- `server/src/db.js` (DB connection)

Python:
- `app.py` (FastAPI LLM endpoint)
- `ingest_rag.py`, `ingest_pdf.py` (RAG ingestion)

---

## When adding features
- Add UI action? → update TOOL_NAMES + intent cases + local router
- Add new API endpoint? → update docs/API_CONTRACTS.md
- Add new natural language command? → add to docs/INTENT_CASES.md

---

## Don't do this
- Do not trigger modals by fuzzy LLM text only.
- Do not parse tool call by naive string equals only.
- Do not put huge contexts into a single message (keep under ~4000 chars).