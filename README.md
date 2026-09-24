# Sudoku Variant Studio 🧩

[![Live Demo](https://img.shields.io/badge/Live_Demo-GitHub_Pages-6366f1?style=for-the-badge&logo=github)](https://danielecursano.github.io/sudoku-studio/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Lucide Icons](https://img.shields.io/badge/Lucide_Icons-F97316?style=for-the-badge&logo=lucide&logoColor=white)](https://lucide.dev/)

An advanced, interactive browser-based studio for creating, playing, and verifying standard and championship Sudoku variants. Features custom puzzle layout design, real-time constraint validation, candidate pencil marks, automatic candidate annotation, solve timer controls, and a dynamic in-browser JavaScript rule builder.

**Live Application Reference**:  
👉 **[https://danielecursano.github.io/sudoku-studio/](https://danielecursano.github.io/sudoku-studio/)**

---

## 🌟 Key Features

### 🎮 Dual-Mode Studio Workflow
- **Setup Givens Mode**: Place initial numbers to create and author your custom puzzle layout from scratch.
- **Play Mode**: Solve your custom creation or sample puzzles with live conflict detection, wavy error underlines, and victory verification.

### ⏱️ Solve Timer with Dedicated Reset
- **Real-Time Stopwatch**: Visible across both desktop and mobile layouts.
- **Reset Button**: One-click timer reset button (`rotate-ccw`) located right in the header timer widget to restart speedruns at any moment.
- **Auto-Stop & Score Reporting**: Automatically halts upon solving the puzzle and displays your official completion time.

### 🏆 Championship Variants Supported
1. **Standard Sudoku**: Traditional 9x9 grid with rows, columns, and 3x3 blocks containing digits 1–9.
2. **Anti-Knight Sudoku**: Cells separated by a chess knight move (L-shape: 2 by 1) cannot contain identical digits. Includes an interactive SVG overlay showing target knight jumps for the selected cell.
3. **Anti-King Sudoku**: Diagonally and orthogonally adjacent cells (chess king move) cannot contain the same digit.
4. **Diagonal Sudoku (X-Sudoku)**: Both main diagonals (top-left to bottom-right, bottom-left to top-right) must contain digits 1–9 without duplicates. Features visual dashed diagonal guide lines across the board.
5. **Non-Consecutive Sudoku**: Orthogonally adjacent cells cannot contain consecutive numbers (e.g. adjacent 4 and 5 are disallowed).

### 🛠️ Dynamic Custom JS Rule & Verifier Builder
- Write your own custom constraint logic in vanilla JavaScript directly in the browser.
- Define a verifier function:
  ```javascript
  isValid(grid, r, c, val)
  ```
- Instant compilation, runtime safety protections, and active board integration.

### ✏️ Candidate Notes & Solver Helpers
- **Pencil Mode**: Easily toggle candidate notes inside cells.
- **Auto Annotate All**: Computes and fills all mathematically valid candidate pencil marks across all empty cells according to the active variant constraints.
- **Validate**: Checks current board state for constraint violations.
- **Full Undo History**: Complete multi-step undo stack supporting board digits, givens, mode changes, and candidate pencil marks (`Ctrl+Z` / `Cmd+Z`).
- **Clear Notes & Clear Board**: Quickly clear player notes or wipe the entire board to start fresh.

### 🌓 Modern, Responsive UI
- **Light & Dark Themes**: Smooth theme toggling with automatic system preference detection and `localStorage` persistence.
- **Virtual Numpad**: On-screen digits and erase controls for touch and mouse interaction.
- **Accessible & Responsive**: Fully optimized for desktops, tablets, and mobile screens.

---

## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
| :--- | :--- |
| `1` – `9` | Place digit / toggle pencil note |
| `0` / `Backspace` / `Delete` | Erase cell digit or clear candidate notes |
| `Arrow Keys` (`↑` `↓` `←` `→`) | Move grid cursor |
| `P` | Toggle Pencil Mode ON / OFF |
| `Ctrl + Z` / `Cmd + Z` | Undo last action |
| `Escape` | Close active modal dialog |

---

## 🚀 Running Locally

No build step, bundler, or dependencies required! Sudoku Studio runs directly in any modern web browser.

### Option 1: Direct File Opening
Double-click `index.html` or open it with your browser:
```bash
open index.html # On macOS
```

### Option 2: Local HTTP Server
Using Python 3:
```bash
python3 -m http.server 8000
```
Then navigate to [http://localhost:8000](http://localhost:8000) in your browser.

Using Node.js:
```bash
npx serve .
```

---

## 🔗 Reference & Deployment

The application is deployed on GitHub Pages:
- **URL**: [https://danielecursano.github.io/sudoku-studio/](https://danielecursano.github.io/sudoku-studio/)
- **Repository**: [https://github.com/danielecursano/sudoku-studio](https://github.com/danielecursano/sudoku-studio)

---

## 📄 License

Open-source under the [MIT License](LICENSE). Contributions, variant ideas, and improvements are welcome!
