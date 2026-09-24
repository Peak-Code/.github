# How we work with Git

Branch names, commit messages, Pull Requests and issues.
(Releases and deployments are **not** covered yet. They will get their own document.)

> Branch from `main` -> open a PR with a Conventional Commit title -> checks + review -> **squash-merge**.

---

## 1. The one rule

**`main` is always in a working state.** Everything below exists to protect that.

Unfinished work can be merged as long as it doesn't break anything and users can't reach it (not wired up yet, or behind a feature flag).

---

## 2. Branches

There is **one permanent branch: `main`**. Every other branch is short-lived (days, not weeks) and is deleted after merging.

### Naming

```text
<type>/<short-description>
<type>/<issue-number>-<short-description>
```

- The `type` is one of the commit types in section 4.
- Lowercase only; words separated by hyphens; no spaces.
- Optional issue number first (`feat/123-user-search` for issue #123).
- Keep it short and descriptive.

```text
feat/user-search
feat/123-user-search
fix/user-age-rounding
refactor/user-service
test/user-service
docs/setup-guide
chore/update-dependencies
build/update-dotnet-sdk
ci/pr-checks
perf/search-query
```

Not OK:

```text
Feature/UserSearch          (uppercase, wrong type)
my-branch                   (no type)
feat/add user search        (spaces)
feat_user_search            (underscore instead of "/")
```

A check on every PR fails if the branch name doesn't match this pattern (section 7).

---

## 3. Day-to-day flow

```bash
git switch main
git pull
git switch -c feat/user-search

# work, commit as often as you like
git push -u origin HEAD
# open a Pull Request (Draft is fine while you work)
```

If `main` moved while you were working: click **Update branch** on the PR, or run `git merge origin/main`. Rebasing is fine on a branch only you use. Never rewrite history on a branch someone else is using.

---

## 4. Commit messages and PR titles

We use [Conventional Commits](https://www.conventionalcommits.org/):

```text
type(scope): short description
```

- **type**: what kind of change (table below).
- **scope** (optional): the feature area in parentheses, e.g. `users`, `auth`. An area of the product, not a file or a layer.
- **description**: imperative and lowercase, no period at the end: "add", not "added" or "adds".

Because we **squash-merge**, each PR becomes exactly one commit on `main`, and **the PR title is that commit's message**. So:

- The **PR title must follow this format** (checked automatically).
- Commits inside your branch can be informal. They disappear when squashed.

| Type | Use it for |
|---|---|
| `feat` | New functionality |
| `fix` | Bug fix |
| `refactor` | Restructuring without behavior change |
| `test` | Adding or changing tests only |
| `docs` | Documentation only |
| `chore` | Maintenance (dependencies, cleanup) |
| `build` | Build system or tooling (SDK, project files) |
| `ci` | CI/CD and GitHub Actions |
| `perf` | Performance improvement |

Good:

```text
feat(users): add user search
fix(auth): handle expired refresh token
refactor(users): simplify user service
test(users): add user creation tests
docs: update setup instructions
chore: update dependencies
feat!: replace authentication contract
```

Not OK:

```text
Added search                  (no type)
feat: Add search.             (capital letter and period; the check allows it but reviewers will ask you to fix it)
feature(users): search        ("feature" is not a type; use "feat")
fix : login                   (space before the colon)
```

A `!` before the colon marks a **breaking change**: `feat!: replace authentication contract`.

---

## 5. Pull Requests

Nobody pushes directly to `main`. Every change goes through a PR.

**Before merging, a PR needs:**

- All automatic checks green
- At least 1 approval (see "Working alone?" in section 8)
- All review conversations resolved

**Rules of thumb:**

- Small enough to review in one sitting (20 minutes or so). Split big changes.
- Open it as **Draft** early if you want feedback. Mark it **Ready for review** when the checks are green and you have re-read your own diff.
- Fill in the PR template: what changed, why, how it was tested.
- Link the issue: `Closes #123` in the description closes it automatically when the PR is merged.
- Reviewers answer within one working day. Review behavior, tests, naming and safety (no secrets, no surprises), not personal taste.
- The **author merges**, using **Squash and merge** (the only enabled option). The branch is deleted automatically.

---

## 6. Issues

Use the issue forms; they exist so that reports contain what we need.

| Form | Use it for |
|---|---|
| **Bug report** | Something doesn't work as expected |
| **Feature request** | New behavior for users, with acceptance criteria |
| **Task** | Refactoring, chores, docs, tests, CI: work that isn't a bug or a feature |

One issue = one problem or one piece of work. Reference it from the PR that solves it.

---

## 7. What is checked automatically

| Check | Where it's set up | What it does |
|---|---|---|
| **Branch name** | `.github/workflows/pr-checks.yml` in each repo | Fails if the branch isn't `type/description` |
| **PR title** | same file | Fails if the title isn't a Conventional Commit |
| **Approval + checks before merge** | Repository ruleset on `main` | Blocks merging until everything is green |
| **No direct pushes, no force-push** | Repository ruleset on `main` | Blocks them |
| **Squash only, title as message** | Repository settings | Merge button offers only "Squash and merge" |

Build and test checks will be added when the CI workflow exists.

---

## 8. Rules at a glance

**Do**

- Branch from `main`; keep branches short and short-lived
- Name branches `type/short-description`
- Give every PR a Conventional Commit title
- Keep PRs small, tested and linked to an issue
- Delete the branch after merging (automatic)

**Don't**

- Push to `main` directly
- Rewrite history on shared branches
- Merge with red checks
- Create extra permanent branches (`develop`, `staging`, ...)

**Working alone?** You can't approve your own PR, so set required approvals to `0` until a second developer joins. Keep "PR required" and the checks switched on.

---

## 9. Repository settings checklist (admins)

Set once per repo (or once for the whole organization with an organization ruleset).

**Settings -> General -> Pull Requests**
- Allow squash merging: **on**. Merge commits and rebase merging: **off**
- Squash merge default message: **Pull request title**
- Automatically delete head branches: **on**

**Settings -> Rules -> Rulesets -> New branch ruleset** (target: default branch)
- Require a pull request before merging (approvals: 1, or 0 while working alone)
- Require conversation resolution
- Require status checks to pass: **PR title**, **Branch name** (add build/test later)
- Block force pushes
- Restrict deletions

> Rulesets and branch protection on **private** repositories depend on your GitHub plan. If a rule shows a plan warning, or an attempt to push to `main` succeeds, it isn't enforced.
> GitHub only lists a check in the ruleset picker after it has run recently (roughly the last 7 days), so open a test PR first, then add **PR title** and **Branch name** as required checks.
