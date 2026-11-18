---
title: 'Keyboard Shortcuts'
description: 'The keyboard shortcuts of the dashboard and the conditions under which they work.'
category: 'core-features'
slug: 'keyboard-shortcuts'
locale: 'en'
apiVersion: 'v1'
order: 11
updatedAt: '2026-11-18'
---

# Keyboard Shortcuts

The main actions of the dashboard can be run from the keyboard. To see the full list, press `?` in the dashboard or choose **Profile menu → Keyboard shortcuts** at the top right.

`⌘` in the tables is for macOS. On Windows and Linux, press `Ctrl`.

---

## Global

| Shortcut | Action |
| -------- | ------ |
| `?` | Show the keyboard shortcuts |
| `⌘ B` | Toggle the sidebar |
| `⌘ J` | Open or close the Ask AI panel (inside a Project, not for guests) |
| `Esc` | Close the open dialog, clear the selected contents or files |

---

## Go to

Press `G`, then the next key within 1.5 seconds. Do not hold both keys together.

| Shortcut | Destination |
| -------- | ----------- |
| `G` `H` | Project home |
| `G` `T` | Tables |
| `G` `F` | Files |
| `G` `C` | Categories |
| `G` `V` | Variables |
| `G` `L` | Translations |
| `G` `E` | API endpoints |
| `G` `K` | API keys |
| `G` `U` | Usage |
| `G` `S` | Project settings |
| `G` `W` | Workspace list |

- All of them except `G` `W` work only inside a Project.
- Usage and Project settings work only for roles that can see those menus. See [Member roles](/docs/developers/member-roles).
- On a [custom domain](/docs/core-features/custom-domains), `G` `W` opens the Project list of that Workspace.

---

## Contents list

These work on the contents list of a table.

| Shortcut | Action |
| -------- | ------ |
| `C` | Add a content (not on read-only tables) |
| `/` | Focus the keyword search |

---

## Contents form

These work while the contents form is open, including while you type.

| Shortcut | Action |
| -------- | ------ |
| `⌘ S` | Save as draft |
| `⌘ Enter` | Save and publish |

Both shortcuts first open the same confirmation as the buttons. Nothing is saved until you confirm. They do nothing on a locked content or when you cannot edit contents.

---

## When a shortcut does not work

- **While typing**: single-key shortcuts such as `?`, `C`, `/` and `G` are off while the cursor is in an input or the editor. Shortcuts with `⌘` still work.
- **While a dialog is open**: single-key shortcuts are off while a form, a confirmation or a menu is open. Close it with `Esc` first.
- **While the contents form is open**: `⌘ B` makes text bold in the editor, so it does not toggle the sidebar.

The shortcuts follow the physical key, so they also work while a non-Latin input method such as Korean is active.
