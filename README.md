# disky

Interactive disc space analysis with an expandable tree view. Browse any directory, see sizes at a glance, and expand only the folders you care about.

## Features

- **Tree view** — Files and directories shown in a tree with box-drawing characters (`├──`, `└──`, `│`).
- **Sizes** — Each file and directory shows its size (from `du`).
- **Expand / collapse** — Enter or → expands a directory; ← collapses it. Subdirectories load on demand.
- **Keyboard navigation** — ↑/↓ or j/k move the cursor; no mouse required.
- **Caching** — Directory listings and sizes are cached for the session so expanding is fast.

## Usage

```bash
./disky              # Analyze current directory
./disky /var         # Analyze /var
./disky ~/Projects   # Analyze your Projects folder
```

## Controls

| Key        | Action                          |
|-----------|----------------------------------|
| **↑** / **k** | Move cursor up                   |
| **↓** / **j** | Move cursor down                 |
| **Enter** / **→** | Expand directory (or collapse if already expanded) |
| **←**     | Collapse directory or move to parent |
| **q**     | Quit                            |

Select the **↑ ..** entry at the top of the tree and press **Enter** or **→** to navigate up to the parent directory.

## Requirements

- Bash
- `du` and `ls` (standard on macOS and Linux)

## Example

```
disky — disc space — /Users/you/project
↑/↓ move  Enter/→ expand  ← collapse  q quit

> .  2.1G
  ├── src/  800M
  │   ├── app/  400M
  │   └── lib/  400M
  ├── node_modules/  1.2G
  └── README.md  4.0K
```
