# Git / Version Control Cheat Sheet

## 1. Setup
```bash
git config --global user.name "Monkey"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

Check config:
```bash
git config --list
git config --edit
```

---

## 2. Creating & Cloning Repos
```bash
git init
```
```bash
git clone <url>
```

---

## 3. Basic Workflow
### Add → Commit → Push
```bash
git status
```
```bash
git add file.txt
```
```bash
git commit -m "feat: add user login"
```
```bash
git push origin main
```

Add all changes:
```bash
git add -A
```

---

## 4. Branching
```bash
git branch
```
```bash
git branch feature-x
```
```bash
git checkout feature-x
```
Or:
```bash
git switch feature-x
```
Create + switch:
```bash
git switch -c feature-x
```

---

## 5. Merging
```bash
git checkout main
```
```bash
git merge feature-x
```

Abort merge:
```bash
git merge --abort
```

---

## 6. Rebasing
```bash
git checkout feature-x
```
```bash
git rebase main
```

Interactive rebase:
```bash
git rebase -i HEAD~5
```

---

## 7. Remote Management
```bash
git remote -v
```
```bash
git remote add origin <url>
```
```bash
git remote remove origin
```

---

## 8. Stash
```bash
git stash
```
```bash
git stash list
```
```bash
git stash pop
```
```bash
git stash drop
```

---

## 9. Inspecting History
```bash
git log
```
```bash
git log --oneline --graph --decorate
```
```bash
git show <commit>
```

Blame:
```bash
git blame file.txt
```

---

## 10. Undoing
Restore file from last commit:
```bash
git restore file.txt
```
Unstage a file:
```bash
git restore --staged file.txt
```
Go back one commit (hard):
```bash
git reset --hard HEAD~1
```
Soft reset (keep changes):
```bash
git reset --soft HEAD~1
```

---

## 11. Tags (Releases)
```bash
git tag v1.0.0
```
```
git push origin v1.0.0
```
List:
```bash
git tag
```

---

## 12. Submodules
```bash
git submodule add <url> path/
```
```bash
git submodule update --init --recursive
```

---

## 13. .gitignore
Example:
```
node_modules/
*.log
build/
.env
```

---

## 14. Cherry-picking
Bring single commit into current branch:
```bash
git cherry-pick <commit>
```

---

## 15. Useful Tricks
- `git add -p` → stage changes interactively.
- `git diff` → see unstaged changes.
- `git diff --staged` → see staged changes.
- `git commit --amend` → change last commit.
- `git clean -fd` → remove untracked files/dirs.
- `git reflog` → recover from screwups.

---

## 16. Conventional Commits (Reminder)
Your style:
```
feat: add new API
fix: correct login regex
refactor: simplify parser
chore: update deps
docs: add usage guide
test: add unit tests
```

---

## 17. Best Practices
- Commit small, focused changes.
- Write meaningful commit messages.
- Never rebase shared branches.
- Keep main branch deployable.
- Use tags for releases.

