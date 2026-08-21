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

The app is split into three areas:

- **Header (top)**: grid dimensions, background image, clear all, lock/unlock, undo/redo, export/import, language switch.
- **Central area**: the optional background image with the grid overlaid on top.
- **Bottom bar**: letter buttons (one per valid letter), clue/decision mode, highlight empty cells, timer, status of letters still to be placed.

## 4. Setting up the grid

- **Rows/Columns**: two number fields in the header, from 2 to 22 each. They don't have to be equal: the grid can be rectangular. Changing either value, after confirmation, clears the grid's contents (clues, decisions, exclusions) — not the size/position of the grid box.
- **Background image** (optional): "Load image" to load a photo of the puzzle to draw the grid over; "Remove image" to drop it without touching the grid. Without an image, the app simply shows an empty grid on a neutral background.
- **Positioning/resizing the grid box**: while the grid is **unlocked** (see below), you can drag the body of the blue box to move it over the image, or drag one of the four corner handles to resize it, so it lines up with the actual grid drawn in the photo.
- **Lock/Unlock** (🔒 button in the header): while **unlocked**, you can move/resize the box but not edit cells. While **locked**, the box is fixed and you can click/navigate between cells to fill them in. This prevents accidentally moving the grid while you're playing.

## 5. How to play

All input happens **via keyboard**: clicking a cell only selects it (the grid must be locked). Use the **arrow keys** to move the selection, then type a letter.

- **Lowercase letter → clue**: notes that this letter is *possible* in that cell. Several clues can coexist in the same cell (e.g. "could be A or C"). A clue is no longer possible for a letter already decided elsewhere on the grid.
- **Shift+letter (uppercase) → decision**: declares that this letter *is* that cell, permanently. Only allowed on an empty cell (no X, no existing decision) and only if that letter hasn't already been decided elsewhere. Once placed:
  - every clue for that letter is removed from every other cell on the grid;
  - every other cell sharing that row or that column is automatically **excluded (X)** — the "rook constraint": one letter per row, one per column.
  - **There is no way to remove a decision by clicking**: the only way back is **Undo**.
- **`X` → exclusion**: declares that no letter can go in that cell. Toggles freely (key `x`, works whether typed lowercase or uppercase) as long as the cell doesn't already hold a decision. Setting `X` on a cell also clears its clues.
- If a cell already has an `X`, you cannot type a clue or a decision into it: you can only remove the `X` by pressing `x` again.

**Which letters are available?** The maximum number of decisions that fit on the whole grid is `min(rows, columns)` (the rook constraint exhausts the shorter dimension first). Valid letters are the first `min(rows,columns)-1` letters of the alphabet (a, b, c, ...) plus a fixed `v` as the last one; `w/x/y/z` are never game letters — `x` stays free for the exclusion tool. On a rectangular grid, some cells along the longer dimension will necessarily remain without a decision even once the puzzle is fully solved: this is correct and expected.

## 6. Keyboard shortcuts

| Key | Effect |
|---|---|
| Arrow keys | Move the selected cell |
| Lowercase letter | Clue in the selected cell |
| Shift + letter | Decision in the selected cell |
| `x` | Toggle exclusion |
| Ctrl+Z | Undo |
| Ctrl+Y (or Ctrl+Shift+Z) | Redo |

Letter/X shortcuts only work while the grid is **locked** and focus isn't on a text field (e.g. the rows/columns inputs).

## 7. Undo/Redo

Every change (X, clue, decision, moving/resizing the grid box, changing dimensions, clear all) is saved as a snapshot in the history. The **↶ Undo** / **↷ Redo** buttons in the header (or Ctrl+Z / Ctrl+Y) step backward and forward through these snapshots.

The history lives in memory only: **it's lost on page reload** (unlike the grid state itself, see next section). "Clear all" also resets the history: afterwards you can no longer go back to before the clearing.

## 8. Highlights

Two buttons work as "press and hold": they show a temporary highlight on the grid only while the mouse button stays pressed, and remove it on release (even if release happens outside the button).

- **Letter buttons** in the bottom bar: holding one down highlights every cell where that letter appears as a clue or a decision.
- **"👁 Highlight empty"**: highlights every cell that still has no `X` and no decision (clues don't count — a cell with only clues is still considered "empty" for this purpose).

## 9. Timer

Measures solving time. It starts automatically on the first interaction with the grid (no need to press "Start"). Controls in the bottom bar:

- **▶ Start / ⏸ Pause**: start or pause it manually.
- **⟲ Reset**: resets the accumulated time (without stopping the timer if it was running).

The timer value is saved together with the grid state (see section 11), but it's **not included** when you export to a file.

## 10. Export/Import

- **Export**: asks for a name, then downloads a `.json` file with the full grid state (dimensions, image, cell contents) — not the timer value nor the undo history. Use it to save a puzzle you're not currently working on, or to share it.
- **Import**: loads a previously exported `.json` file, **overwriting** (after confirmation) the current work session. Also resets the timer.

## 11. Autosave and its limits

The app automatically saves your work in progress (dimensions, image, cell contents, timer) as you go, with nothing to press. **Note an intentional but non-obvious behavior**: this autosave is tied to the single browser tab (technically: `sessionStorage`, not `localStorage`).

- **Pressing F5 / reloading the page in the same tab**: your work stays exactly as you left it.
- **Opening a new tab or window** (even for the exact same file): starts fresh with an empty grid — even if another tab still has work in progress.
- **Closing the tab/browser**: the work is lost, unless you exported it to a file with "Export" beforehand.

If you want to keep a puzzle long-term, or switch between puzzles, always use **Export**.

## 12. Switching language

The 🌐 button in the header toggles the interface between Italian and English. The choice is remembered (in this browser, on this computer) and applied again on every later visit — it's independent of the puzzle autosave described above.

## 13. Rectangular grids

Rows and columns can differ. In that case the number of available letters/decisions stays `min(rows, columns)`: the shorter dimension runs out first because of the rook constraint, so some cells along the longer dimension necessarily remain without a decision even once the puzzle is fully solved. This is correct behavior, not a bug.

## 14. FAQ

**I closed the tab and lost all my work. How do I get it back?**
It can't be recovered unless you had exported it to a file. See section 11: export regularly if a puzzle spans more than one session.

**I placed a decision by mistake, how do I remove it?**
It can't be removed by clicking. Use Undo (Ctrl+Z or the header button) until you're back to the state before that decision.

**Why can't I type a clue or a decision into a cell?**
Check whether that cell already has an `X` (in which case you can only remove the `X`), already has a decision, or whether that letter has already been decided elsewhere on the grid — in each case a message at the bottom explains why.

**Why do some letters always stay "to place" even on a full grid?**
Only on rectangular grids: this is normal, see section 13.
