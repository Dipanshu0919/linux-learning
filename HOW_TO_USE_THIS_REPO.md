# 🖥️ GitHub Codespaces Mein Kaise Use Karein

---

## 🚀 Setup in 5 Minutes

### Step 1: GitHub pe Repository Upload Karein

```bash
# Apne computer pe terminal open karo
git clone https://github.com/YOUR_USERNAME/linux-learning.git
cd linux-learning

# Ya agar naya repo banana hai:
git init
git add .
git commit -m "Initial Linux learning repo"
git remote add origin https://github.com/YOUR_USERNAME/linux-learning.git
git push -u origin main
```

### Step 2: Codespaces Start Karein

```
1. GitHub.com pe apna repository open karo
2. Green "<> Code" button pe click karo
3. "Codespaces" tab pe click karo
4. "Create codespace on main" button pe click karo
5. 1-2 minute wait karo (first time thoda time lagta hai)
```

### Step 3: Layout Configure Karein

```
┌─────────────────────────────────────────────────┐
│           VS Code (Codespaces)                  │
├──────────────┬──────────────────────────────────┤
│ FILE EXPLORER│        EDITOR                    │
│              │  (Markdown Preview yahan)         │
│ 📄 README   │                                   │
│ 📄 START    │                                   │
│ 📁 01-Basics│                                   │
│   📄 01-Intro│                                   │
│   📄 02-Term │                                   │
│ 📁 02-Files  │                                   │
├──────────────┴──────────────────────────────────┤
│                  TERMINAL                        │
│  $ ls -la                                        │
│  $ cd 01-Basics                                  │
│  $ pwd                                           │
└─────────────────────────────────────────────────┘
```

### Step 4: Markdown Preview Enable Karein

```
Option 1: .md file pe right-click karo → "Open Preview"
Option 2: File open karo → top-right mein preview icon click karo
Option 3: Shortcut: Ctrl+Shift+V
```

---

## ⌨️ Important Keyboard Shortcuts

| Action | Shortcut |
|--------|----------|
| Terminal open karo | Ctrl + ` |
| New terminal | Ctrl + Shift + ` |
| File explorer toggle | Ctrl + B |
| Markdown preview | Ctrl + Shift + V |
| Command palette | Ctrl + Shift + P |
| File search | Ctrl + P |
| Text search in files | Ctrl + Shift + F |
| Split editor | Ctrl + \ |

---

## 📖 Padhne Ka Best Tarika

### Split View Setup (Recommended):

```
1. .md file open karo (left side)
2. Ctrl+\ dabo (editor split hoga)
3. Right side pe Terminal open karo
4. Left mein padho, right mein practice karo
```

**Visual:**
```
┌─────────────────┬─────────────────┐
│  Markdown File  │    Terminal     │
│                 │                 │
│  ## Commands    │  $ ls          │
│                 │  file1.txt     │
│  `ls` lists     │  folder/       │
│  files          │                │
│                 │  $ pwd         │
│  `pwd` shows    │  /home/user    │
│  current dir    │                │
└─────────────────┴─────────────────┘
```

---

## 🔧 Codespaces Configuration

### devcontainer.json (already included):
```json
{
  "name": "Linux Learning Environment",
  "image": "ubuntu:22.04",
  "features": {
    "ghcr.io/devcontainers/features/python:1": {},
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/docker-in-docker:2": {}
  },
  "postCreateCommand": "pip install flask fastapi uvicorn gunicorn",
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "yzhang.markdown-all-in-one",
        "esbenp.prettier-vscode"
      ]
    }
  }
}
```

---

## 📝 Daily Learning Routine

```
Har roz ka routine (1-2 hours):

1. ☕ Pehle lesson ka markdown file padho (20 min)
2. 💻 Terminal mein commands try karo (30 min)
3. 🧩 Exercises complete karo (20 min)
4. 📝 Notes lo kisi notepad mein (10 min)
5. 🔁 Kal ke commands review karo (10 min)
```

---

## 🆘 Common Codespaces Issues

### Issue 1: Terminal band ho gaya
```
Solution: Ctrl+` se naya terminal open karo
```

### Issue 2: Codespace timeout ho gaya
```
Solution: GitHub pe codespace restart karo
Files safe rehti hain
```

### Issue 3: Commands kaam nahi kar rahe
```
Solution: Check karo ki correct folder mein ho
pwd command se current location check karo
```

### Issue 4: Markdown preview nahi dikh raha
```
Solution: 
1. .md file pe right-click karo
2. "Open Preview" select karo
Ya Ctrl+Shift+V shortcut use karo
```

---

## 💾 Progress Save Karna

```bash
# Apni progress GitHub pe save karo
git add .
git commit -m "Week 1 completed - basic commands learned"
git push
```

---

## 📱 Mobile Se Bhi Use Kar Sakte Ho!

GitHub Codespaces mobile browser mein bhi kaam karta hai:
- github.dev (press . on any repo)
- Mobile browser mein codespace open karo

---

## 🎯 Effective Learning Tips for Codespaces

1. **Side-by-side view**: Hamesha markdown aur terminal dono open rakho
2. **Type, don't copy**: Commands type karo, copy-paste mat karo
3. **Experiment freely**: Codespace sandbox hai — kuch bhi try karo
4. **Use search**: `Ctrl+Shift+F` se koi bhi topic search kar sako
5. **Commit daily**: Har din progress commit karo motivation ke liye

---

*"Codespaces aapka personal Linux server hai — use isko bharpur!" 🚀*
