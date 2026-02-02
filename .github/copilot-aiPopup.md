# aiPopup.js – AI Intent Router Guide

This file is the **brain of AI interaction**.

It is NOT:
- a chat UI
- a chatbot frontend

It IS:
- an intent router
- an LLM response validator
- a UI action dispatcher

---

## Responsibilities

aiPopup.js handles:

1. Local deterministic intent routing
2. LLM intent-only fallback
3. Retry / guard logic
4. UI action execution

---

## Supported UI Tools

These are frontend-only actions:

- OPEN_HISTORY_MODAL
- OPEN_OCO_CALC
- OPEN_CALCULATOR

No backend call is required to open them.

---

## Mandatory Flow
User Input
→ Local Router (keywords)
→ If uncertain → LLM (intent-only)
→ Parse + validate response
→ Execute UI action

---

## Golden Rules

- Never trust LLM output blindly
- Always parse defensively
- LLM output may be:
  - JSON
  - text + JSON
  - keyword only
  - garbage

---

## Parsing Strategy

Must support:
- Pure JSON
- JSON inside text
- Tool name alone
- Tool name inside sentence

---

## Retry Strategy

- Local routing first
- LLM once
- If LLM fails → normal chat response
- Never infinite retry

---

## UI Rule

If a tool is executed:
- Close menus
- Close AI panel
- Execute exactly ONE action

---

aiPopup.js is the **gatekeeper**.
Do not let chaos in.