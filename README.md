# Murdoku Assistant — User Manual

*Leggi questo in [italiano](MANUALE.md).*

Murdoku Assistant is a digital worksheet for solving **Murdoku** puzzles (murdoku.com): it replaces pen and paper when marking clues and decisions on a grid. It doesn't solve anything for you and gives no hints — it just keeps track of what you write, in an orderly way.

It's a single, self-contained HTML page: no install, no account, no server. Just open `index.html` in a desktop browser.

## Contents

1. [What is a Murdoku](#1-what-is-a-murdoku)
2. [Getting started](#2-getting-started)
3. [Interface overview](#3-interface-overview)
4. [Setting up the grid](#4-setting-up-the-grid)
5. [How to play](#5-how-to-play)
6. [Keyboard shortcuts](#6-keyboard-shortcuts)
7. [Undo/Redo](#7-undoredo)
8. [Highlights](#8-highlights)
9. [Timer](#9-timer)
10. [Export/Import](#10-exportimport)
11. [Autosave and its limits](#11-autosave-and-its-limits)
12. [Switching language](#12-switching-language)
13. [Rectangular grids](#13-rectangular-grids)
14. [FAQ](#14-faq)

## 1. What is a Murdoku

A Murdoku is a logic-deduction puzzle: on an N×N (or rectangular) grid, every row and every column hides a single "murderer" letter — exactly like non-attacking rooks on a chessboard, where once a rook is placed no other rook may share its row or column. Through clues and deduction you narrow things down until, for each letter, only one possible cell remains: that becomes the **decision**.

## 2. Getting started

Open `index.html` by double-clicking it, or "Open with" → your preferred browser. Nothing to install, no internet connection needed after the first load (this app uses no external fonts or resources anyway).

The app is designed for desktop use with mouse and keyboard: there is no dedicated touch interaction path.

## 3. Interface overview

The layout mirrors the original Murdoku board: a top bar plus three columns.

- **Top bar**: the puzzle name (a free text field you can fill in), a badge with the grid size and the number of suspects, the timer with its start/pause and reset buttons, the language switch, and **?** which opens the "How to play" window.
- **Left panel — Suspects**: one card per valid letter, each with a coloured token (the letter), an optional name field, and a green ✓ once that letter has been placed as a decision. The victim (`V`) has its own red card. Underneath: the **clue / decision** switch, a progress bar with the letters still to place, and the four **note symbols**.
- **Centre — the table**: the optional background image with the grid overlaid on top, plus `R1…Rn` / `C1…Cn` labels along the edges of the box. The label of the row and column of the selected cell is highlighted, and the whole row/column is lightly tinted, so you can follow the rook constraint at a glance.
- **Right panel — Tools**: the big **✕** button (exclusion tool), **↖ Select only**, "hold to highlight empty cells", undo/redo, the grid settings (rows, columns, lock/unlock), the background image (load, **detect grid**, remove), and the session commands (export, import, clear all, how to play).

A chip in the top bar always shows **what the next click will write** ("D · Donna — DECISION: click to place"), and hovering a square previews it in place — worth a glance before clicking, because a decision cannot be undone except with Undo.

**Suspect names** are optional and are only there to help you: in a real Murdoku the suspects' initials are in alphabetical order (August, Barnaby, Clarence…) and the victim's name starts with V, so you can type the names printed on your puzzle into the cards and read the grid in terms of people rather than letters. Names are saved and exported along with the puzzle; they never affect the rules.

## 4. Setting up the grid

- **Rows/Columns**: two number fields in the right-hand Tools panel, from 2 to 22 each. They don't have to be equal: the grid can be rectangular. Changing either value, after confirmation, clears the grid's contents (clues, decisions, exclusions) — not the size/position of the grid box.
- **Background image** (optional): "Load image" to load a photo of the puzzle to draw the grid over; "Remove image" to drop it without touching the grid. Without an image, the app simply shows an empty grid on a neutral background.
- **Automatic detection**: as soon as you load a photo, the app tries to work out **the grid size and the position of the box** on its own, and applies them. It copes with a slightly tilted, blurry or unevenly lit phone photo, and with wide page margins. When it isn't sure it applies nothing and says so — better no answer than a wrong one, since applying it clears the grid. **🔍 Detect grid** runs the same detection again by hand (useful after you've straightened or re-cropped the photo).
- **Positioning/resizing the grid box**: while the grid is **unlocked** (see below), you can drag the body of the yellow box to move it over the image, or drag one of the four corner handles to resize it, so it lines up with the actual grid drawn in the photo.
- **Lock/Unlock** (the 🔒 **Grid locked** / 🔓 **Grid unlocked** button in the Tools panel): while **unlocked**, you can move/resize the box but not edit cells, and a banner at the top of the table reminds you of it. While **locked**, the box is fixed and you can click/navigate between cells to fill them in. This prevents accidentally moving the grid while you're playing.

## 5. How to play

You can work either way, and the two are equivalent: **click** a square with a tool selected, or move the selection with the **arrow keys** and type. Either way the grid must be locked.

- **Lowercase letter → clue**: notes that this letter is *possible* in that cell. Several clues can coexist in the same cell (e.g. "could be A or C"). A clue is no longer possible for a letter already decided elsewhere on the grid.
- **Shift+letter (uppercase) → decision**: declares that this letter *is* that cell, permanently. Only allowed on an empty cell (no X, no existing decision) and only if that letter hasn't already been decided elsewhere. Once placed:
  - every clue for that letter is removed from every other cell on the grid;
  - every other cell sharing that row or that column is automatically **excluded (X)** — the "rook constraint": one letter per row, one per column.
  - **There is no way to remove a decision by clicking**: the only way back is **Undo**.
- **`X` → exclusion**: declares that no letter can go in that cell. Toggles freely (key `x`, works whether typed lowercase or uppercase) as long as the cell doesn't already hold a decision. Setting `X` on a cell also clears its clues.
- If a cell already has an `X`, you cannot type a clue or a decision into it: you can only remove the `X` by pressing `x` again.
- **Note symbols (keys `1`–`4`) → free annotation**: ▲ ● ■ ★ are yours to use however you like (for example "checked", "impossible for two reasons", "come back to this"). They mean nothing to the rules, one symbol per cell, and pressing the same one again removes it. They can't be placed on a cell holding an `X` or a decision.

Decisions, clues and note symbols are drawn in the suspect's own colour, the same one shown on their card, so you can recognise a letter on the grid without reading it.

**Placing with the mouse**: pick a tool — a suspect card, the ✕ button, a note symbol — and then click a square: the click fills it in straight away. Click the same square again to toggle a clue, an `X` or a symbol off. If you'd rather move around the grid without writing anything, switch on **↖ Select only**: with that active, clicking just moves the selection.

**Which letters are available?** The maximum number of decisions that fit on the whole grid is `min(rows, columns)` (the rook constraint exhausts the shorter dimension first). Valid letters are the first `min(rows,columns)-1` letters of the alphabet (a, b, c, ...) plus a fixed `v` as the last one; `w/x/y/z` are never game letters — `x` stays free for the exclusion tool. On a rectangular grid, some cells along the longer dimension will necessarily remain without a decision even once the puzzle is fully solved: this is correct and expected.

## 6. Keyboard shortcuts

| Key | Effect |
|---|---|
| Arrow keys | Move the selected cell |
| Lowercase letter | Clue in the selected cell |
| Shift + letter | Decision in the selected cell |
| `x` | Toggle exclusion |
| `1` – `4` | Apply/remove a note symbol |
| Ctrl+Z | Undo |
| Ctrl+Y (or Ctrl+Shift+Z) | Redo |

Shortcuts only work while the grid is **locked** and focus isn't on a text field (rows/columns, puzzle name, suspect names) — inside those fields Ctrl+Z stays the browser's own text undo.

## 7. Undo/Redo

Every change (X, clue, decision, moving/resizing the grid box, changing dimensions, clear all) is saved as a snapshot in the history. The **↶ Undo** / **↷ Redo** buttons in the Tools panel (or Ctrl+Z / Ctrl+Y) step backward and forward through these snapshots. The puzzle name and the suspect names are deliberately left out of the history: typing a name never creates an undo step.

The history lives in memory only: **it's lost on page reload** (unlike the grid state itself, see next section). "Clear all" also resets the history: afterwards you can no longer go back to before the clearing.

## 8. Highlights

Two buttons work as "press and hold": they show a temporary highlight on the grid only while the mouse button stays pressed, and remove it on release (even if release happens outside the button).

- **Suspect cards** in the left panel: holding one down highlights every cell where that letter appears as a clue or a decision.
- **The 👁 button** in the Tools panel: highlights every cell that still has no `X` and no decision (clues don't count — a cell with only clues is still considered "empty" for this purpose).

## 9. Timer

Measures solving time. It starts automatically on the first interaction with the grid (no need to press "Start"). Controls in the top bar:

- **▶ Start / ⏸ Pause**: start or pause it manually.
- **⟲ Reset**: resets the accumulated time (without stopping the timer if it was running).

The timer value is saved together with the grid state (see section 11), but it's **not included** when you export to a file.

## 10. Export/Import

- **Export**: asks for a name (pre-filled with the puzzle name, if you set one), then downloads a `.json` file with the full state (dimensions, image, cell contents, puzzle name, suspect names) — not the timer value nor the undo history. Use it to save a puzzle you're not currently working on, or to share it.
- **Import**: loads a previously exported `.json` file, **overwriting** (after confirmation) the current work session. Also resets the timer.

## 11. Autosave and its limits

The app automatically saves your work in progress (dimensions, image, cell contents, puzzle name, suspect names, timer) as you go, with nothing to press. **Note an intentional but non-obvious behavior**: this autosave is tied to the single browser tab (technically: `sessionStorage`, not `localStorage`).

- **Pressing F5 / reloading the page in the same tab**: your work stays exactly as you left it.
- **Opening a new tab or window** (even for the exact same file): starts fresh with an empty grid — even if another tab still has work in progress.
- **Closing the tab/browser**: the work is lost, unless you exported it to a file with "Export" beforehand.

If you want to keep a puzzle long-term, or switch between puzzles, always use **Export**.

## 12. Switching language

The interface starts in **English**. The **EN**/**IT** button in the top bar toggles it between English and Italian; your choice is remembered (in this browser, on this computer) and applied again on every later visit — it's independent of the puzzle autosave described above.

## 13. Rectangular grids

Rows and columns can differ. In that case the number of available letters/decisions stays `min(rows, columns)`: the shorter dimension runs out first because of the rook constraint, so some cells along the longer dimension necessarily remain without a decision even once the puzzle is fully solved. This is correct behavior, not a bug.

## 14. FAQ

**I closed the tab and lost all my work. How do I get it back?**
It can't be recovered unless you had exported it to a file. See section 11: export regularly if a puzzle spans more than one session.

**I placed a decision by mistake, how do I remove it?**
It can't be removed by clicking. Use Undo (Ctrl+Z or the button in the Tools panel) until you're back to the state before that decision.

**Why can't I type a clue or a decision into a cell?**
Check whether that cell already has an `X` (in which case you can only remove the `X`), already has a decision, or whether that letter has already been decided elsewhere on the grid — in each case a message at the bottom explains why.

**Why do some letters always stay "to place" even on a full grid?**
Only on rectangular grids: this is normal, see section 13.
