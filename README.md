# End-to-End Prototype — Usability Test Tasks

A task-oriented variant of the `e2e-prototype` / `e2e-prototype-v2` clickable prototype
(same screens, same Figma source `Ei3vesaWdzL7bKm5sc03Jo`, same Lydia design system), built
specifically for moderated usability testing: **one distinct URL per research task**, each
starting cold at the right screen and ending on a dedicated success screen.

**Live:** https://ayamaguchi-avid.github.io/e2e-prototype-tasks/
**Source:** https://github.com/ayamaguchi-avid/e2e-prototype-tasks

For the full build history and design rationale behind the underlying screens, see
[`e2e-prototype`](https://github.com/ayamaguchi-avid/e2e-prototype)'s README — this repo only
documents what's different here: the task wrapper.

## Task links

| Task | Prompt | Screens | Start URL | Success screen |
|---|---|---|---|---|
| 1 | Select a bill provider to enroll and log in to its portal | 1–7 | [`/task-1.html`](task-1.html) | `success-task-1.html`, reached by clicking "Enroll" on the final confirmation modal |
| 2 | View the provider page and enroll in Priority Payments | 8–18 | [`/task-2.html`](task-2.html) | `success-task-2.html`, reached by clicking "Enroll" on Review & Enroll |
| 3 | Explain the difference between Auto-Initiate Payment and Auto-Approve | 15–18 | [`/task-3.html`](task-3.html) | `success-task-3.html`, via a moderator-triggered "Mark Task Complete" button on Review & Enroll |
| 4 | Approve a payment, explain why it wasn't auto-approved, and find where to check payment/approval status | 19–24 | [`/task-4.html`](task-4.html) | `success-task-4.html`, via a moderator-triggered "Mark Task Complete" button once a payment's detail panel is open |
| 5 | Auto Match A/B comparison ("Auto match 1–20") | — | *not yet built* | Blocked on Figma access to the `CAB` file/node `7056-42752` — see below |

A hub page at [`/index.html`](index.html) lists all five.

## How each task is isolated

Every underlying screen is otherwise unchanged from `e2e-prototype-v2` — this is additive, not
a fork of the app logic:

- Each `task-N.html` is a small briefing page. Its "Begin Task" button sets
  `sessionStorage.activeTask = 'N'` and navigates to that task's real starting screen
  (`task-4.html` also clears the `tsRow1Approved`/`tsRow3Approved` localStorage flags first, so
  the Time Sensitive table always starts pristine for a new participant).
- Three shared screens read `sessionStorage.activeTask` to branch, but only when it's set —
  with no active task, they behave exactly like the normal end-to-end demo:
  - `screens/wizard-confirmation.html` — Task 1's "Enroll" button goes to `success-task-1.html`
    instead of `home-connected.html`.
  - `screens/pp-review-enroll.html` — Task 2's final "Enroll" link goes to `success-task-2.html`
    instead of `home-connected.html`; Task 3 instead gets a floating "Mark Task Complete" button
    (there's no single terminal action for a comprehension check).
  - `screens/pay-home.html` — Task 4 gets the same floating "Mark Task Complete" button, shown
    once any payment's detail panel is open (whether reached by clicking a row or via the
    existing `?open=1` auto-select).
- Each `success-task-N.html` clears `activeTask` and links to "Restart this task" and "Continue
  to Task N+1."

## Task 5 — blocked

The "Auto match" flow (~20 screens) referenced at
`https://www.figma.com/design/Ei3vesaWdzL7bKm5sc03Jo/CAB?node-id=7056-42752` doesn't exist
anywhere in the current build. Figma's Dev Mode MCP server wasn't enabled when this was built,
so the frames couldn't be read. To unblock:

1. In the Figma desktop app: **Figma menu → Preferences → "Enable Dev Mode MCP Server"**, then
   restart the Claude desktop app, **or**
2. Export the ~20 "Auto match" frames as 2x PNGs and share them for a visual rebuild.

## Running it locally

```bash
cd e2e-prototype-tasks
python3 -m http.server 8080
```

Open `http://localhost:8080/` for the task hub, or jump straight to e.g.
`http://localhost:8080/task-1.html`.

## Structure (additions over `e2e-prototype-v2`)

```
e2e-prototype-tasks/
  index.html              # task hub (replaces the old root index.html / home-empty redirect)
  task-1.html … task-4.html
  success-task-1.html … success-task-4.html
  css/tasks.css           # styling for the hub/briefing/success pages + the floating
                           #   "Mark Task Complete" control
  screens/home-empty.html # Task 1's actual start screen (was previously also the site root)
  screens/*.html          # unchanged from e2e-prototype-v2, except the 3 files noted above
  nav-map.json            # unchanged — full screen inventory + Figma node IDs
```

## Keeping GitHub / the live site in sync

```bash
cd e2e-prototype-tasks
git add -A
git commit -m "describe the change"
git push
```

GitHub Pages rebuilds automatically within roughly a minute of the push.
