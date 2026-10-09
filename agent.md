# Sudoku Variant Studio: build prompt and development history

This document lets an AI assistant recreate the project from scratch. Part 1 is the story of how it evolved, which explains *why* things are the way they are. Part 2 is a consolidated spec. Part 3 is a ready-to-paste prompt.

---

## Part 1: Development history

### Motivation
There are many websites for Sudoku variants, but each covers a single variant (Anti-Knight on one site, X-Sudoku on another, Non-Consecutive on a third), each with its own layout, controls and idea of what a pencil mark is. The goal was **one board, one set of tools, and the variant is just a rule you pick**.

### Iteration 1: first version
A single HTML file using Tailwind (CDN), Lucide icons (CDN) and Google Fonts. It had:
- A 9x9 board with two modes: **Setup Givens** (type the puzzle in) and **Play** (solve it).
- Variants: Standard, Anti-Knight, Anti-King, Diagonal (X-Sudoku), Non-Consecutive. Each variant is a rule function `check(grid, r, c, val)`.
- A custom rule builder (user writes a JS function body) in a modal.
- Pencil marks, undo, "annotate all", validate, clear notes, a timer with reset, a theme toggle.

### Iteration 2: cleanup and usability pass
Requested changes:
1. **Light and dark layouts** that both look good.
2. **Highlight matching numbers** when a cell with a number is selected, **including numbers inside pencil notes**.
3. **Cleaner and more user friendly.**
4. **No alerts or popup banners** when clicking buttons. Banners that covered the board were removed. Feedback goes into one calm status line under the board.
5. **Hide useless buttons once a puzzle starts.** Setup-only controls (clear board, sample, variant picker, custom rule editor) disappear in play mode. Redundant buttons (Validate, Clear Notes, timer reset) were removed outright, because conflicts are shown live in red and the win is detected automatically.

Design decisions made at that point:
- Dropped Tailwind, Lucide and web fonts for a **self-contained file** with CSS variables, inline SVG icons and a system font stack.
- Replaced the two modals with an inline `<select>` for the variant, and a custom rule editor that only shows when "Custom rule…" is selected during setup.
- Show rule conflicts in **both** setup and play mode.
- Added extras that make play nicer: remaining-count under each numpad digit, automatic removal of a placed digit from peers' pencil notes, Undo disabled when there is nothing to undo.

### Iteration 3: About page
An **About page** was added, reached from an info button in the header. It explains the motivation, what the tool can do, shortcuts and privacy. The timer pauses while the About page is open.

### A false start worth remembering: no "Import"
An import feature (paste 81 characters) was briefly added so the About page could claim "import puzzles". The author did not want it. The intended workflow is simply: **recreate an external puzzle, for example from a championship, by typing its givens on the first screen**. Import was removed and the About text reworded. Do not add an import function unless asked.

### Ideas discussed but not built
- **Puzzle generator** for every variant: generate a full solution by randomized backtracking using each variant's `check`, then remove clues while a solution-counting solver confirms uniqueness (run in a Web Worker). Easy for Standard and Diagonal, moderate for the Anti-* variants, hardest for Non-Consecutive and custom rules. Difficulty grading would need a human-technique solver.

---

## Part 2: Spec of the final product

**Deliverable:** one file, `sudoku-variant-studio.html`. No build step, no external requests, no dependencies.

### Layout
- Single centered column, max width 520px, mobile first.
- Header: logo and title on the left; on the right a timer pill (play mode only), an About button and a theme toggle.
- Order: variant card, board, status line, toolbar, numpad, primary actions.
- About view replaces the game view (`body[data-view]`). A "Back to puzzle" button is at the bottom.

### Theming
- CSS variables on `:root` (light) and `[data-theme="dark"]`.
- Initial theme is the saved choice (localStorage, wrapped in try/catch) or `prefers-color-scheme`. Set it in an inline `<head>` script to avoid a flash.
- Variables needed: bg, surface, text, muted, line, line-strong, accent, accent-soft, accent-text, cell, peer, same, same-soft, sel, given, user, error, error-bg, ok.

### Modes (`body[data-mode]`)
- **setup:** typing a digit sets or clears a given. Toolbar: Undo, Erase, Sample, Clear. Action: "Start puzzle" (disabled while the board is empty or the givens break the rules). Variant `<select>` and custom rule editor visible.
- **play:** givens are locked, the timer runs. Toolbar: Undo, Erase, Notes (toggle), Auto notes. Actions: "Edit puzzle" (removes only the player's entries and returns to setup) and "New puzzle" (clears everything; it becomes the primary button when solved). Variant shown as a read-only name and description.
- Visibility is done purely with CSS classes `.setup-only` and `.play-only`.

### Board and highlighting
- 81 `div.cell` elements in a 9x9 CSS grid, thicker lines between 3x3 boxes, outer border. Font sizes use container query units (`cqw`).
- Rebuild the board HTML on every `render()`. Use event delegation for clicks.
- When the selected cell contains digit *d*:
  - every cell containing *d* gets a strong highlight (`same`);
  - every empty cell whose notes include *d* gets a soft tint (`same-note`), and the *d* inside its notes is drawn as a filled accent-colored circle (`hl`);
  - the selected cell has an accent ring;
  - its row, column and box get a faint tint (`peer`);
  - the matching numpad key is highlighted.
- Given digits are bold; player digits use the accent color; conflicting digits are red on a red-tinted background.
- Variant overlays are an SVG on top of the board (`viewBox="0 0 9 9"`, `preserveAspectRatio="none"`). Diagonal draws two dashed lines using `vector-effect="non-scaling-stroke"`.

### Rules engine
`placementOk(r, c, val)` temporarily blanks the cell, checks row, column and box, then calls the variant check (or the custom function), then restores the cell. Variants are plain objects `{id, name, description, check(g, r, c, val), overlay?}`:
- Standard: no extra rule.
- Anti-Knight: no equal digit a chess knight's move away.
- Anti-King: no equal digit in any of the 8 touching cells.
- Diagonal: both main diagonals contain no repeated digit.
- Non-Consecutive: orthogonal neighbours cannot differ by exactly 1 (empty neighbours ignored).
- Custom: the user writes a function body for `(grid, r, c, val)`. Compile with `new Function`, smoke-test it on an empty grid, show errors inline (never with `alert`), and treat a throwing function as "invalid".

### Interaction details
- Digits 1–9 place a digit, or toggle a note when Notes is on in play mode. Pressing the same digit again clears it.
- Placing a digit in play mode removes it from the notes in its row, column and box.
- Auto notes fills every empty cell's notes with the candidates allowed by the active variant, then turns Notes off.
- Numpad keys show remaining counts (9 minus placed), and are disabled when the count reaches zero.
- Undo snapshots grid, givens, notes and mode (max 100). Starting a puzzle resets the history.
- Keyboard: 1–9, Backspace/Delete/0 erase, arrows move, `P` toggles notes (play mode), Ctrl/Cmd+Z undoes. Ignore keys while typing in an input, textarea or select, and while the About view is open.
- Solved means play mode, all 81 cells filled and no conflicts. It stops the timer and shows a success line; it is derived inside `render()`, not stored as state.
- Timer: starts at "Start puzzle", pauses when solved or while the About view is open, and is shown only in play mode.

### Feedback rules
- **No `alert`, `confirm` or `prompt`, no toasts, no banners over the board.**
- The only feedback surface is the status line (`role="status"`, fixed minimum height so the layout never jumps), plus inline errors in the custom rule editor.

### About page content
Written in the author's first person: why it was built (many single-variant sites, no unified one), what you can do (play every variant, recreate external puzzles such as championship ones by typing the givens, use utilities like Auto notes and highlighting on them, write custom rules), the three-step flow for an external puzzle, keyboard shortcuts, and privacy (everything stays in the browser; only the theme choice is saved). State honestly that only given digits and the built-in rules are supported, so cages, arrows and thermometers can't be set up.

### Accessibility and polish
Buttons have `aria-label` or visible text, the board has `role="grid"` and `gridcell`, visible `:focus-visible` outlines, the Notes toggle uses `aria-pressed`, and the status line is a live region.

---

## Part 3: Ready-to-paste prompt

> Build a single self-contained file `sudoku-variant-studio.html` (vanilla HTML, CSS and JS, no CDN, no frameworks, no external requests). It is a 9x9 Sudoku app that supports multiple variants in one place: Standard, Anti-Knight, Anti-King, Diagonal (X) and Non-Consecutive, plus a custom rule written as a JS function body `(grid, r, c, val)`.
>
> The app has a **setup mode**, where the user types the given digits of any puzzle (for example one copied from a championship), and a **play mode**, where givens are locked and a timer runs. Setup-only controls (Sample, Clear, variant select, custom rule editor, Start) must be hidden in play mode, and play-only controls (Notes, Auto notes, Edit puzzle, New puzzle, timer) hidden in setup. Keep the UI minimal: one centered column, mobile first.
>
> Requirements: light and dark themes (system default, toggle, remembered). When a cell containing a digit is selected, highlight every cell with that digit, and also highlight that digit inside other cells' pencil notes (filled circle), plus a faint tint on the row, column and box and on the matching numpad key. Show rule conflicts live in red. Numpad keys show remaining counts. Placing a digit removes it from peers' notes. Undo, erase, auto-fill notes and keyboard shortcuts must work. Win detection is automatic. Never use `alert`, `confirm`, popups or banners over the board; use one status line under the board and inline errors only. Add an About page, opened from a header info button, written in the author's first person: many sites exist for individual variants but none unifies them, so this is one board where you can play every variant, recreate external puzzles by typing the givens, and use utilities like annotation and highlighting on them. Do **not** implement any import or paste function.
>
> Follow the structure and details in the spec section of this document.