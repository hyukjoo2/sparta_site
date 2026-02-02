# LLM Stability & Failure-First Design

LLM output is unreliable by nature.
This project is designed to **survive LLM failure**.

---

## Core Principle

The system must remain functional if:
- LLM is slow
- LLM is wrong
- LLM is unavailable

---

## Design Rules

1. Deterministic logic first
2. LLM as suggestion, not authority
3. Validate everything
4. Never assume format correctness

---

## Allowed LLM Roles

- Intent suggestion
- Text summarization
- Log analysis

---

## Forbidden LLM Roles

❌ Business logic execution  
❌ State mutation  
❌ UI control  

---

## Defensive Patterns

- Strict JSON parsing
- Keyword fallback
- Timeout handling
- Graceful degradation

---

## Ideal Failure Behavior

If LLM fails:
- UI still works
- Menus still open
- History still viewable
- User informed politely

---

## Philosophy

LLM is a **guest** in the Matrix.
The system must not depend on guests to survive.

---

Failure is expected.
Stability is engineered.