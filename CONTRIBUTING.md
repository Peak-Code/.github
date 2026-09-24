# Contributing

Short version of how we work. The full guide:
**[How we work with Git](https://github.com/Peak-Code/.github/blob/main/docs/git-workflow.md)**

## Making a change

1. **Start from an issue** when there is one (use the Bug report, Feature request or Task form).
2. **Branch from `main`**, named `type/short-description`:
   `feat/property-search`, `fix/login-error`, `docs/setup-guide`
   (types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `build`, `ci`, `perf`).
3. **Commit as you like**, push, and open a Pull Request (Draft is fine).
4. **Give the PR a Conventional Commit title**: `type(scope): short description`,
   e.g. `feat(properties): add search`. We squash-merge, so this title becomes the commit on `main`.
5. **Fill in the PR template** and link the issue with `Closes #123`.
6. **Wait for the checks and a review**, then **Squash and merge**. Your branch is deleted automatically.

## Ground rules

- Never push directly to `main`.
- Keep PRs small, tested and focused on one thing.
- Don't merge with red checks.

## Questions?

Ask in the team chat or open a Task issue.

<!--
NOTE FOR MAINTAINERS
- This file is shown in every repo of the organization that has no CONTRIBUTING.md of its own.
- Relative links would break in those repos, so always use absolute URLs.
-->
