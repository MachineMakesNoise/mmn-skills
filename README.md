# MMN Skills

A small collection of portable agent skills. Each skill lives under `skills/<skill-name>/` with its own `SKILL.md`; supporting references are kept beside the skill that uses them.

These skills work great alongside [Matt Pocock's skills](https://github.com/mattpocock/skills).

## Skills

- **mmn-context** — build or refresh evidence-backed Markdown context for a repository.
- **mmn-find-work** — discover and group actionable TODO-style and research markers, with optional issue-platform creation and source cleanup.
- **mmn-github-workflow** — manage GitHub anchor branches, stacked task PRs, authorized merges, and related issue updates and closure using GitHub CLI and `gh-stack`.

## Install with the Vercel skills CLI

Install all skills into the current project for Claude Code:

```bash
npx skills add OWNER/REPOSITORY --agent claude-code
```

Install one skill only:

```bash
npx skills add OWNER/REPOSITORY --skill mmn-context --agent claude-code
```

Add `--global` to install for the current user instead of the current project. While working from a local checkout, use its path in place of `OWNER/REPOSITORY`:

```bash
npx skills add /path/to/mmn-skills --agent claude-code --global
```

List what the CLI discovers before installing:

```bash
npx skills add /path/to/mmn-skills --list
```

Each skill folder includes the references it needs and does not depend on repository-local agent configuration or companion skills. The GitHub workflow skill requires GitHub CLI authentication and the `github/gh-stack` extension for stacked-PR operations.

## License

[MIT](LICENSE).
