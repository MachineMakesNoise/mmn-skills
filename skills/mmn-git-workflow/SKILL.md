---
name: mmn-git-workflow
description: Manage an anchor branch and stacked GitHub task pull requests, from anchor setup through task submission and authorized merging. Use when the user requests this branch and PR workflow.
---

# Anchor and task PR workflow

Use this workflow for a GitHub repository organized around an anchor branch and stacked task branches. It assumes Git, an authenticated GitHub CLI (`gh`), and the `github/gh-stack` CLI extension. Check that each prerequisite is available before using it; if a required command or repository feature is unavailable, report the blocker instead of silently switching workflows. Before any push, submit, or sync, confirm the intended remote. If multiple remotes exist, name the remote explicitly in Git commands (for example, `git push <remote> <branch>`) and pass `--remote <name>` to `gh stack` commands that support it. Ask the user when the destination is ambiguous.

Apply any explicit repository policy that governs the work. This skill does not require a particular `AGENTS.md`, another skill, or repository-specific setup document.

## Branch roles

- The **target branch** is the destination for the broader body of work. Use the branch named by the user; do not infer it from the repository default.
- The **anchor branch** has an anchor PR into the target and is the trunk for task stacks. It is not itself a task layer and stays open while its task PRs merge into it.
- Each **task branch** contains one reviewable task. Its PR targets the branch directly below it: the anchor for the bottom task, or the preceding task branch for higher layers.

Keep implementation changes on task branches. Preserve unrelated working-tree changes, and do not merge a PR without GitHub user approval or an explicit request to merge.

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

Merge only after GitHub user approval or an explicit user request. For a single task PR, use `gh pr merge`. For a stack containing multiple task PRs, merge the lowest task PR with `gh stack merge <lowest-task-PR-number> --yes`, then run `gh stack sync` and inspect the remaining layers before continuing. Do not use `gh pr merge` to merge a multi-PR stack.

After each merge, confirm which PRs actually merged before updating issues. For each merged task PR, comment on its task issue with the merge and previously established acceptance not already recorded, then close the issue. After the anchor PR merges, do the same for its parent issue. Treat acceptance as previously established; do not invent new acceptance criteria at merge time.

Delete a merged source branch locally or remotely only when no open PR or remaining stack layer depends on it. Retain the anchor branch until its PR merges and the stack is finished.

**Done:** only authorized PRs have merged, issue updates reflect confirmed merges, and branches with remaining dependencies are preserved.
