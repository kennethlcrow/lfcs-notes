
## 📌 Overview

These labs are designed to build practical understanding of:

- Users and groups
    
- File ownership
    
- Permissions (rwx / octal)
    
- Real-world troubleshooting
    

---

# 🧪 Scenario 1 — Read but Don’t Touch

## 🎯 Goal

User `alice` should:

- ✅ Read a file
    
- ❌ NOT modify it
    

## 🧱 Setup

```bash
useradd -m alice
echo "Top Secret Data" > /tmp/secret.txt
```

## ❓ Task

Configure permissions so:

- `alice` can read `/tmp/secret.txt`
    
- `alice` cannot modify or delete it
    

## 🧠 Hints

- Consider owner vs group vs others
    
- Avoid over-permissioning (no `777`)
    

## 🧪 Test

```bash
su - alice
cat /tmp/secret.txt
echo "hack" >> /tmp/secret.txt
```

---

# 🧪 Scenario 2 — Group Collaboration

## 🎯 Goal

Users `alice` and `bob` should:

- ✅ Both read/write a shared file
    
- ❌ Others have no access
    

## 🧱 Setup

```bash
useradd -m bob
touch /shared.txt
```

## ❓ Task

Configure access so:

- `alice` and `bob` can edit `/shared.txt`
    
- No other users can access it
    

## 🧠 Hints

- You will need:
    
    - A group
        
    - Group ownership
        
    - Proper chmod
        

## 🧪 Test

```bash
su - alice
echo "alice edit" >> /shared.txt

su - bob
echo "bob edit" >> /shared.txt
```

---

# 🧪 Scenario 3 — Why Can’t I Access This?!

## 🎯 Goal

Fix a broken permission setup

## 🧱 Setup

```bash
mkdir /project
touch /project/data.txt
chmod 700 /project
chmod 644 /project/data.txt
useradd -m charlie
```

## ❓ Problem

User `charlie` cannot read `/project/data.txt`

Even though:

```
-rw-r--r--
```

## ❓ Task

Fix access so:

- `charlie` can read the file
    
- WITHOUT making everything world-accessible
    

## 🧠 Hint

Directory permissions matter more than you think

---

# 🧪 Scenario 4 — The Classic Mistake

## 🎯 Goal

Fix broken group membership

## 🧱 Setup

```bash
groupadd devs
usermod -G devs alice
```

## ❓ Problem

`alice` lost access to something she previously had

## ❓ Task

Fix it so:

- `alice` keeps existing groups
    
- AND is part of `devs`
    

## 🧠 Hint

Think about what `-G` does vs `-aG`

---

# 🧪 Scenario 5 — Directory Permissions Trap

## 🎯 Goal

User `dave` should:

- ✅ List files in `/reports`
    
- ❌ NOT read file contents
    

## 🧱 Setup

```bash
useradd -m dave
mkdir /reports
echo "secret report" > /reports/report1.txt
```

## ❓ Task

Configure permissions so:

- `dave` can run:
    
    ```bash
    ls /reports
    ```
    
- but cannot run:
    
    ```bash
    cat /reports/report1.txt
    ```
    

## 🧠 Key Concept

Directory permissions ≠ File permissions

---

# 🧠 General Approach Checklist

When solving any scenario:

## 1. Inspect

```bash
ls -l
ls -ld <directory>
```

## 2. Identify

- Who owns the file?
    
- What group is assigned?
    
- Where does the user fall?
    
    - owner?
        
    - group?
        
    - other?
        

## 3. Adjust

- `chmod` → permissions
    
- `chown` → ownership
    
- `usermod` → group membership
    

## 4. Verify

```bash
su - <user>
```

---

# 🔥 Core Principles

- Least privilege > convenience
    
- Groups > “others” for controlled sharing
    
- Directory permissions control access path
    
- Ownership determines control
    

---

# 🚀 Goal

Move from:

> “I know commands”

To:

> “I can solve access problems under pressure”