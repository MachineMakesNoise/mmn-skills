---
name: mmn-github-workflow
description: Manage GitHub anchor branches, task branches and stacks, publication, PRs, merges, and issue follow-up. Use whenever implementing an issue, ticket, or task (including `/implement #N`): load it before the first change to check how the current branch, anchor, and stack fit the workflow, and follow it through commit, push, and PR. Also use when setting up an anchor, creating or submitting PRs, merging PRs, or updating related issues.
---

# GitHub anchor, task PR, and issue workflow

Use this workflow for GitHub task implementation and anchor setup, including explicitly authorized non-stack arrangements. It requires Git, an authenticated GitHub CLI (`gh`) for PR operations, and the `github/gh-stack` CLI extension for stacked work. Check the applicable prerequisites before using them; if a required command or repository feature is unavailable, report the blocker instead of silently switching workflows. Before any push, submit, or sync, confirm the intended remote. If multiple remotes exist, name the remote explicitly in Git commands (for example, `git push <remote> <branch>`) and pass `--remote <name>` to `gh stack` commands that support it. Ask the user when the destination is ambiguous.

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

## Publication and completion

- Validated task work and anchor setup include committing the scoped changes, pushing, and creating or updating required PRs in the same workflow run. Publish authorized work automatically; do not ask again merely to commit, push, or submit it. Preserve unrelated changes and follow the Git safeguards above.
- A branch or stack waiver changes how publication happens, not whether it happens. Publication remains required unless the user explicitly waives it for the identified scope.
- For stacked work, use the submission procedure below. For authorized non-stack work, use `git push <remote> <branch>` and `gh pr create` or `gh pr edit` as applicable, verifying the intended base, head, diff, summary, and issue links. When the user authorizes direct default-branch edits, commit and push to the explicit remote without manufacturing a PR.
- Every required PR—anchor, stacked task, or authorized non-stack, new or reused—must be ready for review at publication completion. If an existing PR is a draft, run `gh pr ready <PR>`; verify each required PR with `gh pr view <PR> --json isDraft` and require `isDraft` to be `false`.
- Work is complete only after required publication succeeds and the remote branch and any required PRs are verified. A concrete safety, remote, tooling, or permission blocker leaves the work incomplete; report it rather than handing publication back as a suggested next step. Publication does not authorize merging; merge approval and prerequisites still apply.

## 1. Initialize an anchor

Run this only after the user designates the current branch as the anchor and names its target. Confirm the current branch, target, Git status, and any parent issue before changing history or publishing a PR.

- Inspect for an existing anchor PR whose base is the target and head is the anchor. Reuse a match and update its title or body if needed to reflect the scope and link the parent issue when one exists; if no match exists, create a PR after pushing the anchor below.
- If the anchor has no commits ahead of the target and the index is clean, initialize the anchor PR with an empty commit:

  ```bash
  git commit --allow-empty -m "chore: initialize anchor PR"
  ```

- Push the anchor with `git push <remote> <anchor>`. If no matching PR exists, create a PR in this workflow run with:

  ```bash
  gh pr create --base <target> --head <anchor> --title "<anchor summary>" --body "<summary and parent issue link, if any>"
  ```

  Do not stop after presenting the command. After creating or reusing the PR, inspect it and confirm the base, head, title, body, and parent issue link. Do not initialize the task stack as part of anchor creation.

**Done:** the anchor is pushed, the correct PR targets the named branch and has an accurate summary and parent issue link when applicable, and no task changes have been made on the anchor.

## 2. Implement on task branches

Before the first change, place the task in the current repository state: inspect the current branch, Git status, its PR and base, and `gh stack view`. Continue an existing task branch for the same task, add a layer for a separate task above the current stack, or start a stack on the anchor. When no anchor or target is designated, or the current branch is the default branch, ask before changing anything.

Unless the user explicitly waives the branch or stack arrangement, root each task stack on the anchor, including a stack with only one task. Start the first task branch with:

```bash
gh stack init --base <anchor> <task-branch>
```

For a user-designated separate task that belongs above the current stack, add a layer with `gh stack add <task-branch>`. Use the existing task branch and PR when continuing work on a task already in the stack. A task PR targets its immediate parent branch. When the whole stack has merged into the anchor, start the next task stack from the anchor.

Before committing, inspect the relevant task, parent, sibling, and related issues and repository artifacts. Include affected repository updates in the task commit, update related external issues before pushing or making another commit, and state what changed and why in the commit message. Keep each task and anchor PR title and body accurate, including relevant issue links when applicable. Update existing PRs with `gh pr edit <PR> --title "<title>" --body "<summary and issue link>"`; after submission, inspect every PR and execute `gh pr edit` for any stale title or body. Treat accurate PR summaries and issue links as required completion checks.

For stacked work, after validating task changes, commit them and execute this submission command in the same workflow run:

```bash
gh stack submit --auto --open
```

Running this command is part of implementing the task, not a suggested follow-up for the user. It pushes the stack and creates or updates PRs. Treat implementation as complete only after submission succeeds and you verify each PR with `gh stack view --json`, inspecting its base branch and diff to confirm that it contains only its task. When a lower layer changes, rebase and reconcile affected higher layers before pushing, then verify their PR diffs again. If submission fails or stacked PRs are unavailable, report the concrete blocker and leave the task incomplete rather than presenting the command as a next step for the user.

When addressing PR review comments, reply in each thread with the implemented fix and relevant commit, or explain why no change was made. Resolve the thread only after the agreed outcome is complete and any changes have been validated and pushed. Leave threads open when a decision or follow-up is still pending.

**Done:** publication and verification are complete under the authorized arrangement. For stacked work, each task has its own committed branch and PR targeting its immediate parent; the pushed stack is reconciled and its PR diffs are verified.

## 3. Merge and close

Merge only after GitHub user approval or an explicit user request to merge. Internal agent review does not authorize merging. An explicit user request to merge a PR is sufficient on its own, even when the PR is a draft or has no approving review, because GitHub does not let authors approve their own PRs. Mark a draft PR ready with `gh pr ready <PR>` before merging it. Confirm that the remaining repository merge prerequisites are satisfied, including required checks and branch protections; report any that block the merge rather than bypassing them. Once all task PRs have merged into the anchor, merge the anchor PR into the target only when that PR is separately authorized and its prerequisites are satisfied.

For a single task PR, use `gh pr merge`. For a stack containing multiple task PRs, merge the lowest task PR with `gh stack merge <lowest-task-PR-number> --yes`, then run `gh stack sync` and inspect the remaining layers before continuing. Do not use `gh pr merge` to merge a multi-PR stack.

After each merge, confirm which PRs actually merged before updating issues. For each confirmed merged PR:

- Inspect its task, parent, sibling, and other related issues. Update every affected issue with a comment linking the merged PR, naming the destination branch, and recording the resulting status or remaining work. Include previously established acceptance not already recorded; do not invent new acceptance criteria at merge time.
- Close an issue only when its full scope and established acceptance are satisfied. A completed task issue can close when its task PR merges into the anchor. Keep partially completed and tracking issues open, with their progress and remaining work updated; a related-issue link alone does not justify closure.
- After the anchor PR merges into the target, update its parent issue and close it if its full scope is complete. Keep a parent tracking the broader delivery open until that merge, even when all task issues have closed.

Verify the resulting issue states, including any automatic GitHub closures, and correct premature closures. If a required update or closure is blocked, report the affected issue and remaining action.

After a confirmed successful merge into the anchor or target, delete the merged source branch both locally and on the intended remote once no open PR or remaining stack layer depends on it. If dependencies remain, defer deletion until they are resolved. Retain the anchor branch until its PR merges into the target and the stack is finished. Verify both deletions; report any blocked cleanup.

**Done:** only authorized PRs have merged, every affected issue reflects the confirmed merges, completed issues are closed and unfinished issues remain open, merged source branches without remaining dependencies are deleted locally and remotely, and branches with remaining dependencies are preserved.
