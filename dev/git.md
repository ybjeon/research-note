# Git

## Basic Commands

### Setup
```bash
git init                        # initialize a new repo
git clone <url>                 # clone a remote repo
git config --global user.name "Name"
git config --global user.email "email@example.com"
```

### Snapshot
```bash
git status                      # show working tree status
git add <file>                  # stage a file
git add .                       # stage all changes
git commit -m "message"         # commit staged changes
git commit --amend              # amend the last commit
```

### Branch
```bash
git branch                      # list local branches
git branch <name>               # create a branch
git branch -d <name>            # delete a branch
git branch -m <old> <new>       # rename a branch
git switch <name>               # switch to a branch
git switch -c <name>            # create and switch
git merge <name>                # merge branch into current
```

### Remote
```bash
git remote -v                   # list remotes
git fetch                       # fetch from remote (no merge)
git pull                        # fetch + merge
git push origin <branch>        # push branch to remote
git push -u origin <branch>     # push and set upstream
```

### Inspect
```bash
git log --oneline --graph       # compact commit graph
git diff                        # unstaged changes
git diff --staged               # staged changes
``` 

### Stash
```bash
git stash                       # stash uncommitted changes (tracked files)
git stash push -u               # stash including untracked files
git stash push -m "message"     # stash with a description
git stash list                  # list all stashes
git stash show stash@{0}        # show diff of a stash
git stash pop                   # apply latest stash and remove it
git stash apply stash@{1}       # apply a specific stash (keep it)
git stash drop stash@{0}        # delete a specific stash
git stash clear                 # delete all stashes
git stash branch <name>         # create a branch from a stash
```

## Upstream Tracking

`-u` / `--set-upstream` does two things at once: pushes the branch to the remote and links the local branch to the remote counterpart. After this one-time setup, plain `git push` and `git pull` work without specifying the remote or branch name.

```bash
git push -u origin <branch>           # push and set upstream (same name)
git push -u origin local:remote       # push with different local/remote names
```

Key points:
- Only needed the first time, or when a branch has no upstream configured yet.
- Local and remote branch names can differ, but keeping them the same is conventional.
- Check current upstream: `git branch -vv`

## Refresh All Branches

Fetch all remotes and prune deleted remote-tracking branches:

```bash
git fetch --all --prune
```

To also fast-forward all local branches that track a remote:

```bash
git fetch --all --prune && git branch -r | grep -v '\->' | while read remote; do
  git branch --track "${remote#origin/}" "$remote" 2>/dev/null || git branch -f "${remote#origin/}" "$remote" 2>/dev/null
done
```

Or with a simpler loop (updates only already-checked-out branch):

```bash
git fetch --all --prune
git pull --ff-only              # fast-forward current branch only
```
