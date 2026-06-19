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
git stash                       # stash uncommitted changes
git stash pop                   # restore stashed changes
```

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
