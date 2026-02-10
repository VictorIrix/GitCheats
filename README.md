# GitChetos 🧠
A practical Git cheat sheet for day-to-day work, including **both Merge and Rebase workflows**.

> This is intentionally task-oriented: **command + when to use + example**.

---

## Table of contents
- [Top 20 commands (80/20)](#top-20-commands-8020)
- [Setup](#setup)
- [Daily loop](#daily-loop)
- [Stage changes](#stage-changes)
- [Commit](#commit)
- [Undo & fix mistakes](#undo--fix-mistakes)
- [Branches](#branches)
- [Merge workflow](#merge-workflow)
- [Rebase workflow](#rebase-workflow)
- [Remotes](#remotes)
- [Stash](#stash)
- [Logs & inspect](#logs--inspect)
- [Tags & releases](#tags--releases)
- [Cleaning & maintenance](#cleaning--maintenance)
- [Emergencies (lifesavers)](#emergencies-lifesavers)
- [Contributing flow (fork + PR)](#contributing-flow-fork--pr)

---

## Top 20 commands (80/20)
```bash
git status -sb
git clone <url> [dir]

git add -A
git add -p
git commit -m "message"
git commit --amend

git pull --rebase
git fetch --all --prune
git push -u origin <branch>

git switch <branch>
git switch -c <new-branch>
git restore <file>
git restore --staged <file>

git log --oneline --decorate --graph --all
git diff
git diff --staged
git show <ref>

git stash push -m "wip" -u
git stash pop

git reflog
```

⸻

## Setup

Identity, editor, defaults
```bash
git config --global user.name  "Your Name"
git config --global user.email "you@email.com"
git config --global core.editor "code --wait"     # or vim/nano
git config --global init.defaultBranch main
```
Useful aliases
```bash
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --decorate --graph --all"
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 --stat"
```

⸻

## Daily loop
```bash
git status -sb
git diff
git add -p
git commit -m "feat: do thing"
git push
```

⸻

## Stage changes
```bash
git add <file>
git add <dir>
git add -A                 # stage all (including deletes)
git add -p                 # stage interactively (patch mode)
git add -i                 # interactive staging menu
```

⸻

## Commit
```bash
git commit -m "message"
git commit -v              # show diff in editor
git commit -a -m "message" # auto-stage tracked files only
git commit --amend         # edit last commit (message and/or contents)

Commit message convention
	•	Title: <= 50 chars, imperative mood (e.g., “Fix login redirect”)
	•	Blank line
	•	Body: explain what and why (not how)
```
⸻

## Undo & fix mistakes

Discard local changes (working tree)
```bash
git restore <file>
git restore .              # discard all unstaged changes
```
Unstage (keep changes in working tree)
```bash
git restore --staged <file>
git restore --staged .     # unstage everything
```
Restore a file from a commit/branch
```bash
git restore --source <ref> -- <path>
# example:
git restore --source origin/main -- src/app.js
```
Undo commits

Safe for shared branches: use revert
```bash 
git revert <commit>        # creates a new commit that undoes changes
```
Rewrite history (ONLY if not pushed/shared):
```bash
git reset --soft HEAD~1    # keep changes staged
git reset --mixed HEAD~1   # keep changes unstaged (default)
git reset --hard HEAD~1    # discard changes (danger)
```
“I messed up” recovery
```bash
git reflog                 # find lost commits/branches
git reset --hard <reflog-ref>
```

⸻

## Branches
```bash
git branch                 # list local branches
git branch -vv             # show upstream/tracking info

git switch <branch>
git switch -c <new-branch> # create + switch
git branch -d <branch>     # delete (safe)
git branch -D <branch>     # delete (force)
```

⸻

## Merge workflow

Best when you want explicit merge commits, or your team prefers “no history rewriting”.

Update your branch
```bash
git switch <branch>
git fetch origin
git merge origin/main      # or origin/master depending on repo
```
Merge a feature branch into main
```bash
git switch main
git fetch origin
git merge <feature-branch>
git push origin main
```
If push is rejected (non-fast-forward)
```bash
git fetch origin
git merge origin/<your-branch>
git push
```

⸻

## Rebase workflow

Best to keep a linear history. Do not rebase commits that others already pulled.

Update your branch with rebase
```bash
git switch <branch>
git fetch origin
git rebase origin/main
```
Preferred “pull” variant
```bash
git pull --rebase
```
Resolve conflicts during rebase

# fix files
```bash
git add <fixed-files>
git rebase --continue
```
# or to abort
```bash
git rebase --abort
```
Interactive rebase (clean up before PR)
```bash
git rebase -i origin/main
# use: pick / reword / squash / fixup / drop
```
If you already pushed and rebased anyway

You will likely need:
```bash
git push --force-with-lease
```
--force-with-lease is safer than --force.

⸻

## Remotes
```bash
git remote -v
git remote add <name> <url>
git remote rename <old> <new>
git remote remove <name>
git remote show origin
```
Fetch vs pull
```bash
git fetch origin            # download, no merge/rebase
git pull                    # fetch + merge (by default)
git pull --rebase           # fetch + rebase
```
Push basics
```bash
git push origin <branch>
git push -u origin <branch> # set upstream
```
Delete remote branch
```bash
git push origin --delete <branch>
# old syntax:
git push origin :<branch>
```
Multiple remotes (GitHub + Bitbucket, etc.)
```bash
git remote add bitbucket <url>
git push -u bitbucket <branch>
git push bitbucket --all
git push bitbucket --tags
```

⸻

## Stash
```bash
git stash                   # stash tracked changes
git stash push -m "wip"     # message
git stash push -u -m "wip"  # include untracked files
git stash list
git stash show -p stash@{0}

git stash apply stash@{0}   # apply without removing from stash
git stash pop               # apply + remove latest
git stash drop stash@{0}
git stash clear
```

⸻

## Logs & inspect
```bash
git log
git log -p -2
git log --stat
git log --pretty=short
git log --pretty=fuller
git log --since="2 weeks ago"

git log --oneline --decorate --graph --all
git show <ref>
git diff
git diff --staged
git diff <refA>..<refB>     # commits in refB not in refA
git diff <refA>...<refB>    # changes since common ancestor

git blame <file>
git blame -L 20,60 <file>
```
What will I push?
```bash
git log origin/main..HEAD --oneline
```

⸻

## Tags & releases
```bash
git tag
git tag -l "v1.4.2*"

git tag v1.0.0                     # lightweight
git tag -a v1.0.0 -m "release 1.0"  # annotated

git show v1.0.0
git tag -d v1.0.0
git push origin v1.0.0
git push origin --tags
git push origin :refs/tags/v1.0.0   # delete remote tag
```

⸻

## Cleaning & maintenance
```bash
git clean -nd              # preview delete of untracked files/dirs
git clean -fd              # delete untracked files/dirs (danger)

git fetch --prune
git remote prune origin
```

⸻

## Emergencies (lifesavers)

Cherry-pick (grab one commit)
```bash
git cherry-pick <commit>
```
Bisect (find the commit that introduced a bug)
```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
# test, then:
git bisect good
git bisect bad
git bisect reset
```
Conflicts checklist
```bash
	1.	git status to see conflicted files
	2.	Fix conflict markers <<<<<<< ======= >>>>>>>
	3.	git add <file>
	4.	Continue:
	  •	merge: git commit
	  •	rebase: git rebase --continue
```
⸻

## Contributing flow (fork + PR)
```bash
git clone <your-fork-url>
cd repo

git remote add upstream <original-repo-url>
git fetch upstream

git switch -c feature/my-change upstream/main
# work...
git add -p
git commit -m "feat: add X"
git push -u origin feature/my-change
```
Then open a Pull Request from origin/feature/my-change → upstream/main.

⸻
