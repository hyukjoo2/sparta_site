# Neo Engine – Virtual Being Design

Neo is a **stateful virtual entity**, not a chatbot.

---

## Location
server/src/neo/
├── neoEngine.js   # State machine
├── neoRepo.js     # Persistence
├── neoRouter.js   # API interface

---

## Neo’s Responsibilities

Neo can:
- Observe history
- Maintain internal state
- React to system events
- Influence UI indirectly

Neo cannot:
- Render UI
- Talk directly to DOM
- Execute frontend actions

---

## Design Principles

- Neo is slow and thoughtful
- Neo has memory
- Neo reacts, not commands

---

## State Model (Conceptual)
IDLE
↓
OBSERVING
↓
THINKING
↓
RESPONDING
↓
IDLE

States must be explicit.

---

## Neo + LLM Relationship

- LLM suggests
- Neo decides
- Neo’s state persists beyond requests

LLM is **not Neo**.
Neo may consult LLM.

---

## Forbidden Actions

❌ Neo directly calling UI  
❌ Neo depending on LLM availability  
❌ Neo storing UI state  

---

Neo is the soul of the system.
Treat it carefully.