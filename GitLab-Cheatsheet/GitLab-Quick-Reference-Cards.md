# 🎨 GitLab Quick Reference Cards - Easy Visual Guide

## Color-Coded Command Reference with Icons

---

## 📱 COMMAND QUICK CARDS

### 🟦 BLUE CARD: GIT BASICS

```
╔═══════════════════════════════════════════╗
║        🔷 GIT BASIC COMMANDS 🔷           ║
╠═══════════════════════════════════════════╣
║                                           ║
║  📊 Check Status                          ║
║     git status                            ║
║     Shows: What changed? Stage status?    ║
║                                           ║
║  📝 View Changes                          ║
║     git diff                              ║
║     Shows: Exactly what changed           ║
║                                           ║
║  🔨 Stage Changes                         ║
║     git add .              (all files)    ║
║     git add app.js         (one file)     ║
║                                           ║
║  📦 Create Commit                         ║
║     git commit -m "🔷 Your message"       ║
║                                           ║
║  📤 Push to GitLab                        ║
║     git push origin branch-name           ║
║                                           ║
║  📥 Get Updates                           ║
║     git pull origin main                  ║
║                                           ║
║  🔄 See Commit History                    ║
║     git log                               ║
║                                           ║
╚═══════════════════════════════════════════╝
```

### 🟩 GREEN CARD: BRANCH COMMANDS

```
╔═══════════════════════════════════════════╗
║      🌿 BRANCH MANAGEMENT 🌿              ║
╠═══════════════════════════════════════════╣
║                                           ║
║  🌿 List All Branches                     ║
║     git branch              (local)       ║
║     git branch -a           (all)         ║
║                                           ║
║  🆕 Create New Branch                     ║
║     git checkout -b feature/name          ║
║     git switch -c feature/name   (newer)  ║
║                                           ║
║  🔄 Switch Branch                         ║
║     git checkout main                     ║
║     git switch main         (newer)       ║
║                                           ║
║  🗑️ Delete Branch (Local)                 ║
║     git branch -d feature/name            ║
║     git branch -D feature/name (force)    ║
║                                           ║
║  🗑️ Delete Branch (Remote)                ║
║     git push origin -d feature/name       ║
║                                           ║
║  📍 Push New Branch                       ║
║     git push -u origin new-branch         ║
║     (-u = set upstream)                   ║
║                                           ║
║  🔄 Rename Branch                         ║
║     git branch -m old-name new-name       ║
║                                           ║
╚═══════════════════════════════════════════╝
```

### 🟧 ORANGE CARD: MERGE COMMANDS

```
╔═══════════════════════════════════════════╗
║        🔀 MERGE & SYNC 🔀                 ║
╠═══════════════════════════════════════════╣
║                                           ║
║  🔀 Merge Another Branch                  ║
║     git merge feature/name                ║
║     (Combines branches)                   ║
║                                           ║
║  ⚠️  Conflict? (Choose which code)        ║
║     1. Edit file                          ║
║     2. Remove conflict markers            ║
║     3. git add file                       ║
║     4. git commit                         ║
║                                           ║
║  📥 Pull Latest Main                      ║
║     git pull origin main                  ║
║     (Updates your branch)                 ║
║                                           ║
║  🔄 Rebase on Main (Advanced)             ║
║     git rebase main                       ║
║     (Cleaner history)                     ║
║     (Be careful with shared branches!)    ║
║                                           ║
║  ↩️ Undo Last Commit                      ║
║     git reset --soft HEAD~1               ║
║     (Keep changes, redo commit)           ║
║                                           ║
║  🧹 Clean Uncommitted Changes             ║
║     git restore .                         ║
║     ⚠️ WARNING: Can't undo!                ║
║                                           ║
╚═══════════════════════════════════════════╝
```

### 🔴 RED CARD: DANGER ZONE

```
╔═══════════════════════════════════════════╗
║    🚨 DANGEROUS COMMANDS 🚨               ║
║     (Use only if you know what!          ║
║      you're doing!)                       ║
╠═══════════════════════════════════════════╣
║                                           ║
║  💣 HARD RESET (Undo everything)          ║
║     git reset --hard HEAD~1               ║
║     ⚠️ Deletes all changes!               ║
║     ✅ Use: Accidental commit             ║
║                                           ║
║  💣 FORCE PUSH                            ║
║     git push --force origin branch        ║
║     ⚠️ Overwrites remote!                 ║
║     ✅ Use: Fixing your own branch        ║
║     ❌ Never: On shared branch!           ║
║                                           ║
║  💣 REVERT COMMIT                         ║
║     git revert COMMIT_SHA                 ║
║     Creates new commit undoing old one    ║
║     ✅ Safe: Doesn't change history       ║
║                                           ║
║  💣 DELETE BRANCH                         ║
║     git branch -D feature/name            ║
║     ⚠️ Permanently deletes!               ║
║     ✅ Make sure merged first!            ║
║                                           ║
║  💣 STASH CHANGES                         ║
║     git stash                             ║
║     Temporarily hides changes             ║
║     git stash pop                         ║
║     Brings them back                      ║
║                                           ║
╚═══════════════════════════════════════════╝
```

### 💜 PURPLE CARD: GITLAB CLI (glab)

```
╔═══════════════════════════════════════════╗
║      💻 GITLAB CLI (glab) 💻              ║
╠═══════════════════════════════════════════╣
║                                           ║
║  🔐 LOGIN FIRST                           ║
║     glab auth login                       ║
║     glab auth status                      ║
║                                           ║
║  📦 PROJECT COMMANDS                      ║
║     glab repo list                        ║
║     glab repo view                        ║
║     glab repo clone project               ║
║     glab repo create my-project           ║
║                                           ║
║  📋 ISSUE COMMANDS                        ║
║     glab issue create                     ║
║     glab issue list                       ║
║     glab issue view 42                    ║
║     glab issue update 42                  ║
║     glab issue close 42                   ║
║                                           ║
║  🔀 MERGE REQUEST COMMANDS                ║
║     glab mr create                        ║
║     glab mr list                          ║
║     glab mr view 10                       ║
║     glab mr approve 10                    ║
║     glab mr merge 10                      ║
║     glab mr merge 10 --squash             ║
║                                           ║
║  ⚙️ PIPELINE COMMANDS                     ║
║     glab ci run                           ║
║     glab ci list                          ║
║     glab ci status                        ║
║     glab ci trace JOB_ID                  ║
║     glab ci retry JOB_ID                  ║
║                                           ║
║  📊 API COMMANDS                          ║
║     glab api projects/1/issues            ║
║     glab api project/1/merge_requests     ║
║                                           ║
╚═══════════════════════════════════════════╝
```

---

## 📊 STATUS QUICK REFERENCE

### Pipeline Status

```
✅ GREEN (Success)
┌─────────────────────┐
│ ✅ All passed       │
│ 🚀 Ready to merge   │
│ 🟢 All systems go   │
└─────────────────────┘

🟡 YELLOW (In Progress)
┌─────────────────────┐
│ ⏳ Still running    │
│ 🔄 Wait please     │
│ 📊 Check status    │
└─────────────────────┘

🔴 RED (Failed)
┌─────────────────────┐
│ ❌ Something broke  │
│ 🔧 Fix needed      │
│ 📜 Check logs      │
└─────────────────────┘
```

### Approval Status

```
📊 Approval Progress

⭕⭕⭕ = No approvals
   "Not ready yet, need reviews!"

⭕✅⭕ = 1 approval
   "One more review needed!"

✅✅⭕ = 2 approvals
   "Ready to merge!"

✅✅✅ = 3+ approvals
   "Everyone loves it! Merge!"
```

---

## 🎯 DECISION FLOWCHART CARDS

### "What Should I Do?" Quick Cards

#### CARD 1: "I made a mistake!"

```
┌────────────────────────────────┐
│    OH NO! I MADE A MISTAKE!    │
└────────────────────────────────┘
         │
    What happened?
         │
    ┌────┴────┬─────────────┐
    │          │             │
Not pushed yet  Already pushed   In production!
    │            │             │
    │            │      😱 CRITICAL!
    │            │      Call team lead
    │            │      Plan rollback
    │            │
Committed wrong? Pushed wrong branch?
    │            │
Fix code         Force revert:
git reset        git revert COMMIT
git commit       git push
    │
 No force push!
 (If shared branch)
```

#### CARD 2: "Should I merge?"

```
╔═══════════════════════════════╗
║    MERGE CHECKLIST            ║
╠═══════════════════════════════╣
║ ☐ Tested locally?             ║
║ ☐ Pipeline passed? (✅)        ║
║ ☐ 2+ approvals?                ║
║ ☐ No conflicts?                ║
║ ☐ Updated docs?                ║
║ ☐ Clear commit message?        ║
║ ☐ Code reviewed by expert?     ║
║ ☐ All comments resolved?       ║
║                                ║
║ If ALL checked: MERGE! ✅      ║
║ If any NO: Fix it first!       ║
╚═══════════════════════════════╝
```

---

## 📌 COMMIT MESSAGE TEMPLATES

### Template 1: Standard

```
📝 Template Format:

[emoji] [type]: [description]

[body - optional details]

[footer - optional issue number]

Example:

✨ feature: add dark mode toggle

- Added theme switcher to header
- Updated color variables
- Tested on all browsers

Closes #1234
```

### Template 2: Conventional

```
[type]([scope]): [subject]

[body]

[footer]

Types:
  feat     ✨ New feature
  fix      🐛 Bug fix
  docs     📝 Documentation
  style    🎨 Formatting
  refactor ♻️  Code reorganization
  perf     ⚡ Performance
  test     🧪 Tests
  chore    🔧 Maintenance

Example:

feat(auth): add password validation

- Check minimum length
- Check for special chars
- Show error message

Closes #456
```

---

## 🎓 LEARNING TRACK CARDS

### BEGINNER TRACK (Weeks 1-2)

```
┌──────────────────────────────┐
│   WEEK 1: THE BASICS         │
├──────────────────────────────┤
│ ✅ Create account             │
│ ✅ Generate SSH key           │
│ ✅ Clone repository           │
│ ✅ Create first branch        │
│ ✅ Make commits               │
│ ✅ Push to GitLab             │
│                              │
│ Time: 5-7 hours              │
│ Difficulty: ⭐ Easy          │
└──────────────────────────────┘

┌──────────────────────────────┐
│  WEEK 2: COLLABORATION       │
├──────────────────────────────┤
│ ✅ Create MR                  │
│ ✅ Request code review        │
│ ✅ Make requested changes     │
│ ✅ Merge your code            │
│ ✅ Celebrate! 🎉              │
│                              │
│ Time: 5-7 hours              │
│ Difficulty: ⭐⭐ Easy         │
└──────────────────────────────┘
```

### INTERMEDIATE TRACK (Weeks 3-4)

```
┌──────────────────────────────┐
│ WEEK 3: AUTOMATION           │
├──────────────────────────────┤
│ ✅ Set up CI/CD pipeline      │
│ ✅ Create .gitlab-ci.yml      │
│ ✅ Run tests automatically    │
│ ✅ Deploy on merge            │
│                              │
│ Time: 6-8 hours              │
│ Difficulty: ⭐⭐ Medium       │
└──────────────────────────────┘

┌──────────────────────────────┐
│ WEEK 4: PRODUCTION           │
├──────────────────────────────┤
│ ✅ Deploy to production       │
│ ✅ Monitor applications       │
│ ✅ Manage environments        │
│ ✅ Handle issues              │
│                              │
│ Time: 6-8 hours              │
│ Difficulty: ⭐⭐⭐ Hard       │
└──────────────────────────────┘
```

---

## 🎯 COMMON WORKFLOWS

### Workflow 1: "Fix a Bug"

```
Step 1: Create Issue
  └─ Go to Issues → New issue
     Title: "Login button not working on mobile"
     Label: "bug", "urgent"

Step 2: Create Branch
  └─ git checkout -b fix/mobile-login-button

Step 3: Make Changes
  └─ Edit files
     Test locally: npm test
     
Step 4: Commit
  └─ git commit -m "🐛 Fix mobile login button"

Step 5: Push
  └─ git push origin fix/mobile-login-button

Step 6: Create MR
  └─ Go to Merge Requests → New MR
     Closes #1234

Step 7: Get Reviewed
  └─ Wait for team to review
     Address feedback
     
Step 8: Merge
  └─ Click [Merge]

Step 9: Done ✅
  └─ Bug is fixed!
     Issue auto-closes
```

### Workflow 2: "Add a Feature"

```
Step 1: Plan
  └─ Create issue with requirements
     Get feedback from team

Step 2: Create Branch
  └─ git checkout -b feature/dark-mode

Step 3: Develop
  └─ Break into small commits:
     ✨ Add dark theme colors
     ✨ Add toggle button
     ✨ Add save preference
     
Step 4: Test
  └─ npm test
     npm start (manual testing)
     Test on mobile

Step 5: Push
  └─ git push origin feature/dark-mode

Step 6: Create MR
  └─ Write detailed description
     Link related issues

Step 7: Code Review Cycle
  └─ Review by 2+ people
     Address comments
     Push fixes
     
Step 8: Merge
  └─ Pipeline must pass ✅
     All reviews done ✅
     Click [Merge]

Step 9: Celebrate 🎉
  └─ Your feature is live!
```

---

## 🆘 EMERGENCY RESPONSE CARDS

### Emergency 1: "I pushed to main!"

```
⚠️ PANIC LEVEL: 🔴 HIGH

DO THIS NOW:
1. Tell your team immediately
2. Don't push more changes
3. Take a deep breath

ASSESS:
- Is code breaking? (Critical)
- Is it just a typo? (Minor)
- Did tests fail? (Check)

FIX OPTIONS:

Option A: Revert (Safe)
  ├─ Find the commit SHA
  ├─ git revert COMMIT_SHA
  ├─ git push origin main
  └─ Old code is restored

Option B: Force Fix (Risky)
  ├─ Fix code locally
  ├─ Commit fix
  ├─ git push origin main
  └─ (Only do after discussing!)

NEXT TIME:
✅ Branch protection rules
✅ Require MR approvals
✅ Prevent direct push to main
```

### Emergency 2: "Merge conflict!"

```
⚠️ PANIC LEVEL: 🟡 MEDIUM

You see: "Merge conflict in app.js"

THIS IS NORMAL! Steps:

1. Pull latest code
   git pull origin main

2. See conflict markers
   <<<<<<<< HEAD
   Your code
   ========
   Their code
   >>>>>>>>

3. Choose which code to keep
   Delete markers
   Keep best code

4. Stage changes
   git add app.js

5. Commit
   git commit -m "Resolve conflict"

6. Push
   git push origin your-branch

7. Try merge again ✅

TIPS:
- Read both versions
- Talk to teammate if unsure
- Test after resolving
- It gets easier with practice
```

---

## 🎓 FACT CARDS

### Git vs GitHub vs GitLab

```
┌──────────┬──────────┬──────────┬──────────┐
│   GIT    │ GITHUB   │ GITLAB   │ BITBUCKET│
├──────────┼──────────┼──────────┼──────────┤
│ Tool for │ Cloud    │ Cloud    │ Cloud    │
│ version  │ hosting  │ hosting  │ hosting  │
│ control  │ + tools  │ + DevOps │ + tools  │
│          │          │          │          │
│ Local    │ GitHub   │ gitlab.  │ Bit      │
│ command  │ .com     │ com      │ bucket.  │
│ line     │          │ (Free)   │ org      │
│          │          │          │          │
│ Free     │ Free for │ Free     │ Free for │
│ always   │ public   │ tier     │ public   │
│          │          │ available│          │
├──────────┼──────────┼──────────┼──────────┤
│ Best for:│ Open     │ DevOps   │ Teams    │
│          │ source   │ teams    │ (Jira)   │
│          │ projects │ CI/CD    │          │
└──────────┴──────────┴──────────┴──────────┘
```

### Branch Naming Conventions

```
✅ GOOD NAMES:
feature/dark-mode
fix/login-button
docs/api-update
refactor/database
test/payment-flow

❌ BAD NAMES:
my-changes
test
fix
new-thing
asdf123
the-big-update
```

### Common Emoji Meanings

```
✨ New feature
🐛 Bug fix
📝 Documentation
🎨 UI/style changes
⚡ Performance
🔒 Security
🧪 Tests
♻️  Refactor
🗑️ Remove
🚀 Deploy
⚙️ Config
🔧 Tools
```

---

## 📖 MINI GLOSSARY CARDS

```
┌──────────────────────────────┐
│ 📚 GIT GLOSSARY              │
├──────────────────────────────┤
│                              │
│ COMMIT                       │
│ = Snapshot of changes        │
│ = Can be reverted            │
│                              │
│ BRANCH                       │
│ = Parallel development line  │
│ = Isolated changes           │
│                              │
│ MERGE                        │
│ = Combine two branches       │
│ = Integrate changes          │
│                              │
│ FETCH                        │
│ = Download from GitLab       │
│ = Don't update your files    │
│                              │
│ PULL                         │
│ = Fetch + Merge              │
│ = Update your files          │
│                              │
│ PUSH                         │
│ = Upload to GitLab           │
│ = Share your changes         │
│                              │
│ CLONE                        │
│ = Download entire repo       │
│ = First time setup           │
│                              │
│ STASH                        │
│ = Temporarily hide changes   │
│ = Get them back later        │
│                              │
│ REVERT                       │
│ = Undo a commit              │
│ = Creates new commit         │
│                              │
│ RESET                        │
│ = Undo commits               │
│ = Dangerous!                 │
│                              │
└──────────────────────────────┘
```

---

## ✅ CHECKLIST CARDS (Print & Use!)

### Daily Standup Card

```
📋 DAILY STANDUP CHECKLIST

Name: ________________  Date: ___/___/___

WHAT DID YOU DO YESTERDAY?
☐ Completed: _____________________
☐ Pushed to: ____________________
☐ Reviewed: _____________________

WHAT ARE YOU DOING TODAY?
☐ Task 1: _____________________
☐ Task 2: _____________________
☐ Task 3: _____________________

ANY BLOCKERS?
☐ Yes: _____________________
☐ No: All clear!

NOTES:
_________________________________
_________________________________
```

### Code Review Checklist

```
👀 CODE REVIEW CHECKLIST

MR #: _____  Author: ___________

FUNCTIONALITY
☐ Does it work as described?
☐ Does it solve the problem?
☐ Any edge cases missed?

CODE QUALITY
☐ Code is readable
☐ No obvious bugs
☐ Follows conventions
☐ DRY (no duplication)
☐ SOLID principles

TESTS
☐ Tests present
☐ Tests pass
☐ Coverage adequate

DOCUMENTATION
☐ Code commented
☐ README updated
☐ API docs updated

SECURITY
☐ No hardcoded secrets
☐ No SQL injection risk
☐ Input validated
☐ Authorization checked

DECISION:
☐ APPROVE
☐ REQUEST CHANGES
☐ COMMENT (Just info)

Comments:
_________________________________
_________________________________
```

---

## 🎯 SUCCESS TRACKER

### Monthly Progress Card

```
MONTH: _________________

WEEK 1:
┌─────────────────────────┐
│ PRs Created: ____       │
│ Issues Closed: ____     │
│ Code Reviews: ____      │
│ Learning: ________      │
│ Confidence: 1-10: ___   │
└─────────────────────────┘

WEEK 2:
┌─────────────────────────┐
│ PRs Created: ____       │
│ Issues Closed: ____     │
│ Code Reviews: ____      │
│ Learning: ________      │
│ Confidence: 1-10: ___   │
└─────────────────────────┘

WEEK 3:
┌─────────────────────────┐
│ PRs Created: ____       │
│ Issues Closed: ____     │
│ Code Reviews: ____      │
│ Learning: ________      │
│ Confidence: 1-10: ___   │
└─────────────────────────┘

WEEK 4:
┌─────────────────────────┐
│ PRs Created: ____       │
│ Issues Closed: ____     │
│ Code Reviews: ____      │
│ Learning: ________      │
│ Confidence: 1-10: ___   │
└─────────────────────────┘

MONTHLY GOALS ACHIEVED: ___/5

NEXT MONTH GOALS:
1. _______________________
2. _______________________
3. _______________________
```

---

**Print these cards, laminate them, keep them at your desk!**

🦊 Made with ❤️ for GitLab learners
