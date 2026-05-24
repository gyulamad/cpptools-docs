# CI Fix Rules

## Pre-Flight: Ambiguity Resolution (NEW)

**Before attempting any fix, always ask yourself: "Do I understand the *intention* behind this code?"**

Intention clarification is **critical**. Without a clear grasp of what the implementation was *supposed* to do, you cannot reliably verify whether your fix actually resolves the issue — or introduces a new bug. Guessing at intent leads to fragile patches and recurring failures.

- If the failing test can be fixed confidently → proceed with agent mode (see below).
- **If not** — i.e., if it's unclear what behavior is expected, the implementation intent is ambiguous or un-verifiable:
  1. Check the `done/` folder for the latest task that touches this area and use it to infer intent.
  2. If no relevant context exists there → **ask the user** to clarify before making any changes.

> ⚠️ Never silently remove or rewrite tests whose intended behavior you cannot confirm via documentation, prior tasks, or code comments. When in doubt, escalate — don't guess.

---

## Normal Fix Flow (Agent Mode)

When ambiguity is resolved and the intent is clear:

1. Run the CI script with tail last 5 lines.
2. Does it pass? → git commit "test fixes" and then STOP (go to step 5).
3. Read the last error and fix it.
    Note: always proceed with last error first, that way if your fix changes the line numbers for the rest of the file 
    the CI command still shows the right line numbers from the previous iteration 
    but can make confusions if you do fixes below.
4. Go to step 1 → repeat.
5. Finish

