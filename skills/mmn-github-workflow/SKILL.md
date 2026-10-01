---
name: mmn-github-workflow
description: Manage GitHub anchor branches, stacked task PRs, and related issues. Use when setting up an anchor PR, implementing or submitting task branches in an anchor-based stack, merging authorized PRs, or updating and closing issues after a PR merges.
---

# GitHub anchor, task PR, and issue workflow

Use this workflow for a GitHub repository organized around an anchor branch and stacked task branches. It assumes Git, an authenticated GitHub CLI (`gh`), and the `github/gh-stack` CLI extension. Check that each prerequisite is available before using it; if a required command or repository feature is unavailable, report the blocker instead of silently switching workflows. Before any push, submit, or sync, confirm the intended remote. If multiple remotes exist, name the remote explicitly in Git commands (for example, `git push <remote> <branch>`) and pass `--remote <name>` to `gh stack` commands that support it. Ask the user when the destination is ambiguous.

Apply any explicit repository policy that governs the work. This skill does not require a particular `AGENTS.md`, another skill, or repository-specific setup document.

## Branch roles

- The **target branch** is the destination for the broader body of work. Use the branch named by the user; do not infer it from the repository default.
- The **anchor branch** has an anchor PR into the target and is the trunk for task stacks. It is not itself a task layer and stays open while its task PRs merge into it.
- Each **task branch** contains one reviewable task. Its PR targets the branch directly below it: the anchor for the bottom task, or the preceding task branch for higher layers.

## Git safeguards

- Stay on the current branch unless the user authorizes a switch or the transition is explicitly covered by the requested workflow. Ask when the destination or authorization is unclear.
- Working on the repository's default branch requires explicit user permission; selecting it as the target does not authorize implementation changes there. Keep implementation changes on task branches unless the user explicitly waives that constraint for the identified scope.
- Preserve unrelated working-tree changes. Keep each task branch focused on its reviewable task; include adjacent changes only for correctness, safety, consistency, or maintainability.
- When delegating work, pass the branch roles, authorized transitions, task scope, and applicable Git and merge constraints to every delegated agent. Delegation does not expand the authorization.

## 1. Initialize an anchor

Run this only after the user designates the current branch as the anchor and names its target. Confirm the current branch, target, Git status, and any parent issue before changing history or publishing a PR.

- Reuse a matching anchor PR or create a draft PR from the anchor into the target. Include the parent issue link when one exists, and keep it current if that issue changes.
- If the anchor has no commits ahead of the target and the index is clean, initialize the anchor PR with an empty commit:

  ```bash
  git commit --allow-empty -m "chore: initialize anchor PR"
  ```

- Push the anchor before creating the draft PR. Do not initialize the task stack as part of anchor creation.

**Done:** the correct anchor PR targets the named branch, any existing parent issue is linked, and no task changes have been made on the anchor.

## 2. Implement on task branches

Root each task stack on the anchor, including a stack with only one task. Start the first task branch with:

```bash
gh stack init --base <anchor> <task-branch>
```

For a user-designated separate task that belongs above the current stack, add a layer with `gh stack add <task-branch>`. Use the existing task branch and PR when continuing work on a task already in the stack. A task PR targets its immediate parent branch. When the whole stack has merged into the anchor, start the next task stack from the anchor.

Before committing, inspect the relevant task, parent, sibling, and related issues and repository artifacts. Include affected repository updates in the task commit, update related external issues before pushing or making another commit, and state what changed and why in the commit message. Keep task and anchor PR summaries accurate.

After task changes are validated, commit them and submit the stack with:

```bash
gh stack submit --auto --open
```

This pushes the stack and opens ready-for-review PRs. After submission, run `gh stack view --json` and inspect each PR's base branch and diff to confirm that it contains only its task. When a lower layer changes, rebase and reconcile affected higher layers before pushing, then verify their PR diffs again. If stacked PRs are unavailable in the repository, stop and report that blocker rather than submitting ordinary independent PRs.

**Done:** each task has its own committed branch and PR targeting its immediate parent; the pushed stack is reconciled and its PR diffs are verified.

## 3. Merge and close

Merge only after GitHub user approval or an explicit user request to merge. Internal agent review does not authorize merging. Confirm that all applicable repository merge prerequisites are satisfied, including required checks, reviews, and branch protections; user authorization does not bypass them. Once all task PRs have merged into the anchor, merge the anchor PR into the target only when that PR is separately authorized and its prerequisites are satisfied.

For a single task PR, use `gh pr merge`. For a stack containing multiple task PRs, merge the lowest task PR with `gh stack merge <lowest-task-PR-number> --yes`, then run `gh stack sync` and inspect the remaining layers before continuing. Do not use `gh pr merge` to merge a multi-PR stack.

After each merge, confirm which PRs actually merged before updating issues. For each confirmed merged PR:

- Inspect its task, parent, sibling, and other related issues. Update every affected issue with a comment linking the merged PR, naming the destination branch, and recording the resulting status or remaining work. Include previously established acceptance not already recorded; do not invent new acceptance criteria at merge time.
- Close an issue only when its full scope and established acceptance are satisfied. A completed task issue can close when its task PR merges into the anchor. Keep partially completed and tracking issues open, with their progress and remaining work updated; a related-issue link alone does not justify closure.
- After the anchor PR merges into the target, update its parent issue and close it if its full scope is complete. Keep a parent tracking the broader delivery open until that merge, even when all task issues have closed.

Verify the resulting issue states, including any automatic GitHub closures, and correct premature closures. If a required update or closure is blocked, report the affected issue and remaining action.

Delete a merged source branch locally or remotely only when no open PR or remaining stack layer depends on it. Retain the anchor branch until its PR merges and the stack is finished.

**Done:** only authorized PRs have merged, every affected issue reflects the confirmed merges, completed issues are closed and unfinished issues remain open, and branches with remaining dependencies are preserved.
