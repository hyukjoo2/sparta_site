# docs/STYLE_GUIDE.md
# Code Style & Safety Rules (for Copilot + contributors)

## JavaScript style
- Use `const` by default, `let` only if reassigned.
- Avoid deep nesting; extract helpers.
- Prefer pure functions for parsing/routing:
  - `tryParseToolCall(text)`
  - `routeIntent(q)` (deterministic)
- All user-visible strings should be Korean unless clearly internal logs.

## Error handling
- Frontend fetch:
  - Always check `res.ok`
  - On network error, show user-friendly message
- Never crash UI on JSON parse errors; treat as non-tool output.

## LLM usage rules
- LLM is a fallback, not a router.
- System prompt must explicitly:
  - forbid hallucination
  - require "로그에 없음" if missing
  - require JSON-only tool output for UI actions

## Context limits
- Single user message content <= ~3950 chars.
- Trim context from the end (most recent logs) first.

## Tool protocol
Tools are "UI actions", not backend actions:
- OPEN_HISTORY_MODAL
- OPEN_OCO_CALC
- OPEN_CALCULATOR

Tool output must be parsed robustly:
- pure JSON
- JSON embedded in text
- token-only tool name
- tool keyword inside a sentence (backup only)

## Security
- Never embed secrets in frontend.
- Endpoints/config in `public/src/app/constants.js`.
- Avoid logging user private data in console in production builds.