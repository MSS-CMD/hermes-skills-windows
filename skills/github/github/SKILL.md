---
name: github
description: "Complete GitHub operations guide: authentication, repo management, issues, PR workflow, code review, and codebase inspection. Covers gh CLI and git+curl fallback for every operation."
version: 1.0.0
author: Hermes Agent (consolidated)
license: MIT
platforms: [linux, macos, windows, android]
metadata:
  hermes:
    tags: [github, git, authentication, pull-requests, issues, code-review, ci-cd, releases, actions, code-inspection]
    related_skills: [claude-code, hermes-agent]
---

# GitHub Operations Guide

Everything you need to work with GitHub: authentication, repository management, issues, pull requests, code review, and codebase inspection. Every operation shows `gh` CLI first, then `git + curl` fallback for machines without `gh`.

## Quick Auth Detection

Run this at the start of any GitHub workflow:

```bash
if command -v gh &>/dev/null && gh auth status &>/dev/null; then
  AUTH="gh"
  GH_USER=$(gh api user --jq '.login')
else
  AUTH="curl"
  if [ -z "$GITHUB_TOKEN" ]; then
    if [ -f ~/.hermes/.env ] && grep -q "^GITHUB_TOKEN=" ~/.hermes/.env; then
      GITHUB_TOKEN=$(grep "^GITHUB_TOKEN=" ~/.hermes/.env | head -1 | cut -d= -f2 | tr -d '\n\r')
    elif grep -q "github.com" ~/.git-credentials 2>/dev/null; then
      GITHUB_TOKEN=$(grep "github.com" ~/.git-credentials 2>/dev/null | head -1 | sed 's|https://[^:]*:\([^@]*\)@.*|\1|')
    fi
  fi
  GH_USER=$(curl -s -H "Authorization: token $GITHUB_TOKEN" https://api.github.com/user | python3 -c "import sys,json; print(json.load(sys.stdin)['login'])")
fi

REMOTE_URL=$(git remote get-url origin 2>/dev/null || echo "")
OWNER_REPO=$(echo "$REMOTE_URL" | sed -E 's|.*github\.com[:/]||; s|\.git$||')
OWNER=$(echo "$OWNER_REPO" | cut -d/ -f1)
REPO=$(echo "$OWNER_REPO" | cut -d/ -f2)
```

---

## Section 1: Authentication Setup

### HTTPS with Personal Access Token (Recommended)

Create token at **https://github.com/settings/tokens** with `repo` + `workflow` scopes.

```bash
# Store credentials
git config --global credential.helper store
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

# Test
git ls-remote https://github.com/username/repo.git
```

### SSH Key Authentication

```bash
ssh-keygen -t ed25519 -C "email@example.com" -f ~/.ssh/id_ed25519 -N ""
cat ~/.ssh/id_ed25519.pub  # Add at https://github.com/settings/keys
ssh -T git@github.com
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

### gh CLI Authentication

```bash
# Interactive
gh auth login

# Headless (token)
echo "TOKEN" | gh auth login --with-token
gh auth setup-git
```

### Helper: Detect Auth Method

See `scripts/gh-env.sh` for the canonical auth detection script.

---

## Section 2: Repository Management

### Cloning

```bash
# Pure git (works everywhere)
git clone https://github.com/owner/repo-name.git
git clone --depth 1 https://github.com/owner/repo-name.git  # shallow

# With gh
gh repo clone owner/repo-name
```

### Creating Repositories

```bash
# With gh
gh repo create my-project --public --clone
gh repo create my-org/my-project --private --description "..." --clone

# With curl
curl -s -X POST -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/user/repos \
  -d '{"name": "my-project", "private": false, "auto_init": true}'

# From template
gh repo create my-app --template owner/template-repo --public --clone
```

### Forking

```bash
# With gh
gh repo fork owner/repo --clone

# With curl + git
curl -s -X POST -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/owner/repo/forks
sleep 3
git clone https://github.com/$GH_USER/repo.git
git remote add upstream https://github.com/owner/repo.git

# Keep fork in sync
git fetch upstream && git checkout main && git merge upstream/main && git push origin main
```

### Repository Settings

```bash
gh repo edit --description "..." --visibility public --enable-wiki=false
gh repo edit --add-topic "machine-learning,python"
```

### Branch Protection

```bash
curl -s -X PUT -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/$OWNER/$REPO/branches/main/protection \
  -d '{"required_status_checks": {"strict": true, "contexts": ["ci/test"]}, "required_pull_request_reviews": {"required_approving_review_count": 1}}'
```

### Secrets Management

```bash
gh secret set API_KEY --body "your-secret-value"
gh secret list
gh secret delete API_KEY
```

### Releases

```bash
# With gh
gh release create v1.0.0 --title "v1.0.0" --generate-notes
gh release create v2.0.0-rc1 --draft --prerelease --generate-notes
gh release list

# With curl
curl -s -X POST -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/$OWNER/$REPO/releases \
  -d '{"tag_name": "v1.0.0", "name": "v1.0.0", "generate_release_notes": true}'
```

### GitHub Actions Workflows

```bash
gh workflow list
gh run list --limit 10
gh run view <RUN_ID> --log-failed
gh run rerun <RUN_ID> --failed
gh workflow run ci.yml --ref main
```

### Gists

```bash
gh gist create script.py --public --desc "Useful script"
gh gist list
```

---

## Section 3: Issues Management

### Viewing Issues

```bash
# With gh
gh issue list
gh issue list --state open --label "bug"
gh issue list --assignee @me
gh issue view 42

# With curl
curl -s -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/repos/$OWNER/$REPO/issues?state=open&per_page=20"
```

### Creating Issues

```bash
gh issue create --title "Login redirect bug" --body "## Description\n..." --label "bug,backend" --assignee "username"
```

See `templates/bug-report.md` and `templates/feature-request.md` for issue body templates.

### Managing Issues

```bash
# Labels
gh issue edit 42 --add-label "priority:high,bug" --remove-label "needs-triage"

# Assignment
gh issue edit 42 --add-assignee username

# Comments
gh issue comment 42 --body "Investigating..."

# Close/Reopen
gh issue close 42 --reason "completed"
gh issue reopen 42
```

### Issue Triage Workflow

1. List untriaged: `gh issue list --label "needs-triage" --state open`
2. Read and categorize
3. Apply labels and priority
4. Assign if owner is clear
5. Comment with triage notes

---

## Section 4: Pull Request Workflow

### Branch Creation

```bash
git fetch origin
git checkout main && git pull origin main
git checkout -b feat/description
```

### Committing

```bash
git add src/auth.py
git commit -m "feat: add JWT-based user authentication

- Add login/register endpoints
- Add unit tests"
```

### Creating a PR

```bash
git push -u origin HEAD
gh pr create --title "feat: add authentication" --body "## Summary\n..." --label "enhancement"
gh pr create --draft --reviewer user1,user2
```

### Monitoring CI

```bash
gh pr checks --watch
```

### Merging

```bash
gh pr merge --squash --delete-branch
gh pr merge --auto --squash --delete-branch
```

For curl fallback details (checking CI, auto-fix loops, enabling auto-merge via GraphQL), see `references/ci-troubleshooting.md`.

### Complete Workflow

```bash
git checkout main && git pull origin main
git checkout -b fix/login-redirect-bug
# (make code changes)
git add src/auth/login.py tests/test_login.py
git commit -m "fix: correct redirect URL after login"
git push -u origin HEAD
gh pr create --title "fix: login redirect" --body "Closes #42"
gh pr checks --watch
gh pr merge --squash --delete-branch
```

---

## Section 5: Code Review

### Pre-Push Review (Local Changes)

```bash
git diff main...HEAD --stat
git diff main...HEAD
git log main..HEAD --oneline
```

Check for common issues:
```bash
git diff main...HEAD | grep -n "print(\|console\.log\|TODO\|FIXME\|debugger"
git diff main...HEAD | grep -in "password\|secret\|api_key\|token.*=\|private_key"
```

### PR Review Workflow

**With gh:**
```bash
gh pr view 123
gh pr diff 123
gh pr checkout 123
gh pr checks 123
```

**With curl:**
```bash
curl -s -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/$OWNER/$REPO/pulls/$PR_NUMBER/files
```

### Leaving Comments

**General:**
```bash
gh pr comment 123 --body "Overall looks good."
```

**Inline:**
```bash
HEAD_SHA=$(gh pr view 123 --json headRefOid --jq '.headRefOid')
gh api repos/$OWNER/$REPO/pulls/123/comments --method POST \
  -f body="Use parameterized queries." -f path="src/auth.py" -f commit_id="$HEAD_SHA" -f line=45 -f side="RIGHT"
```

**Formal review:**
```bash
gh pr review 123 --approve --body "LGTM!"
gh pr review 123 --request-changes --body "See inline comments."
```

### Review Checklist

- **Correctness:** Edge cases, error paths, data flow
- **Security:** No hardcoded secrets, SQL injection, XSS, path traversal
- **Code Quality:** Clear naming, DRY, single responsibility
- **Testing:** New code paths tested, happy + error paths
- **Performance:** No N+1 queries, unnecessary loops
- **Documentation:** Public APIs documented, non-obvious logic explained

See `references/review-output-template.md` for the structured review output format.

---

## Section 6: Codebase Inspection (pygount)

### Prerequisites

```bash
pip install pygount
```

### Basic Summary

```bash
cd /path/to/repo
pygount --format=summary \
  --folders-to-skip=".git,node_modules,venv,.venv,__pycache__,.cache,dist,build,.next,.tox,.eggs" \
  .
```

### Language Filter

```bash
pygount --suffix=py --format=summary .
pygount --suffix=py,yaml,yml --format=summary .
```

### Output Formats

```bash
pygount --format=summary .     # Table (recommended)
pygount --format=json .        # JSON for programmatic use
```

### Interpreting Results

Columns: Language, Files, Code lines, Comment lines, % of total.

Pseudo-languages: `__empty__`, `__binary__`, `__generated__`, `__duplicate__`, `__unknown__`.

### Pitfalls

- **Always** use `--folders-to-skip` to exclude .git, node_modules, venv — pygount will crawl everything otherwise.
- **Markdown** shows 0 code lines (classified as comments).
- **Large monorepos:** use `--suffix` to target specific languages.
- **JSON files** may show low counts — use `wc -l` for accuracy.

---

## Quick Reference Table

| Action | gh | curl endpoint |
|--------|-----|-------------|
| List issues | `gh issue list` | `GET /repos/{o}/{r}/issues` |
| Create issue | `gh issue create` | `POST /repos/{o}/{r}/issues` |
| View PR | `gh pr view N` | `GET /repos/{o}/{r}/pulls/N` |
| Merge PR | `gh pr merge` | `PUT /repos/{o}/{r}/pulls/N/merge` |
| Clone repo | `gh repo clone o/r` | `git clone https://github.com/o/r.git` |
| Create repo | `gh repo create` | `POST /user/repos` |
| Fork | `gh repo fork o/r` | `POST /repos/o/r/forks` |
| Create release | `gh release create` | `POST /repos/o/r/releases` |
| List workflows | `gh workflow list` | `GET /repos/o/r/actions/workflows` |
| Rerun CI | `gh run rerun ID` | `POST /repos/o/r/actions/runs/ID/rerun` |
| Set secret | `gh secret set KEY` | `PUT /repos/o/r/actions/secrets/KEY` |
| Search repos | `gh search repos "query"` | `GET /search/repositories?q=...` |
| LOC analysis | n/a | Use `pygount` (pip package) |
