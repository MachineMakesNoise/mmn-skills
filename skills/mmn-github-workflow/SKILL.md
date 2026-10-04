---
name: mmn-github-workflow
description: Manage GitHub anchor branches, issue-based task branches and their PRs, merges, and issue follow-up. Use when implementing a task from an issue, setting up an anchor, creating or submitting task PRs, merging PRs, or updating related issues.
---

# GitHub anchor, task PR, and issue workflow

Use this workflow for a GitHub repository organized around an anchor branch and stacked task branches. It assumes Git, an authenticated GitHub CLI (`gh`), and the `github/gh-stack` CLI extension. Check that each prerequisite is available before using it; if a required command or repository feature is unavailable, report the blocker instead of silently switching workflows. Before any push, submit, or sync, confirm the intended remote. If multiple remotes exist, name the remote explicitly in Git commands (for example, `git push <remote> <branch>`) and pass `--remote <name>` to `gh stack` commands that support it. Ask the user when the destination is ambiguous.

Apply any explicit repository policy that governs the work. This skill does not require a particular `AGENTS.md`, another skill, or repository-specific setup document.

## Branch roles

- The **target branch** is the destination for the broader body of work. Use the branch named by the user; do not infer it from the repository default.
- The **anchor branch** has an anchor PR into the target and is the trunk for task stacks. It is not itself a task layer and stays open while its task PRs merge into it.
- Each **task branch** contains one reviewable task. Its PR targets the branch directly below it: the anchor for the bottom task, or the preceding task branch for higher layers.

## Git safeguards

- Use the current worktree unless the user explicitly instructs you to create or use a different isolated worktree.
- Stay on the current branch unless the user authorizes a switch or the transition is explicitly covered by the requested workflow. Ask when the destination or authorization is unclear.
- Working on the repository's default branch requires explicit user permission; selecting it as the target does not authorize implementation changes there. Keep implementation changes on task branches unless the user explicitly waives that constraint for the identified scope.
- Preserve unrelated working-tree changes. Keep each task branch focused on its reviewable task; include adjacent changes only for correctness, safety, consistency, or maintainability.
- When delegating work, pass the branch roles, authorized transitions, task scope, and applicable Git and merge constraints to every delegated agent. Delegation does not expand the authorization.

## 1. Initialize an anchor

Run this only after the user designates the current branch as the anchor and names its target. Confirm the current branch, target, Git status, and any parent issue before changing history or publishing a PR.

- Inspect for an existing anchor PR whose base is the target and head is the anchor. Reuse a match and update its title or body if needed to reflect the scope and link the parent issue when one exists; if no match exists, create a draft PR after pushing the anchor below.
- If the anchor has no commits ahead of the target and the index is clean, initialize the anchor PR with an empty commit:

  ```bash
  git commit --allow-empty -m "chore: initialize anchor PR"
  ```

- Push the anchor. If no matching PR exists, create a draft PR in this workflow run with:

  ```bash
  gh pr create --draft --base <target> --head <anchor> --title "<anchor summary>" --body "<summary and parent issue link, if any>"
  ```

  Do not stop after presenting the command. After creating or reusing the PR, inspect it and confirm the base, head, title, body, and parent issue link; confirm it is a draft when newly created. Do not initialize the task stack as part of anchor creation.

**Done:** the anchor is pushed, the correct PR targets the named branch and has an accurate summary and parent issue link when applicable, and no task changes have been made on the anchor.

## 2. Implement on task branches

Root each task stack on the anchor, including a stack with only one task. Start the first task branch with:

```bash
gh stack init --base <anchor> <task-branch>
```

For a user-designated separate task that belongs above the current stack, add a layer with `gh stack add <task-branch>`. Use the existing task branch and PR when continuing work on a task already in the stack. A task PR targets its immediate parent branch. When the whole stack has merged into the anchor, start the next task stack from the anchor.

Before committing, inspect the relevant task, parent, sibling, and related issues and repository artifacts. Include affected repository updates in the task commit, update related external issues before pushing or making another commit, and state what changed and why in the commit message. Keep each task and anchor PR title and body accurate, including relevant issue links when applicable. Update existing PRs with `gh pr edit <PR> --title "<title>" --body "<summary and issue link>"`; after submission, inspect every PR and execute `gh pr edit` for any stale title or body. Treat accurate PR summaries and issue links as required completion checks.

After validating task changes, commit them and execute this submission command in the same workflow run:

```bash
gh stack submit --auto --open
```

Running this command is part of implementing the task, not a suggested follow-up for the user. It pushes the stack and opens ready-for-review PRs. Treat implementation as complete only after submission succeeds and you verify each PR with `gh stack view --json`, inspecting its base branch and diff to confirm that it contains only its task. When a lower layer changes, rebase and reconcile affected higher layers before pushing, then verify their PR diffs again. If submission fails or stacked PRs are unavailable, report the concrete blocker and leave the task incomplete rather than presenting the command as a next step for the user.

When addressing PR review comments, reply in each thread with the implemented fix and relevant commit, or explain why no change was made. Resolve the thread only after the agreed outcome is complete and any changes have been validated and pushed. Leave threads open when a decision or follow-up is still pending.

**Done:** each task has its own committed branch and PR targeting its immediate parent; the pushed stack is reconciled and its PR diffs are verified.

## 3. Merge and close

Merge only after GitHub user approval or an explicit user request to merge. Internal agent review does not authorize merging. Confirm that all applicable repository merge prerequisites are satisfied, including required checks, reviews, and branch protections; user authorization does not bypass them. Once all task PRs have merged into the anchor, merge the anchor PR into the target only when that PR is separately authorized and its prerequisites are satisfied.

For a single task PR, use `gh pr merge`. For a stack containing multiple task PRs, merge the lowest task PR with `gh stack merge <lowest-task-PR-number> --yes`, then run `gh stack sync` and inspect the remaining layers before continuing. Do not use `gh pr merge` to merge a multi-PR stack.

After each merge, confirm which PRs actually merged before updating issues. For each confirmed merged PR:

- Inspect its task, parent, sibling, and other related issues. Update every affected issue with a comment linking the merged PR, naming the destination branch, and recording the resulting status or remaining work. Include previously established acceptance not already recorded; do not invent new acceptance criteria at merge time.
- Close an issue only when its full scope and established acceptance are satisfied. A completed task issue can close when its task PR merges into the anchor. Keep partially completed and tracking issues open, with their progress and remaining work updated; a related-issue link alone does not justify closure.
- After the anchor PR merges into the target, update its parent issue and close it if its full scope is complete. Keep a parent tracking the broader delivery open until that merge, even when all task issues have closed.

Verify the resulting issue states, including any automatic GitHub closures, and correct premature closures. If a required update or closure is blocked, report the affected issue and remaining action.

After a confirmed successful merge into the anchor or target, delete the merged source branch both locally and on the intended remote once no open PR or remaining stack layer depends on it. If dependencies remain, defer deletion until they are resolved. Retain the anchor branch until its PR merges into the target and the stack is finished. Verify both deletions; report any blocked cleanup.

**Done:** only authorized PRs have merged, every affected issue reflects the confirmed merges, completed issues are closed and unfinished issues remain open, merged source branches without remaining dependencies are deleted locally and remotely, and branches with remaining dependencies are preserved.
