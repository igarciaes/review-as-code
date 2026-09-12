# R042-F001 — Feedback thread

**Finding:** [R042-F001](../reviews/R042.md) — Missing request timeout

## FB001 — Clarification

**Author:** Reviewer
**Date:** 2026-09-07

Does the shared HTTP client already expose a timeout option, or must we add one?

## FB002 — Response

**Author:** Implementer
**Date:** 2026-09-07

The shared client exposes `timeoutMs`; we can set it at the call site.

## FB003 — Confirmation

**Author:** Reviewer
**Date:** 2026-09-07

Setting it at the call site satisfies the recommendation. Decision recorded as Accepted in the review record.