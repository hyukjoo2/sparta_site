# docs/API_CONTRACTS.md
# API Contracts

This document defines the stable API contracts used by the frontend.

## 1) LLM Endpoint (Python FastAPI)
### Endpoint
- `POST {LLM_ENDPOINT}` from `public/src/app/constants.js`

### Request (expected)
```json
{
  "system_prompt": "string (optional)",
  "messages": [
    { "role": "user", "content": "string" }
  ]
}

Response (expected)
{
  "content": "string"
}

Tool calling protocol (frontend-parsed)

If a UI action is needed, model must output ONLY one of:
{"tool":"OPEN_HISTORY_MODAL"}
{"tool":"OPEN_OCO_CALC"}
{"tool":"OPEN_CALCULATOR"}

No explanation text, no code fences.

Frontend must still handle messy outputs safely (JSON inside text, token-only, etc).

2) Node Backend (Express)

Base path depends on server/src/app.js configuration.

History APIs

(See: server/src/api/historyRouter.js)

Typical usage patterns:
	•	Get recent history rows
	•	Search history rows by query

Frontend wrapper:
	•	public/src/app/api.js
	•	apiGetChatLogRecent(limit)
	•	apiSearchChatLog(query, limit)

Important
	•	Return arrays; frontend expects an array of rows.
	•	Row fields commonly used by UI:
	•	id
	•	created_at (ISO string)
	•	message (string)

⸻

3) Neo APIs

(See: server/src/neo/neoRouter.js)

Frontend caller:
	•	public/src/app/neoClient.js

Guidelines:
	•	Keep Neo endpoints consistent (JSON in/out)
	•	Avoid coupling Neo response format to a single UI component.

    ---

```md
# docs/INTENT_CASES.md
# Intent Test Cases (Natural Language → Tool/Behavior)

Purpose:
- Prevent LLM from being the single point of failure.
- Drive a deterministic local router and regression tests.
- Improve Copilot suggestions with explicit examples.

## Tools
- OPEN_HISTORY_MODAL: asset/history view
- OPEN_OCO_CALC: OCO calculator
- OPEN_CALCULATOR: normal calculator
- NONE: normal chat response (no UI modal)

---

## A) History / Asset status → OPEN_HISTORY_MODAL

### Strong (must route to tool)
- "자산현황 보여줘"
- "자산현황"
- "자산 상태 보여줘"
- "자산 상태 어때?"
- "내 자산 좀 보여줘"
- "내 돈 얼마야"
- "오늘 자산 현황"
- "수익률 보여줘"
- "손익 보여줘"
- "히스토리 열어"
- "기록 보여줘"
- "거래 기록 보여줘"
- "내 기록 열어줘"
- "history 열어"
- "자산 현황 모달 열어"
- "자산창 열어"
- "현황창 열어"

### Medium (still tool)
- "요즘 얼마나 벌었지?"
- "내가 마지막으로 뭐 샀더라?"
- "최근 로그 보여줘"
- "최근 기록 보여줘"
- "최근 대화 로그"
- "최근 채팅 로그"
- "최근 자산변화"

Expected:
- Tool: OPEN_HISTORY_MODAL
- No LLM required (local router must catch)

---

## B) OCO calculator → OPEN_OCO_CALC
### Strong
- "OCO 계산 열어줘"
- "오코 계산"
- "오코 계산기"
- "OCO"
- "익절/손절 계산"
- "TP SL 계산"
- "스탑로스 계산"
- "익절가/손절가 계산"
- "오코 모달 열어"
- "oco calc"

Expected:
- Tool: OPEN_OCO_CALC

---

## C) Calculator → OPEN_CALCULATOR
### Strong
- "계산기 열어줘"
- "계산기"
- "calc"
- "일반 계산"
- "숫자 계산"
- "연산기"

Expected:
- Tool: OPEN_CALCULATOR

---

## D) Not a tool (NONE) — normal chat answer
### Examples
- "오늘 기분이 별로야"
- "내 전략 점검해줘"
- "이 로그 의미가 뭐야?"
- "네오가 왜 이런 말을 했지?"
- "이 코드 리팩토링 해줘"
- "RAG 인덱싱 어떻게 해야 해?"

Expected:
- No tool; answer text.

---

## E) Ambiguous → ask a follow-up (or LLM intent-only)
### Examples
- "열어줘" (what?)
- "보여줘" (what?)
- "그거 보여줘" (what?)

Expected:
- Prefer follow-up question OR LLM intent-only classify
- Must not randomly open modals.