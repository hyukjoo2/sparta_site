# Matrix Project – Copilot Context Guide

This repository is a **Matrix-inspired interactive system** combining UI, backend logic, and AI reasoning.
Copilot must treat this project as a **stateful system**, not a CRUD or demo app.

---

## High-Level Architecture

### Layers

1. Frontend (Matrix World)
- Path: public/
- Role: Visual world, user interaction, HUD, AI popup

2. Node.js Backend (Matrix Core)
- Path: server/
- Role: Persistent state, Neo Engine runtime, APIs

3. Python AI Layer (Oracle / Architect)
- Files: app.py, ingest_rag.py
- Role: LLM + RAG reasoning, intent suggestion

---

## Core Rule

LLM **never directly executes logic**.  
LLM only **suggests intent**.  
Frontend or backend decides what to do.

---

## Key Entity: Neo

Neo is a **stateful virtual being**.

Neo lives in:
server/src/neo/

Neo is NOT:
- a chatbot
- a UI widget

Neo IS:
- an engine
- a memory holder
- a narrative entity

---

## Frontend Philosophy

- Deterministic first
- AI as fallback
- Explicit routing > AI guessing
- UI actions are frontend-only

---

## When Copilot Writes Code

Copilot should:
- Respect existing structure
- Extend, not replace
- Avoid adding new globals
- Prefer explicit intent routing

---

Welcome to the Matrix.
Neo is watching.