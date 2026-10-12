---
name: mmn-integration-review
description: Delegate an explicitly requested whole-PR, branch, or complete-change integration review to a fresh read-only worker that discovers the issue, plans, and solution context.
---

# MMN integration review

Use only for an explicitly requested whole-change integration review, distinct
from routine post-TDD implementation review.

1. If the repository or requested change is ambiguous, ask the user to identify
   it. Pass the requested repository, PR/ref, comparison target if stated, review
   objective and constraints verbatim, and existing issue, plan, or task references.
   The caller need not investigate or pre-digest the solution.
2. Delegate through the host's native mechanism into a fresh, read-only context.
   Prefer a named `integration-review` adapter when available; otherwise use a
   suitable native reviewer or general-purpose agent with the read-only contract
   in [REVIEW.md](REVIEW.md) and capability to delegate fresh axis children.
   Pass the full bundled procedure or an absolute path accessible to the worker.
   Require the external `code-review` skill and nested review capability. Missing
   dependencies, unavailable delegation, or dispatch failure are blockers, not
   permission to review in the caller.
3. Await the worker's report. Resolve escalated scope and product decisions with
   the user; convey the verdict, validation, and separate Standards and Spec
   reports without merging or reranking the axes.

**Done:** the delegated report is delivered, or the exact blocker is reported.
