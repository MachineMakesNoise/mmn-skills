---
name: mmn-find-work
description: Find actionable TODO-style and research markers in textual files, group them into reviewable work items, and optionally create them in a user-supplied issue platform with source cleanup. Use when asked to inventory, triage, group, or turn source markers into work items.
---

# Find marked work

Turn textual work markers into user-verified, independently actionable groups. A finding is actionable when it describes work whose completion would allow its marker and associated annotation to be removed.

## Marker vocabulary

The fixed, case-sensitive vocabulary is below. These meanings and completion signals are defaults; the annotation text and local context are authoritative.

### `TODO`

**Meaning:** General work remains to be done.

**Complete when:** The stated work is performed and verified sufficiently for the annotation to be removed.

### `FIXME`

**Meaning:** Code or content is known to be wrong and needs correction.

**Complete when:** The incorrect state is corrected and the intended result is verified.

### `HACK`

**Meaning:** A deliberate expedient workaround should be replaced or justified.

**Complete when:** A robust solution replaces the workaround, or the constraint is accepted and documented so the warning is no longer needed.

### `XXX`

**Meaning:** Something hazardous, surprising, or unresolved demands attention, and no more precise marker was chosen.

**Complete when:** The concern is resolved or replaced by a more precise annotation whose intended work is clear.

### `BUG`

**Meaning:** An observable defect violates expected behavior.

**Complete when:** Expected behavior is restored and verified against the reported defect.

### `OPTIMIZE`

**Meaning:** An opportunity exists to improve resource use or efficiency.

**Complete when:** The target is improved to an agreed threshold and the relevant behavior remains correct.

### `PERF`

**Meaning:** A concrete or suspected performance problem requires measurement and, when warranted, improvement.

**Complete when:** Measured behavior meets the relevant target, or evidence establishes that no change is warranted.

### `REVIEW`

**Meaning:** An evaluation or decision is needed.

**Complete when:** The review is performed and its conclusions or resulting actions are captured where needed.

### `REFACTOR`

**Meaning:** Internal structure should improve without intentionally changing behavior.

**Complete when:** The structural concern is resolved and preserved behavior is verified.

### `TEST`

**Meaning:** Test coverage or another form of validation is missing or inadequate.

**Complete when:** The needed validation exists and passes, or evidence establishes that it is unnecessary.

### `RESEARCH`

**Meaning:** A question, uncertainty, or stated subject requires investigation and reported findings.

**Complete when:** The marker's objective is satisfied with traceable evidence appropriate to the investigation: links or citations for external claims, paths and symbols for code findings, and commands and results for experiments. Findings are recorded in the resulting work item's durable record, or linked from it with a concise conclusion. The executor gives the requester a brief completion response.

## Questions in annotations

Across every marker type, an explicit question or a clear indirect question such as “whether,” “why,” “how,” “which,” or “determine if” is part of the marked work rather than incidental context. Include each question in the finding and resulting work item, preserve explicit wording when practical, and require the executor to answer it directly to the requester while performing the work. When the wording is uncertain, preserve it as an objective without inventing a question. A marker that asks no question gets no invented question-and-answer section.

## 1. Resolve the scope

Accept any mixture of files, directories, textual include/exclude patterns, and positions equivalent to `path:line`, `path:line:column`, or a start/end range. If no scope is supplied, use the current directory recursively.

Directory traversal respects repository ignores and conventional generated/vendor exclusions. An explicitly named file overrides those exclusions. Inclusion patterns nominate files but override ignores only when the user also requests ignored files. Explicit exclusions win over inclusions. Normalize overlapping inputs so a file is scanned once at the broadest independently requested scope.

When the user requests ignored files or the estimated scope is unusually large, show the estimated breadth and get confirmation. Do not follow symlinked directories by default; an explicitly named symlinked file is eligible after showing its resolved path.

A platform is optional, but it must be named at invocation to enable item creation and cleanup. It must resolve to one destination such as a repository, project, workspace, or queue. When no platform is supplied, this run is list-only; do not introduce one later in the run.

**Done:** the effective file scope, text-selection rules, and optional single platform destination are unambiguous.

## 2. Discover occurrences

Scan the resolved scope with the available file and search tools, preserving the candidate-file, skip, occurrence, and coverage information required below. For a large scope, use a reproducible command or temporary script to make coverage auditable. If a suitable helper agent is available, it may perform discovery over the exact resolved scope; verify its coverage and findings before classifying them.

Select ordinary textual files using known text extensions plus content probing for unknown or extensionless files. User inclusion patterns may nominate unfamiliar extensions. Exclude binary files and containers; do not unpack archives. Scan non-UTF-8 text only when it can be decoded and later preserved confidently.

Match each vocabulary token exactly and case-sensitively with non-word boundaries on both sides. Surrounding punctuation is allowed, as in `[TODO]`, `#TODO`, `TODO:`, or `TODO(owner)`; embedded or lowercase forms do not match.

For every occurrence record:

- repository-relative path;
- marker type;
- exact token start and end;
- the smallest contiguous annotation range expressing the work;
- the original annotation text.

A whole-line annotation may omit columns. For a mid-sentence marker, keep the smallest grammatical span containing the marker and action. Multiline text may continue through the same comment, paragraph, or list item; stop at blank separators, a new marker or list item, or unrelated prose unless continuity is clear.

Report unreadable, undecodable, oversized, excluded, and failed files instead of silently claiming complete coverage. Give counts for candidate files, scanned text files, skipped files, and raw occurrences by marker type.

**Done:** every eligible occurrence in scope is recorded or its omission is disclosed.

## 3. Classify with minimal context

Read only enough nearby text or code to identify the actionable subject, obvious relationships, and the annotation range. Do not turn discovery into architecture analysis, implementation planning, or issue refinement.

Classify occurrences as:

- **actionable** — concrete work can remove the annotation;
- **ambiguous** — intent or action is unclear, including a bare marker;
- **non-actionable** — an example, quoted token, vocabulary inventory, or policy reference;
- **apparently obsolete** — the marked work appears complete or irrelevant.

Keep questionable candidates visible for user review. Never silently discard them. Extract owners, dates, references, or tags only as candidate metadata; a parenthesized value is not automatically an assignee.

When an annotation contains several independent tasks, split them into findings. When several occurrences describe the same resolution, make one finding with all locations. One annotation such as `TODO/FIXME: replace parser` is one finding with multiple marker types unless it contains independently actionable work.

Treat unanswered questions as part of the actionable subject. Keep several questions in one finding when they belong to the same work; split them when they can be answered and closed independently.

A `RESEARCH` marker describes future work; classify and track it without performing the investigation during this run. When an annotation combines research and implementation, split them if the research is independently actionable; otherwise keep the work together.

**Done:** every occurrence has a visible classification and enough evidence for grouping or a user decision.

## 4. Build groups and cleanup cohorts

Assign stable session-local IDs such as `F001`, `G001`, and `C001`; never reuse retired IDs during the run.

A **group** is one independently assignable, implementable, verifiable, and closable unit of work. Group by shared resolution, root cause, or tightly coupled change—not by proximity or broad subsystem. Marker type is metadata, not a grouping boundary. Keep weakly related findings separate and ask the user when unsure. When separate research and implementation groups come from the same annotation, make the implementation depend on the research when its work cannot proceed confidently without the findings.

Use canonical relationships `depends-on`, `blocks`, `duplicates`, `overlaps`, and `conflicts-with`. Infer them from explicit text or strong local evidence, label confidence as confirmed/inferred/possible, and present possible relationships for decision. Treat dependency cycles as evidence to merge or ask the user.

A **cleanup cohort** is the full connected component of groups and annotations joined by a shared marker token or overlapping associated-text ranges. This may span several markers, locations, and groups. Same-line annotations with disjoint ranges remain independently cleanable. Never split a cleanup cohort across execution batches.

**Done:** every finding belongs to a proposed group, every group belongs to one cleanup cohort, and dependency and cleanup ownership conflicts are exposed.

## 5. Verify with the user

Discover and group the complete scope before review. Show every group. Order dependency roots before dependents; review ambiguous and non-actionable candidates last.

Default to one cleanup cohort per page and display progress such as `Cohort 2 of 7 (3 groups approved)`. The user may request a larger batch of independent cohorts. For each group show only what supports the decision: ID, concise proposed title and summary, marker types, all locations/ranges, grouping rationale, relationships, and open questions.

Offer approve, edit, split, merge, change relationships, cleanup-only for confirmed obsolete annotations, or skip. Skip and reject mean the same thing: leave source unchanged and create nothing. Defer also leaves the marker for a future run.

When groups may be duplicates or merge candidates, temporarily show the relevant groups together with their evidence and differences. Allow merging all, a subset, or none. A large comparison cluster starts with a compact table. When any edit changes grouping or dependencies, recompute cohort membership and ordering, then re-present materially changed approvals.

**Done:** every group has been shown and has an explicit user disposition; no uncertain merge, dependency, or cleanup range remains implicit.

## 6. Finish the selected branch

### List-only

Return approved groups as lightweight, provider-neutral drafts containing a concise imperative title, one short summary, source locations, and minimal relationships. Include a short excerpt only when those fields would otherwise be ambiguous. For each group containing questions, preserve them in the draft and require their answers as part of the work. For each group containing `RESEARCH`, also include a short research outcome carrying that marker's completion contract and phrased for recording findings in the resulting work item. These are the only exceptions to the lightweight payload: produce no other acceptance criteria, solution design, implementation steps, test plan, platform item, or source cleanup. Write Markdown or JSON only when requested.

### Platform supplied

Read [`PLATFORM.md`](PLATFORM.md) and execute its per-cohort creation and cleanup workflow. Platform access is lazy: first contact it only when a verified cohort reaches creation.

**Done:** the selected branch's completion condition is satisfied and the final status accounts for every group and skipped file.
