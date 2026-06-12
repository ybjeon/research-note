# tmux

## Commands

**Create session**
```bash
tmux new-session -s session_name
tmux new-s session_name
```

**List sessions**
```bash
tmux list-sessions
tmux ls
```

**Attach to session**
```bash
tmux attach-session -t session_name
tmux attach -t session_name
tmux a -t session_name
```

**Detach from session**
```bash
tmux detach-client
Ctrl+b d
```

**Rename session**
```bash
tmux rename-session -t old_name new_name
```

**Kill session**
```bash
tmux kill-session -t session_name
```

**Create new window**
```bash
Ctrl+b c
```

**Switch window**
```bash
Ctrl+b n (next)
Ctrl+b p (previous)
Ctrl+b 0-9 (specific window number)
```

**Create pane (split)**
```bash
Ctrl+b % (vertical)
Ctrl+b " (horizontal)
```

**Switch pane**
```bash
Ctrl+b arrow keys
```

**Kill pane/window**
```bash
Ctrl+b x
```
