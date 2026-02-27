## disky

`disky` is a single-file Bash TUI for **interactive disk usage exploration**.

```
     ___
   /     \
  | DISK |
  |   Total: 926Gi
  |   Used:  515Gi
  |   Free:  336Gi
  | =================----------- |  61% used
   \___/

disky — disc space — /Users/me/Src/disky
↑/↓ move  Enter/→ expand  ← collapse  u up  q quit

> ├── .. (parent)  (go up)
  ├── ./  320K
  │   ├── .git/  ?
  │   ├── disky  16K
  │   ├── LICENSE  1K
  │   └── README.md  5K

```

### Key capabilities

- **Full‑screen TUI**: Renders a persistent interface in your terminal with a disk gauge and a scrollable tree.
- **Tree view**: Files and directories rendered with box‑drawing characters (`├──`, `└──`, `│`) and sizes.
- **Per‑directory navigation**: Pressing Enter/Right on a directory *enters* that directory and rebuilds the tree with it as the new root.
- **Session caching**: Directory listings and file sizes are cached (per process) so revisiting directories is fast.
- **Keyboard‑only navigation**: Optimized for arrow keys and `j`/`k`, no mouse required.

---

## Installation

### macOS / Linux (local clone)

```bash
git clone https://github.com/your-user/disky.git
cd disky
chmod +x disky
```

You can then run it directly with:

```bash
./disky             # analyze current directory
./disky /var        # analyze /var
./disky ~/Projects  # analyze your Projects folder
```

### Optional: put `disky` on your PATH

```bash
cp disky /usr/local/bin/disky   # may require sudo
```

After that:

```bash
disky
disky /some/path
```

---

## Usage & navigation model

The core idea of `disky` is **directory‑as‑root navigation**:

- At any moment there is a **current root** directory.
- The tree shows that root as `.` with its immediate children.
- Moving **into** a directory (Enter/Right) **changes the root** to that directory and rebuilds the tree.
- Moving **up** goes to the parent directory and rebuilds the tree.

This keeps the UI responsive and avoids deeply nested expansions.

### Starting the interface

```bash
disky            # or: ./disky
disky /tmp       # start rooted at /tmp
```

When the interface starts:

- The terminal screen is cleared.
- A header shows:
  - A **disk gauge** for the filesystem containing the root path.
  - A **hint line** with the key bindings.
- The main area shows the tree for the current root.

---

## Controls

| Key                     | Action                                                                 |
|-------------------------|------------------------------------------------------------------------|
| **↑** / **k**           | Move cursor up                                                        |
| **↓** / **j**           | Move cursor down                                                      |
| **Enter** / **→**       | If on `..`, go to parent directory; otherwise enter the selected directory as new root |
| **u**                   | Go up to parent directory (same as selecting `..` and pressing Enter) |
| **q**                   | Quit                                                                  |

### Tree rows

- `.. (parent)` — Appears at the top when the root is not `/`. Selecting this and pressing Enter/Right moves you one level up.
- `.` — The current root directory.
- `name/` — A subdirectory under the current root.
- `name` — A file under the current root.

Each row shows:

- **Name** (with `/` suffix for directories).
- **Size**:
  - For the root row: a `du -sh` style summary of the whole subtree.
  - For files: a fast stat‑based human size.
  - For directories: `?` (directory sizes are expensive; use the root size for high‑level estimates).

---

## Disk gauge

At the very top:

- Shows **Total**, **Used**, and **Free** space for the filesystem that contains the current root.
- Renders a bar where the filled portion reflects the percent used.

This updates automatically whenever you change the root directory.

---

## Performance & caching

- Directory listings and file sizes for the current process are cached under a temporary directory (e.g. `/tmp/disky_<pid>`).
- Large directories (more than a configurable threshold of entries) skip per‑file `stat` and just show `?` sizes to keep navigation responsive.
- The cache is cleaned up automatically on exit via a trap.

---

## Requirements

- **Bash** (the `disky` script uses Bash‑specific features).
- Standard Unix tools: `ls`, `du`, `df`, `stat`, `tput`, `stty`.
- A real terminal (TTY); behavior is undefined in non‑interactive environments.

---

## Compatibility notes

- **macOS**: Uses the BSD `stat` flags (`stat -f%z`). Tested on recent macOS releases.
- **Linux**: You may need to adjust the `stat` invocation if you port the script; currently the script is tailored for macOS/BSD‑style `stat`.
- **Terminal size**:
  - `disky` uses `tput lines` to determine how many tree rows fit on screen.
  - If the reported height is too small, it falls back to 24 lines.

---

## Troubleshooting

- **The interface looks garbled or doesn’t clear correctly**
  - Make sure you are running `disky` in a real terminal, not inside something that captures/filters ANSI escapes.
  - Check that `TERM` is set to a reasonable value (e.g. `xterm-256color`).

- **High CPU usage while idle**
  - `disky` should only repaint when something changes (tree dirty or input).
  - If you see constant CPU usage, check for issues with your terminal emulator or any wrapper tools.

- **Weird characters instead of tree lines**
  - Make sure your terminal is using UTF‑8 and a font that supports box‑drawing characters.

---

## Development notes

- All logic lives in a single Bash script: `disky`.
- The script avoids `set -e` because some commands (`clear`, `du`, `read`, etc.) can fail in normal interactive use.
- Terminal state (`stty`) is saved and restored on exit so your shell remains usable after quitting.

---

## License

`disky` is licensed under the **MIT License**.  
See the `LICENSE` file for full terms.
