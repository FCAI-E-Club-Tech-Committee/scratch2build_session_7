# Scratch2Build – Git Assignment
 
## FCAI Activity Portal
 
A hands-on Git exercise built around the **FCAI Activity Portal**. Two features — **Event Search** and **Category Filtering** — are developed on separate branches, merged back into `main`, a merge conflict is resolved, and a bad commit is undone safely with `git revert`.
 
> **Individual assignment.** Separate branches are used to simulate parallel work on independent features.
 
---
 
## Table of Contents
 
- [Skills Practiced](#skills-practiced)
- [Repository Structure](#repository-structure)
- [Branching Strategy](#branching-strategy)
- [Workflow](#workflow)
  - [1. Initial Setup](#1-initial-setup)
  - [2. Event Search](#2-event-search)
  - [3. Category Filtering](#3-category-filtering)
  - [4. Merge and Resolve Conflicts](#4-merge-and-resolve-conflicts)
  - [5. Recover from a Mistake](#5-recover-from-a-mistake)
  - [6. Inspect the History](#6-inspect-the-history)
- [Final State](#final-state)
- [Questions & Answers](#questions--answers)
---
 
## Skills Practiced
 
- Forking and cloning a repository
- Creating and switching between feature branches
- Writing clear, atomic commits
- Merging branches and resolving conflicts
- Undoing pushed work with `git revert`
- Inspecting history with `git log --graph`
## Repository Structure
 
```text
.
├── README.md
└── portal/
    ├── index.html
    ├── style.css
    └── script.js
```
 
## Branching Strategy
 
| Branch | Created from | Purpose |
| --- | --- | --- |
| `main` | — | Stable branch; receives all merges |
| `feature/event-search` | `main` | Event Search feature |
| `feature/category-filter` | `main` | Category Filtering feature |
 
| Branch | Changes |
| --- | --- |
| `feature/event-search` | Search placeholder in `portal/index.html` → `"Search events by title..."`<br>`searchSettings.placeholder` in `portal/script.js` → `"Search events by title..."`<br>`portalSettings.statusMessage` → `"Event search is ready."` |
| `feature/category-filter` | `categorySettings.defaultCategory`: `"all"` → `"tech"`<br>`portalSettings.statusMessage` → `"Category filtering is ready."` |
 
---
 
## Workflow
 
> Run all Git commands from the **repository root** unless stated otherwise.
 
### 1. Initial Setup
 
Fork the starter repository on GitHub, then clone your fork and inspect it:
 
```bash
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>/portal
 
git branch -a
git log --oneline
 
cd ..
```
 
### 2. Event Search
 
```bash
git checkout -b feature/event-search
```
 
Edit the files:
 
- `portal/index.html` → set the search placeholder to `"Search events by title..."`
- `portal/script.js` → set `searchSettings.placeholder` to the same value
- `portal/script.js` → set `portalSettings.statusMessage` to `"Event search is ready."`
```bash
git add portal/index.html portal/script.js
git commit -m "feat(search): update search placeholder and status message"
```
 
### 3. Category Filtering
 
```bash
git checkout main
git checkout -b feature/category-filter
```
 
Edit `portal/script.js`:
 
- `categorySettings.defaultCategory` → `"tech"`
- `portalSettings.statusMessage` → `"Category filtering is ready."`
```bash
git add portal/script.js
git commit -m "feat(category): default to tech category and update status message"
```
 
### 4. Merge and Resolve Conflicts
 
```bash
git checkout main
 
# main has not moved since the branches were created, so this is a fast-forward
git merge feature/event-search
 
# both branches changed the same line, so this merge conflicts in portal/script.js
git merge feature/category-filter
```
 
Resolve the conflict in `portal/script.js`:
 
1. Open the file and find the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
2. Keep **both** features: the new search placeholder and `defaultCategory: "tech"`.
3. Set `portalSettings.statusMessage` to `"Event search and category filtering are ready."`.
4. Delete every conflict marker line.
Verify that no markers are left, then finish the merge:
 
```bash
grep -rn "<<<<<<<\|>>>>>>>" portal/
 
git add portal/script.js
git commit -m "merge: combine event search and category filtering"
```
 
### 5. Recover from a Mistake
 
Change `portalSettings.statusMessage` to `"The portal is currently unavailable."`, then commit and push:
 
```bash
git add portal/script.js
git commit -m "chore: set status message to portal unavailable"
git push origin main
```
 
Undo that commit with `git revert`, which keeps the shared history intact, and push the revert:
 
```bash
git revert HEAD
git push origin main
```
 
### 6. Inspect the History
 
```bash
git log --oneline --graph --all --decorate
```
 
The graph should show:
 
- the commit on `feature/event-search`
- the commit on `feature/category-filter`
- the merge commit on `main`
- the "portal unavailable" commit
- the revert commit
**Output:**
 
```text
# paste the output of the command above here
```
 
---
 
## Final State
 
After all merges (and after the revert), `main` should contain:
 
| Setting | Value |
| --- | --- |
| Search input placeholder (`portal/index.html`) | `"Search events by title..."` |
| `searchSettings.placeholder` (`portal/script.js`) | `"Search events by title..."` |
| `categorySettings.defaultCategory` | `"tech"` |
| `portalSettings.statusMessage` | `"Event search and category filtering are ready."` |
 
---
 
## Questions & Answers
 
### 1. Why did the merge conflict occur?
 
Both feature branches were created from the same commit on `main`, and each one changed the **same line** — `portalSettings.statusMessage` in `portal/script.js` — to a different value (`"Event search is ready."` vs. `"Category filtering is ready."`). Git can merge changes to different lines automatically, but when two branches edit the same line differently it cannot tell which version is correct. It stops the merge, marks the conflict in the file, and leaves it to the developer to decide what the final line should be.
 
### 2. Why is `git revert` appropriate for undoing an already-pushed commit?
 
`git revert` does not delete the bad commit; it adds a **new commit** that applies the opposite changes. Once a commit has been pushed, other people may already have pulled it, so rewriting history with `git reset` or `git rebase` followed by a force-push would diverge their copies from the remote and cause conflicts. Reverting keeps the shared history intact and append-only, and leaves a clear audit trail of what went wrong and how it was undone.
 
