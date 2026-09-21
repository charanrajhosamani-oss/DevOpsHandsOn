# Git and GitHub: A Practical Guide for Daily Development

This guide is written so a new teammate can go from “I have never used Git” to “I can ship work every day” without memorizing every command. Read the mental model once. Use the daily workflow as a checklist. Keep the cheat sheet nearby.

---

## Table of contents

1. [What Git and GitHub actually are](#1-what-git-and-github-actually-are)
2. [The mental model (this is the whole game)](#2-the-mental-model-this-is-the-whole-game)
3. [One-time setup](#3-one-time-setup)
4. [The daily workflow](#4-the-daily-workflow)
5. [Branches: why they exist and how to name them](#5-branches-why-they-exist-and-how-to-name-them)
6. [Commits that people can actually read](#6-commits-that-people-can-actually-read)
7. [Talking to GitHub: fetch, pull, push](#7-talking-to-github-fetch-pull-push)
8. [Pull requests: how teams ship code](#8-pull-requests-how-teams-ship-code)
9. [Merge vs rebase](#9-merge-vs-rebase)
10. [Conflicts (they are normal)](#10-conflicts-they-are-normal)
11. [Undo, safely](#11-undo-safely)
12. [Stash, ignore, and tags](#12-stash-ignore-and-tags)
13. [GitHub features you will use every week](#13-github-features-you-will-use-every-week)
14. [Team habits that prevent pain](#14-team-habits-that-prevent-pain)
15. [Common problems and exact fixes](#15-common-problems-and-exact-fixes)
16. [Daily cheat sheet](#16-daily-cheat-sheet)
17. [Glossary](#17-glossary)

---

## 1. What Git and GitHub actually are

**Git** is a version control tool that runs on your computer. It records snapshots of your files (commits), lets you work on parallel copies of the project (branches), and lets you combine work later.

**GitHub** is a website and service that hosts Git repositories. Teams use it to:

- store the shared copy of the code (`origin`)
- review work before it lands on the main branch (pull requests)
- track bugs and tasks (issues)
- run automated checks (GitHub Actions)
- release versions (tags and GitHub Releases)

A useful sentence:

> Git is the notebook. GitHub is the shared filing cabinet plus the review process.

You can use Git without GitHub. You cannot use GitHub as a team without Git.

| Thing | Where it lives | What it is |
| --- | --- | --- |
| Working copy | Your disk | Files you edit in VS / VS Code / Cursor |
| Local repository | `.git` folder | Git’s history on your machine |
| Remote repository | GitHub | The shared history the team trusts |
| Pull request | GitHub | A proposal: “please take my branch into `main`” |

---

## 2. The mental model (this is the whole game)

Almost every Git command moves files between four places:

```text
  Working tree          Staging area           Local repo            GitHub
  (files you edit)      (what the next         (commits on           (shared
                         commit will include)   your machine)         history)

       edit  ----->  git add  ----->  git commit  ----->  git push
       <----- git restore --staged <-- git reset  <-----  git fetch / pull
```

### The four areas, in plain language

1. **Working tree** — the files open in your editor. Dirty until you commit.
2. **Staging area (index)** — a shopping cart. You choose *which* changes go into the next snapshot. This is why you can commit one file and leave another unfinished.
3. **Local repository** — commits stored in `.git`. These exist even if GitHub is down.
4. **Remote (`origin`)** — GitHub. Other people only see your work after `git push`.

### A commit is a snapshot, not a diff

A commit is a full snapshot of the staged files, plus:

- a unique hash (`a1b2c3d...`)
- author, date, message
- a pointer to the parent commit(s)

History is a chain of snapshots. A branch is **not a copy of the whole project**. A branch is a sticky note that says “this commit is the tip of this line of work.”

```text
  main:     A --- B --- C
                         \
  feature:                D --- E     <- you are here
```

When you create a branch, Git just creates a new sticky note. Cheap. Fast. Use branches constantly.

### HEAD

`HEAD` means “where I am right now.” Usually it points at a branch name, and the branch points at a commit.

If you hear “detached HEAD,” it means you checked out a commit directly instead of a branch. You can look around, but new commits have no branch name protecting them. Check out a branch again to get back to normal.

---

## 3. One-time setup

### Install Git

- Windows: [https://git-scm.com/download/win](https://git-scm.com/download/win) (or Git for Windows that Visual Studio already installed)
- Confirm:

```bash
git --version
```

### Tell Git who you are

Use the same name and email you use on GitHub. Commits record this.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@company.com"
```

Useful extras:

```bash
git config --global init.defaultBranch main
git config --global core.autocrlf true          # Windows: convert line endings
git config --global pull.rebase false           # merge on pull (safer default for most teams)
git config --global fetch.prune true            # drop deleted remote branches locally
git config --global diff.colorMoved zebra
```

See what you set:

```bash
git config --global --list
```

### Authenticate with GitHub

Pick one. HTTPS with a password **does not work** anymore; GitHub needs a credential helper, a Personal Access Token, or SSH.

**SSH (recommended for daily work):**

```bash
ssh-keygen -t ed25519 -C "you@company.com"
```

Then add the public key (`~/.ssh/id_ed25519.pub`) in GitHub → Settings → SSH and GPG keys.

Test:

```bash
ssh -T git@github.com
```

**GitHub CLI (very useful):**

```bash
gh auth login
```

After that, `gh pr create`, `gh pr checkout`, and `gh pr view` become part of the daily loop.

### Clone the team repo

HTTPS:

```bash
git clone https://github.com/org/repo.git
cd repo
```

SSH:

```bash
git clone git@github.com:org/repo.git
cd repo
```

That creates:

- a local folder with the files
- a remote named `origin` pointing at GitHub
- a local `main` (or `master`) tracking `origin/main`

---

## 4. The daily workflow

This is the loop you will run hundreds of times. Learn this first. Everything else is a variation.

### Start of day: update `main`

```bash
git checkout main
git pull
```

`git pull` = `git fetch` (download new commits) + merge/rebase them into your current branch.

If your team uses `master` instead of `main`, substitute that name everywhere.

### Start a task: new branch from latest `main`

Never commit directly on `main` unless the team explicitly says to.

```bash
git checkout main
git pull
git checkout -b feature/short-ticket-description
```

Examples of good branch names:

- `feature/user-export-csv`
- `fix/login-timeout`
- `chore/upgrade-nuget-packages`

### Do the work, then check what changed

```bash
git status
git diff                 # unstaged changes
git diff --staged        # already staged changes
```

`git status` is the most important Git command. When you are lost, run it.

### Stage only what belongs in this commit

```bash
git add path/to/file.cs
git add src/             # a folder
git add -p               # stage hunks interactively (best habit)
```

Avoid `git add .` until you have looked at `git status`. It is easy to commit secrets, `bin/`, `obj/`, or a local config file.

### Commit

```bash
git commit -m "Add CSV export for user list"
```

Or open an editor for a proper message:

```bash
git commit
```

A commit is local until you push. You can still fix the last commit if you have not pushed (see [Undo](#11-undo-safely)).

### Push the branch to GitHub the first time

```bash
git push -u origin HEAD
```

`-u` (upstream) remembers that this local branch belongs to `origin/feature/...`. Later you can just run `git push` and `git pull`.

### Open a pull request

On GitHub: compare your branch to `main`, write why the change exists, request reviewers.

Or with GitHub CLI:

```bash
gh pr create --fill --base main
```

### After review: update the same branch

You do **not** open a new PR for review comments. You commit on the same branch and push again. The PR updates automatically.

```bash
git add ...
git commit -m "Address review: validate empty export"
git push
```

### After merge: clean up locally

```bash
git checkout main
git pull
git branch -d feature/short-ticket-description
```

If GitHub deleted the remote branch:

```bash
git fetch --prune
```

That is the whole job. The rest of this file is how to handle the messy days.

---

## 5. Branches: why they exist and how to name them

### Why

Two people can change the same project at the same time without overwriting each other. `main` stays releasable. Your experiment lives on a branch until a review says it is ready.

### Local vs remote branches

| Name | Meaning |
| --- | --- |
| `main` | Your local sticky note |
| `origin/main` | Last time you fetched, this is where GitHub’s `main` was |
| `feature/login` | Your local work |
| `origin/feature/login` | The copy on GitHub after you pushed |

`origin/main` does **not** update by magic. `git fetch` or `git pull` updates it.

### Commands you need

```bash
git branch                       # list local branches
git branch -a                    # local + remote-tracking
git checkout -b new-branch       # create and switch
git switch new-branch            # modern alternative to checkout
git switch -c new-branch         # create and switch (modern)
git branch -d old-branch         # delete if merged
git branch -D old-branch         # delete even if not merged (careful)
```

`git checkout` and `git switch` both change branches. `switch` is newer and harder to confuse with restoring files.

### A simple team model that works

Keep this unless the team already has a documented GitFlow.

- **`main`** — always deployable (or always the integration branch the team agreed on)
- **short-lived feature branches** — one ticket, one branch, one PR
- **merge to `main` through a pull request** — never push straight to `main` if the repo is protected

Long-lived branches (`develop`, `release/1.2`) only help if the team actually uses them. Extra permanent branches add merge cost.

### Do not

- put many unrelated tickets on one branch
- let a branch live for weeks without merging `main` into it
- reuse a branch name for a second, unrelated change after it was merged

---

## 6. Commits that people can actually read

A good commit answers: **why did the tree change?**

### Message style

Prefer present tense, imperative mood, like a command:

- Good: `Fix timeout when identity provider is slow`
- Bad: `fixed stuff` / `WIP` / `asdf` / `updates`

A practical template:

```text
Short summary in 72 characters or less

Optional body: what problem you saw, what you changed,
and anything a reviewer would miss (feature flags, migrations,
follow-up work).
```

### What belongs in one commit

One idea. Not “refactor + bugfix + formatting + new feature.”

If you mixed things, unstage and split:

```bash
git reset HEAD           # unstage everything, keep file changes
git add -p               # pick hunks for commit 1
git commit -m "..."
git add -p
git commit -m "..."
```

### What never belongs in a commit

- passwords, connection strings, API keys, `.pfx` files, `appsettings.Development.json` with secrets
- `bin/`, `obj/`, `node_modules/`, user-specific `.vs/` folders
- commented-out “just in case” code dumps
- huge generated files unless the team agreed to vendor them

If you committed a secret, **rotating the secret is required**. Deleting it in a later commit is not enough; it is still in history. Tell the team and rotate immediately.

---

## 7. Talking to GitHub: fetch, pull, push

### Remote

```bash
git remote -v
```

Usually you have one remote:

```text
origin  git@github.com:org/repo.git (fetch)
origin  git@github.com:org/repo.git (push)
```

### fetch vs pull vs push

| Command | What it does | Changes your files? |
| --- | --- | --- |
| `git fetch` | Downloads new commits and updates `origin/*` | No |
| `git pull` | Fetch, then merge (or rebase) into **current** branch | Yes, if your branch can move |
| `git push` | Uploads your commits to GitHub | No local file edits |

Safe habit when you want to look before integrating:

```bash
git fetch origin
git log --oneline HEAD..origin/main
```

That shows what `main` has that you do not.

### “Your branch is behind”

Someone merged to `main` while you were working. Update your feature branch:

```bash
git checkout feature/your-work
git fetch origin
git merge origin/main
```

Resolve conflicts if Git stops, then:

```bash
git push
```

### “Updates were rejected because the remote contains work you do not have”

Someone else pushed to **your** branch (or you pushed from another machine). Get their commits first:

```bash
git pull
git push
```

Do not `git push --force` unless you understand [force-push](#force-push-with-a-seatbelt).

### Tracking

See what your branch tracks:

```bash
git status
```

Set it if you forgot `-u` earlier:

```bash
git push -u origin HEAD
```

---

## 8. Pull requests: how teams ship code

A **pull request (PR)** is a GitHub conversation attached to a branch. It is not a Git object. Git only knows commits and branches. GitHub adds the review UI.

### What a good PR looks like

- **Small.** A reviewer can finish it in one sitting.
- **One purpose.** “Add CSV export” not “export + login rewrite + CSS cleanup.”
- **Description explains why.** Screenshots for UI. Notes for migrations or config.
- **Green checks.** If CI is required, wait for it.
- **Draft until it is ready.** `gh pr create --draft` or the Draft button.

### The GitHub flow

```text
1. Branch from main
2. Commit locally
3. Push branch
4. Open PR against main
5. Review + CI
6. Address comments with new commits
7. Merge
8. Delete branch
9. Pull main locally
```

### Merge buttons on GitHub

| Option | Result | When to use |
| --- | --- | --- |
| **Create a merge commit** | PR branch is merged; history shows the PR | Default on many teams; safest mentally |
| **Squash and merge** | All PR commits become one commit on `main` | Nice when the branch has messy “wip” commits |
| **Rebase and merge** | PR commits replay onto `main` with no merge commit | Clean linear history; more moving parts |

Use whatever the repository’s default is. Do not fight the team setting on every PR.

### Reviewing someone else’s PR locally

```bash
gh pr checkout 123
```

Or classic Git:

```bash
git fetch origin
git checkout feature/their-branch
```

Run the app. Leave comments on GitHub, not only in chat.

### Review comments that help

- Point at a line and say what is wrong and what “done” looks like.
- Separate **blocking** comments from **nit** comments.
- Approve when you would be willing to support this in production.

---

## 9. Merge vs rebase

Both mean “bring this line of work up to date with that other line.” They differ in history shape.

### Merge (default, easy to reason about)

```bash
git checkout feature/your-work
git merge origin/main
```

Git creates a merge commit if both sides moved. History keeps the true parallel work.

```text
main:     A --- B --- C
               \       \
feature:        D --- E --- M
```

### Rebase (linear history)

```bash
git checkout feature/your-work
git rebase origin/main
```

Git takes your commits and replays them on top of the latest `main`.

```text
main:     A --- B --- C
                       \
feature:                D' --- E'
```

The commits `D` and `E` are **rewritten** into new hashes `D'` and `E'`.

### Rule that prevents disasters

> Do not rebase (or otherwise rewrite) commits that other people already pulled.

If the branch is only yours and not on `main` yet, rebase is fine. If the branch is shared, merge is safer.

### After a rebase you already pushed

History changed, so a normal push is rejected. You must force-push **your feature branch only**:

```bash
git push --force-with-lease
```

Never force-push `main`.

---

## 10. Conflicts (they are normal)

A conflict means Git cannot guess how to combine two edits to the same place. It is not a failure. It is Git asking you to be the editor.

### When they happen

- you and a teammate changed the same lines
- you both added a file with the same name
- a rebase is replaying a commit onto different code

### How to resolve

Git stops and marks files. Open them. You will see:

```text
<<<<<<< HEAD
your current branch version
=======
incoming version
>>>>>>> origin/main
```

1. Edit the file into the correct final code. Delete the `<<<<<<<`, `=======`, `>>>>>>>` markers.
2. `git add` the resolved file.
3. Continue:

**If you were merging:**

```bash
git add path/to/file
git commit                 # Git often pre-fills a merge message
```

**If you were rebasing:**

```bash
git add path/to/file
git rebase --continue
```

Abort if it is a mess:

```bash
git merge --abort
git rebase --abort
```

Your branch returns to the state before the merge/rebase started.

### Tips

- Merge `main` into your feature branch **often** (every day on long work) so you get small conflicts instead of one giant conflict at PR time.
- If a generated file conflicts, regenerate it instead of hand-merging.
- Ask the other author if the conflict is business logic, not syntax.

---

## 11. Undo, safely

Git almost never deletes history immediately. “Undo” usually means “move a pointer” or “copy an old snapshot back.”

**Look before you undo:**

```bash
git status
git log --oneline -10
git reflog                 # your personal “where HEAD has been”
```

### “I changed a file and want the last committed version”

Unstaged file:

```bash
git restore path/to/file
```

Older Git:

```bash
git checkout -- path/to/file
```

This **throws away uncommitted edits** in that file. There is no recycle bin.

### “I staged a file by mistake”

```bash
git restore --staged path/to/file
```

The edits stay in the working tree. They just leave the shopping cart.

### “The last commit message is wrong” (not pushed)

```bash
git commit --amend -m "Better message"
```

### “I forgot a file in the last commit” (not pushed)

```bash
git add forgotten-file.cs
git commit --amend --no-edit
```

Do not amend if you already pushed, unless you will `--force-with-lease` a **personal** branch and nobody else is using it.

### “I committed on the wrong branch”

If the commit is still only local:

```bash
git branch rescue-branch      # save the commit under a name
git reset --hard HEAD~1       # only if you are sure this branch should drop it
git checkout correct-branch
git cherry-pick rescue-branch
```

If you are not sure, stop and use `git stash` or ask. `reset --hard` destroys uncommitted work.

### “I want to throw away local commits but keep the files”

```bash
git reset --soft HEAD~1      # uncommit, keep staging
git reset HEAD~1             # uncommit, keep files unstaged (mixed, default)
```

### “I want this file as it was in an old commit, but keep current history”

```bash
git checkout COMMIT_HASH -- path/to/file
git add path/to/file
git commit -m "Restore file from older behavior"
```

### “I deleted a branch by mistake”

```bash
git reflog
git checkout -b recovered HASH_FROM_REFLOG
```

### Force-push with a seatbelt

```bash
git push --force-with-lease
```

This overwrites the remote branch **only if** nobody else pushed in the meantime. Prefer it over `--force`.

Still never do this on `main`.

---

## 12. Stash, ignore, and tags

### Stash — put unfinished work in a drawer

You need to switch branches but the current edits are not ready to commit.

```bash
git stash push -m "half-done validation"
git checkout main
# ... later
git stash list
git stash pop
```

Useful variants:

```bash
git stash push -u -m "includes untracked files"
git stash show -p stash@{0}
git stash drop stash@{0}
```

Do not stash for days as a substitute for a branch. A branch is easier to share and harder to lose.

### .gitignore — files Git should never track

Create or edit `.gitignore` at the repo root. Typical .NET / Node entries:

```gitignore
bin/
obj/
node_modules/
.vs/
*.user
appsettings.*.local.json
```

If a file is **already tracked**, adding it to `.gitignore` is not enough:

```bash
git rm --cached path/to/file
git commit -m "Stop tracking local file"
```

The file stays on disk; Git just stops versioning it.

`git status` showing a file you do not want is your cue to ignore it **before** `git add`.

### Tags and releases

A tag names a commit, usually a version:

```bash
git tag -a v1.4.0 -m "Release 1.4.0"
git push origin v1.4.0
```

On GitHub, a Release is a tag plus notes and optional binaries.

---

## 13. GitHub features you will use every week

### Issues

An issue is a tracked discussion: bug, task, or proposal. Link it from the PR:

- `Fixes #42` in a commit or PR description closes the issue when the PR merges.
- `Refs #42` links without closing.

### CODEOWNERS and branch protection

Repos often require:

- a passing build
- at least one approval
- no direct pushes to `main`
- up-to-date with `main` before merge

If your push to `main` is rejected, that is the protection working. Use a PR.

### Forks (open source and some enterprise models)

A **fork** is your copy of someone else’s repo under your account.

```text
upstream  = original project
origin    = your fork
```

Typical flow:

```bash
git clone git@github.com:you/their-repo.git
cd their-repo
git remote add upstream git@github.com:them/their-repo.git
git fetch upstream
git checkout -b fix/typo
# work, commit
git push -u origin HEAD
# open a PR from your fork branch into them/main
```

Keep your fork’s `main` current:

```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

### GitHub Actions (CI)

On each push or PR, GitHub can run tests and builds defined in `.github/workflows/*.yml`. A red X on a PR means “do not merge yet.” Open the failed job log, fix the code or the test, push again.

You do not need to author workflows to use them. You do need to treat a failing check as part of your job.

### `gh` commands worth learning

```bash
gh pr status
gh pr view
gh pr checks
gh pr merge
gh issue list
gh browse
```

---

## 14. Team habits that prevent pain

1. **Pull `main` at the start of the day** and before you open a PR.
2. **One branch per ticket.** Small PRs merge faster and break less.
3. **Never commit on `main`.** Protect it.
4. **Run `git status` before every commit and push.**
5. **Do not commit secrets.** If you do, rotate them; do not only delete the file.
6. **Write commit messages for the next reader**, which is often you in three months.
7. **Review as carefully as you write.** A rubber-stamp review is how bugs reach production.
8. **Talk before rewriting shared history.** Rebase private branches; merge public ones.
9. **Delete merged branches.** Noise hides the branches that still matter.
10. **Keep `main` green.** If you break it, your next job is to fix it.

---

## 15. Common problems and exact fixes

### “I cloned the repo but I am not on the latest code”

```bash
git checkout main
git pull
```

### “I have local changes and Git will not switch branches”

Commit, stash, or discard. Git refuses to overwrite your edits.

```bash
git stash push -m "wip"
git switch other-branch
```

### “I accidentally committed to `main`”

If not pushed:

```bash
git branch feature/move-this
git reset --hard origin/main
git switch feature/move-this
git push -u origin HEAD
```

If already pushed to a shared `main`, **do not reset `main`**. Open a revert PR:

```bash
git revert COMMIT_HASH
git push
```

`revert` creates a new commit that undoes a previous one. That is the correct public undo.

### “Git says I have diverged”

Both you and the remote have commits the other does not.

```bash
git status
git pull                 # merge remote into yours, unless the team uses rebase-on-pull
git push
```

### “I need one commit from another branch, not the whole branch”

```bash
git cherry-pick COMMIT_HASH
```

Resolve conflicts if asked, then push.

### “The file is modified but I did not touch it” (line endings)

On Windows, line endings (`CRLF` vs `LF`) cause this. Prefer a repo `.gitattributes`:

```gitattributes
* text=auto
```

Do not mass-convert line endings in an unrelated PR.

### “Permission denied (publickey)”

SSH key is missing from the agent or from GitHub.

```bash
ssh -T git@github.com
```

Add the key in GitHub settings, or switch the remote to HTTPS if the team uses that.

### “I need to see who changed this line”

```bash
git blame path/to/file
git log -p -- path/to/file
```

### “I want a clean copy of a file from `main`”

```bash
git checkout main -- path/to/file
```

---

## 16. Daily cheat sheet

```bash
# orientation
git status
git log --oneline --graph --decorate -20
git diff
git diff --staged

# start work
git switch main
git pull
git switch -c feature/ticket-name

# save work
git add -p
git commit -m "Explain why"
git push -u origin HEAD

# update my branch with latest main
git fetch origin
git merge origin/main
git push

# end of work
# open PR on GitHub or: gh pr create
git switch main
git pull
git branch -d feature/ticket-name
git fetch --prune

# parking unfinished work
git stash push -m "wip"
git stash pop

# undo last local commit, keep files
git reset HEAD~1

# undo a commit that is already on main
git revert COMMIT_HASH
```

Copy this block into a note if you want. After a few weeks you will not need it.

---

## 17. Glossary

| Term | Meaning |
| --- | --- |
| **Repository (repo)** | A project’s Git database plus working files |
| **Commit** | A snapshot with a hash, message, and parent(s) |
| **Branch** | A movable pointer to a commit |
| **HEAD** | Your current position |
| **Staging area / index** | The next commit’s shopping cart |
| **Remote** | A named GitHub URL, usually `origin` |
| **Fetch** | Download commits without merging |
| **Pull** | Fetch + integrate into the current branch |
| **Push** | Upload commits to a remote |
| **Upstream** | The branch your local branch tracks, or the original repo when you forked |
| **Fast-forward** | Your branch can simply slide forward; no merge commit needed |
| **Merge commit** | A commit with two parents that joins histories |
| **Rebase** | Replay commits on a new base; rewrites hashes |
| **Conflict** | Overlapping edits Git will not auto-combine |
| **PR / merge request** | GitHub (or GitLab) review of a branch |
| **Clone** | Copy a remote repo, including history |
| **Fork** | A server-side copy of a repo under your account |
| **Tag** | A name permanently attached to a commit, often a version |
| **Reflog** | Local diary of where HEAD has been; life saver |
| **SHA / hash** | The commit id, e.g. `9f3a1c2` |
| **Detached HEAD** | You checked out a commit, not a branch |
| **CI** | Automated build/test, often GitHub Actions |

---

## A 30-minute practice path

If you want this in your hands, not only in your head:

1. Create a throwaway repo on GitHub.
2. Clone it.
3. Create a branch, add a file, commit, push, open a PR, merge it.
4. Make two branches that edit the same line, merge one, then merge the other, and resolve the conflict.
5. Amend a commit you have not pushed.
6. `git revert` a commit you have pushed.
7. Use `git stash`, switch branches, then `stash pop`.
8. Run `git reflog` and see your own trail.

When those eight steps feel boring, you know enough Git for professional daily work. Everything after that is lookup, not magic.

---

*Use this file as a shared team reference. If your repository has extra rules (branch prefixes, squash-only merges, required reviewers), add them at the top as a short “how we work here” section so nobody has to guess.*
