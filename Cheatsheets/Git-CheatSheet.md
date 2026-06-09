# 🌿 Git Cheatsheet — Quick Reference

---

## ⚙️ Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nano
git config --global init.defaultBranch main
git config --list                              # All config
```

---

## 🚀 Basic Workflow

```bash
git init                                # New repo
git clone URL                           # Clone existing
git clone git@github.com:user/repo.git  # SSH clone

git status                              # Working state
git status -s                           # Short format
git diff                                # Unstaged changes
git diff --staged                       # Staged changes

git add file.py                         # Stage file
git add .                               # Stage all
git add -p file.py                      # Selective staging
git reset file.py                       # Unstage file

git commit -m "message"                 # Commit
git commit -am "message"                # Add + commit
git commit --amend                      # Edit last commit

git push origin main                    # Push
git push -u origin main                 # First push
git push origin --delete feature        # Delete remote branch

git pull origin main                    # Pull + merge
git fetch origin                        # Download (no merge)
git pull --rebase origin main           # Rebase instead
```

---

## 🌿 Branches

```bash
git branch                              # List local
git branch -a                           # All (incl. remote)
git branch feature/auth                 # Create branch
git checkout feature/auth               # Switch branch
git checkout -b feature/auth            # Create + switch
git switch feature/auth                 # Modern switch
git switch -c feature/auth              # Create + switch

git merge feature/auth                  # Merge branch
git merge --no-ff feature/auth          # Merge with commit
git rebase main                         # Rebase onto main

git branch -d feature/auth              # Delete (merged)
git branch -D feature/auth              # Force delete
```

---

## 📜 History

```bash
git log                                 # Full log
git log --oneline                       # Compact
git log --oneline --graph --all         # Visual tree
git log -10                             # Last 10
git log --author="Name"                 # By author
git log --since="1 week ago"            # By time
git log -- filename.py                  # File history
git show commit_hash                    # Commit details
git blame file.py                       # Line-by-line blame
```

---

## 🔄 Undo & Fix

```bash
git restore file.py                     # Discard unstaged changes
git restore --staged file.py            # Unstage
git checkout -- file.py                 # Old syntax restore

git revert commit_hash                  # New undo commit
git reset --soft HEAD~1                 # Undo commit (keep changes staged)
git reset --mixed HEAD~1                # Undo commit (keep changes unstaged)
git reset --hard HEAD~1                 # Undo commit (DISCARD changes!)

git stash                               # Save dirty state
git stash push -m "work in progress"    # Named stash
git stash list                          # List stashes
git stash pop                           # Apply + delete
git stash apply stash@{0}              # Apply (keep stash)
git stash drop stash@{0}               # Delete stash
git stash clear                         # Delete all stashes
```

---

## 🔍 Search

```bash
git grep "function_name"                # Search in code
git log -S "function_name"             # Search in history
git log --all --grep="bug fix"         # Search commit messages
```

---

## 🏷️ Tags

```bash
git tag                                 # List tags
git tag v1.0.0                         # Lightweight tag
git tag -a v1.0.0 -m "Release 1.0"    # Annotated tag
git push origin v1.0.0                 # Push tag
git push origin --tags                 # Push all tags
git tag -d v1.0.0                      # Delete local tag
git push origin --delete v1.0.0        # Delete remote tag
```

---

## 🔗 Remotes

```bash
git remote -v                           # List remotes
git remote add origin URL              # Add remote
git remote set-url origin URL          # Change URL
git remote remove origin               # Remove remote
git remote rename origin upstream      # Rename

# Fork workflow:
git remote add upstream https://github.com/original/repo
git fetch upstream
git merge upstream/main
```

---

## 🐍 .gitignore for Python

```
# Virtual environments
venv/
.venv/
env/

# Python
__pycache__/
*.pyc
*.pyo
*.egg-info/
dist/
build/

# Secrets
.env
.env.*
!.env.example
*.key
*.pem

# Database
*.db
*.sqlite3

# IDE
.vscode/
.idea/
*.swp

# Logs
*.log
logs/

# OS
.DS_Store
Thumbs.db
```
