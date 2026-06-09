# ✏️ Lesson 4: Text Editors — Nano aur Vim

---

## 📚 Learning Objectives

- Nano editor use karna (Beginner-friendly)
- Vim basics seekhna (Power user tool)
- Files create, edit, save karna terminal mein
- Vim modal editing samjhna

---

## 🟢 NANO — Beginner ka Best Friend

Nano ek simple text editor hai. Bahut easy!

### Basic Usage:
```bash
nano filename.txt      # File open karo (create if not exists)
nano app.py            # Python file edit
nano /etc/hosts        # System file (sudo needed for system files)
sudo nano /etc/hosts   # Sudo ke saath
```

### Nano Interface:
```
+--[ GNU nano 6.2 ]----[ app.py ]-----+
|                                      |
|  from flask import Flask             |
|                                      |
|  app = Flask(__name__)               |
|                                      |
|  @app.route('/')                     |
|  def home():                         |
|      return "Hello!"                 |
|                                      |
+--[ Line 1/8, Col 1/1 ]------+
|^G Help  ^O Write  ^X Exit   |
|^K Cut   ^U Paste  ^W Search |
+------------------------------+

^ = Ctrl key
```

### Most Important Shortcuts:
```
Ctrl + O      = Save file (Write Out)
Ctrl + X      = Exit (bahar aao)
Ctrl + G      = Help
Ctrl + W      = Search
Ctrl + K      = Line cut (cut the line)
Ctrl + U      = Paste
Ctrl + Z      = Suspend (terminal mein wapas)
Alt  + U      = Undo
Ctrl + A      = Line ka start
Ctrl + E      = Line ka end
Ctrl + Y      = Previous page
Ctrl + V      = Next page
Ctrl + C      = Current line/col number dikhaao
```

### Nano Practice:

```bash
# 1. Ek Python file banao:
nano hello.py
```

*Nano mein type karo:*
```python
def greet(name):
    return f"Hello, {name}! Welcome to Linux!"

message = greet("Python Student")
print(message)
```
- `Ctrl + O` dabao (save)
- Enter dabao (filename confirm)
- `Ctrl + X` dabao (exit)

```bash
# 2. File run karo:
python3 hello.py
```

---

## 🔴 VIM — Power User ka Tool

Vim thoda mushkil lagta hai pehle, par ek baar seekh lo toh bahut fast ho jaoge!

### Vim kyu seekhna?

```
- Server pe sirf vim hoti hai aksar (nano nahi)
- SSH se connect ho toh vim zaroori hai
- Bahut fast editing (hands keyboard pe rehti hain)
- DevOps interviews mein vim aata hai
- Plugins se IDE ban jaata hai
```

### Vim ka BIGGEST concept: MODES

Vim mein teen modes hain:

```
NORMAL MODE   = Navigation aur commands (default jab vim khulta hai)
INSERT MODE   = Text type karna (i dabao enter karne ke liye)
VISUAL MODE   = Text select karna (v dabao)
COMMAND MODE  = Save, quit, search (:)
```

```
Vim open karo        → NORMAL MODE (orange)
'i' press karo       → INSERT MODE (green) — ab type kar sakte ho
'Esc' press karo     → NORMAL MODE wapas
':' press karo       → COMMAND MODE
```

### Vim Survival Kit (Minimum Zaruri Commands):

```bash
# File open karo:
vim app.py

# NORMAL MODE mein:
i          = INSERT MODE shuru karo (cursor ke aage)
I          = INSERT MODE line ke start pe
a          = INSERT MODE cursor ke baad
A          = INSERT MODE line ke end pe
o          = Neeche naya line kholo + INSERT MODE
O          = Upar naya line kholo + INSERT MODE

# Text type karo

Esc        = NORMAL MODE wapas

# Save aur quit (COMMAND MODE):
:w         = Save (Write)
:q         = Quit
:wq        = Save + Quit (MOST USED!)
:q!        = Save kiye bina quit (FORCE quit)
:wq!       = Force save + quit
ZZ         = :wq shortcut (no colon needed)
```

### Navigation (NORMAL MODE):

```
h = Left
l = Right
j = Down
k = Up
(Arrow keys bhi work karte hain)

w = Next word ka start
b = Previous word ka start
e = Current word ka end

0 = Line ka bilkul start
^ = Line ka first character
$ = Line ka end

gg = File ka start
G  = File ka end
:N = Line number N pe jao (e.g., :50)

Ctrl + f = Next page
Ctrl + b = Previous page
Ctrl + d = Half page down
Ctrl + u = Half page up
```

### Editing (NORMAL MODE):

```
x    = Ek character delete (Delete key jaisa)
dd   = Puri line delete karo
dw   = Ek word delete
d$   = Cursor se line end tak delete
yy   = Puri line copy (yank)
yw   = Ek word copy
p    = Paste (cursor ke baad)
P    = Paste (cursor ke pehle)
u    = Undo
Ctrl+r = Redo
.    = Last command repeat karo
r    = Ek character replace
R    = Replace mode (overwrite karo)
```

### Search aur Replace (COMMAND MODE):

```
/pattern    = Aage search karo
?pattern    = Peeche search karo
n           = Next match
N           = Previous match

:%s/old/new/g       = Puri file mein replace
:%s/old/new/gc      = Replace but confirm karo
:5,10s/old/new/g    = Lines 5-10 mein replace
```

### Visual Mode:

```
v         = Character selection
V         = Line selection
Ctrl+v    = Block/column selection

# Select karo, phir:
d  = Delete
y  = Copy (yank)
>  = Indent right
<  = Indent left
```

### Vim Practice Sequence:

```bash
# Step 1: Vim kholo
vim practice.py

# Step 2: Ye file like karo (i dabao pehle):
# i -> type -> Esc -> :wq

# File content:
# names = ["Alice", "Bob", "Charlie"]
# for name in names:
#     print(f"Hello, {name}!")

# Step 3: "Bob" ko "Ravi" mein change karo:
# /Bob -> Enter -> r (replace mode) -> type "Ravi" -> Esc

# Step 4: Save karo:
# :wq

# Step 5: Run karo:
python3 practice.py
```

---

## 🆚 Nano vs Vim — Kab Kya Use Karein?

| Situation | Use |
|-----------|-----|
| Quick config file edit | Nano |
| Server pe kaam karna | Vim |
| Large code edit karna | Vim |
| Pehli baar file edit | Nano |
| SSH remote server | Vim |
| System administration | Vim |

---

## 🔧 Troubleshooting

### "I'm stuck in Vim!"

```
CALM DOWN! Hamesha kaam karta hai:
1. Esc dabao (kai baar)
2. :q! type karo
3. Enter dabao
Aap bahar aa jaoge! Kuch save nahi hoga.
```

### Vim ne accidentally khula?

```bash
# Agar vim band karna hai bina save kiye:
Esc
:q!
Enter
```

### Nano mein change undo karna?

```
Alt + U = Undo
Alt + E = Redo
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: Nano se Flask app banao
nano flask_nano_app.py
# Likhho:
# from flask import Flask
# app = Flask(__name__)
# @app.route('/')
# def home():
#     return "Created with Nano!"
# if __name__ == '__main__':
#     app.run()
# Ctrl+O, Enter, Ctrl+X

# Task 2: Vim survival practice
vim vim_practice.py
# i dabao
# print("I survived Vim!")
# Esc dabao
# :wq
# python3 vim_practice.py

# Task 3: Vim search practice
vim flask_nano_app.py
# /Flask dabo (search karo)
# n (next match)
# Esc
# :q!
```

---

## 📖 Summary

```
NANO:
Ctrl+O   = Save
Ctrl+X   = Exit
Ctrl+W   = Search
Ctrl+K   = Cut line
Very beginner friendly!

VIM:
i        = Insert mode (type karo)
Esc      = Normal mode
:wq      = Save + quit
:q!      = Quit without save
/text    = Search
dd       = Line delete
yy + p   = Copy paste line
Powerful but learning curve hai!
```

---

**Next:** `02-Files/Exercises.md` → Sab practice karo! 💪
