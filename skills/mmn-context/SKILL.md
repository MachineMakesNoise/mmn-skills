---
name: mmn-context
description: Create or refresh a repository's Markdown context through evidence-backed inspection and a lightweight guided interview.
disable-model-invocation: true
---

# Maintain repository context

Create or update the smallest useful set of human-owned project documents. Output is Markdown context only; leave source code, project tooling, and Git history unchanged. Treat trailing invocation text as an optional project brief.

## 1. Resolve the scope

Use the nearest Git root as the target. If none exists, show the current directory and ask whether to treat it as the target. When workspace or directory evidence makes a monorepo scope ambiguous, ask whether this run covers the repository root or one subproject.

When the target is a Git repository, snapshot Git status before analysis. In a non-Git target, record existing candidate paths before writing. Preserve unrelated changes. Editing an existing, modified, or untracked candidate document requires explicit, path-specific approval in the write plan.

**Done:** one target scope is confirmed and every protected path is known.

## 2. Infer before asking

Make one bounded, read-only evidence pass over:

- the top-level tree and important subdirectories;
- manifests, scripts, and dependency metadata;
- entry points, public interfaces, tests, and configuration;
- existing project documentation;
- a small recent-history sample when Git history exists.

Deepen only where a material uncertainty remains. Use repository evidence and user input by default; use external research only when the user requests it.

Build a concise project model covering what the repository is for, who uses it, its current or planned scope, domain language, architecture, development workflow, constraints, security concerns, and settled trade-off decisions. Separate current evidence from proposed intent. Surface material contradictions between code, documents, and user input instead of choosing silently.

For an empty or context-poor target, obtain a plain-language purpose from the user before proceeding. The user may leave every other detail unknown.

**Done:** the purpose and remaining high-impact uncertainties are explicit.

## 3. Run the guided loop

Ask only questions that improve the top-level model or determine whether a document has distinct value. Do not ask for facts the evidence pass already established.

In each round:

1. Show the concise current model, distinguishing verified facts, accepted plans, and unresolved points.
2. Ask one to four related, high-impact questions, moving from overview to specifics.
3. Use a structured question tool when available; put the evidence-backed recommendation first and offer an unknown, skip, or use-the-suggestion path when useful. Otherwise ask concise prose questions.
4. Incorporate the answers and recompute only the remaining material uncertainties.

Unknown details are omittable; exhaustive knowledge is not the goal. Continue until the model contains:

- a clear purpose;
- an intended user or use;
- current or planned scope boundaries;
- enough supported detail to route useful documents.

When those conditions hold, read [Document routing](DOCUMENTS.md) and build a file plan. For each candidate path, show `create`, `update`, or `skip`, plus its proposed sections and key additions, corrections, or removals. Flag protected paths. Keep this an outline rather than drafting complete files.

When genuine bounded-context boundaries or substantial decision work exceed this lightweight interview, pause and ask whether the user wants to expand the scope or finish with the supported model so far. Do not invent a formal model or decision record as a substitute for missing evidence.

State that the plan is ready and offer three outcomes:

- **Write now** — approve the displayed model and file plan;
- **Continue refining** — ask the next useful questions, even though the minimum is met;
- **Stop** — finish without filesystem changes.

Treat an abandoned questionnaire as Stop unless the user says to continue. Choosing Write approves verifiable repository facts and the ideas visible in the accepted model, never an unshown guess.

**Done:** the user explicitly chose Write for one fully displayed plan, or chose Stop.

## 4. Apply the plan

Use the accepted file plan and `DOCUMENTS.md` to guide the prose and structure; no separate document-writing skill is required.

On Write, apply only approved actions and the rules in `DOCUMENTS.md`:

- preserve the structure and voice of existing files;
- make only the targeted additions, corrections, and removals in the accepted plan;
- write supported or confirmed statements and omit unresolved detail;
- keep each fact in one authoritative home and link to it elsewhere;
- create directories and specialized documents only when their first substantive content is ready;
- add no generator banners or managed-section markers.

A repeated run is an idempotent audit: reconcile confirmed drift by correcting stale statements, removing obsolete content, and adding missing value without duplicating sections or links. If the accepted plan yields no valuable change, finish successfully with a no-op report.

Leave files unstaged. Staging, commits, pushes, and pull requests require a separate explicit request.

**Done:** every approved action is created, updated, or accounted for as an intentional no-op.

## 5. Validate and report

Re-read every changed document and inspect the complete diff. Verify that:

- every change is substantive, evidence-backed or confirmed, and routed according to `DOCUMENTS.md`;
- current and planned states are distinguishable;
- content outside the approved updates and unrelated working-tree changes remain intact;
- links resolve and Markdown diagnostics pass when available;
- no duplicate sections, unsupported claims, or placeholder-only files were introduced.

Report created, updated, and skipped files; explain material skip reasons; list useful unresolved gaps; and state that Git operations were not performed.

**Done:** all checks pass and every planned path and unresolved gap appears in the final status.
