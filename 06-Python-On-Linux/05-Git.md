# 🌿 Lesson 5: Git on Linux — Version Control

---

## 📚 Learning Objectives

- Git basics Linux terminal mein
- Common workflows
- GitHub ke saath SSH
- Git best practices for deployment

---

## ⚙️ Git Setup

```bash
# Install:
sudo apt-get install git

# Global config (pehli baar):
git config --global user.name "Tumhara Naam"
git config --global user.email "tumhara@email.com"
git config --global core.editor nano      # Default editor
git config --global init.defaultBranch main

# Config dekho:
git config --list
cat ~/.gitconfig
```

---

## 🚀 Daily Git Commands

```bash
# Repository banao:
git init
git clone https://github.com/user/repo.git

# Status:
git status
git status -s   # Short format

# Staging:
git add file.py
git add .                # Sab files
git add *.py             # Pattern
git add -p file.py       # Selective staging

# Commit:
git commit -m "feat: add user authentication"
git commit -am "fix: correct typo"   # add + commit ek saath

# Push/Pull:
git push origin main
git push -u origin main  # First time
git pull origin main
git fetch origin         # Pull without merge

# Branches:
git branch               # List
git branch feature/auth  # Create
git checkout feature/auth # Switch
git checkout -b hotfix    # Create + switch
git merge feature/auth   # Merge
git branch -d feature    # Delete local
git push origin --delete feature  # Delete remote

# Logs:
git log
git log --oneline
git log --oneline --graph --all

# Diff:
git diff
git diff --staged
git diff HEAD~1

# Stash:
git stash           # Changes temporarily save
git stash pop       # Wapas laao
git stash list      # Stashes dekho

# Undo:
git restore file.py      # Unstaged changes undo
git reset HEAD file.py   # Unstage
git reset --soft HEAD~1  # Last commit undo (changes keep)
git reset --hard HEAD~1  # Last commit undo (changes delete!)
```

---

## 🌿 .gitignore for Python Projects

```bash
cat > .gitignore << 'EOF'
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
.eggs/

# Secrets
.env
.env.local
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
EOF
```

---

## 🚀 Git Workflow for Deployment

```bash
# Feature branch workflow:
git checkout main
git pull origin main
git checkout -b feature/new-endpoint

# Code likhna...
git add .
git commit -m "feat: add /api/users endpoint"

# Main se latest laao:
git fetch origin main
git rebase origin/main

# Push karo:
git push origin feature/new-endpoint

# Production deployment:
ssh deploy@server.com
cd /var/www/myapp
git pull origin main
source venv/bin/activate
pip install -r requirements.txt
sudo systemctl restart myapp
```

---

## 🏋️ Practice

```bash
# Complete workflow:
mkdir ~/git_practice && cd ~/git_practice
git init
echo "# My Project" > README.md
echo "flask" > requirements.txt
git add .
git commit -m "Initial commit"

# Branch banao:
git checkout -b feature/hello
echo "Hello from feature!" > hello.py
git add .
git commit -m "feat: add hello.py"

# Main pe wapas:
git checkout main
git merge feature/hello
git log --oneline

cd ~
```

---

## 📖 Summary

```
git init             = Repo banao
git clone URL        = Clone karo
git add .            = All stage
git commit -m "msg"  = Commit
git push             = Push to remote
git pull             = Pull from remote
git branch name      = Branch create
git checkout branch  = Branch switch
git merge branch     = Merge
git log --oneline    = History
git stash            = Changes temporarily save
```

**Next:** `06-Python-On-Linux/06-Running-Python-Apps.md` → Apps chalao! 🚀
