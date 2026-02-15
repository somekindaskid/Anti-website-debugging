# POC site

Proof-of-concept pages that react when you leave the page (switch tab or click outside the window). One version **redirects** to a disclaimer page; the other **hides** the content behind a black overlay and restores it when you return. All client-side; no backend.

---

## What’s going on

When the user isn’t looking at the page (different tab or another window focused), the POC can either:

1. **Redirect** – Navigate away to a disclaimer page (`poc.html`).
2. **Overlay** – Show a full-screen “PRIVACY ACTIVATED” screen and hide the main content, then restore when they come back.

Use case: demos, kiosks, or shared screens where you want to clear or leave the page when attention is elsewhere.

---

## Project structure

```
late night project/
├── README.md                 ← this file
├── privacy crash.html        ← redirects to poc.html when you leave the page
├── privacy pop up.html       ← black overlay + “PRIVACY ACTIVATED” when you leave; restores on return
└── poc.html                  ← disclaimer page you land on after privacy crash redirect
```

---

## File-by-file

### `privacy crash.html`
`https://somekindaskid.github.io/Anti-website-debugging/privacy privacy crash.html`
**Purpose:** As soon as you leave the page (switch tab or click outside the window), it **navigates** to `poc.html`. No overlay, no going back—the tab leaves the original page.

**How it works:**
- Listens for **`visibilitychange`** (tab hidden = you switched to another tab).
- Listens for **`blur`** (window loses focus = you clicked another app or window).
- On either event it sets `window.location.href = 'poc.html'`, so the browser loads the disclaimer page.

**Use case:** Demo “bail out” when the user isn’t looking (e.g. shared screen or kiosk). Includes a CSP meta tag so inline scripts run in stricter environments.

---

### `privacy pop up.html`
`https://somekindaskid.github.io/Anti-website-debugging/privacy pop up.html`
**Purpose:** Softer privacy mode. When you leave the page it **does not** redirect. It hides the main content, shows a full-screen black overlay with “PRIVACY ACTIVATED”, and sets the tab title to “TABBED OUT”. When you return (same tab or window focus), it restores the content and title.

**How it works:**
- **`visibilitychange`**: tab hidden → `showPrivacy()` (overlay on, main content removed from DOM, title set). Tab visible again → `restore()` (content re-inserted, overlay off, title restored).
- **`blur`**: window loses focus → `showPrivacy()`.
- **`focus`**: window gains focus → `restore()`.
- `showPrivacy()` removes the `#main` div from the DOM so only the overlay remains; `restore()` inserts it back.

**Use case:** Hide sensitive content without leaving the page; good for demos or shared screens.

---

### `poc.html`

**Purpose:** Landing page shown after **privacy crash** redirects. Explains that this is a proof of concept.

**Content:** Centred text: *“This can crash your client, or wipe the cookies for security reasons. This is a proof of concept.”* Same dark theme as the other POC pages.

**Use case:** Tells the user why they were sent here (e.g. “you tabbed out or clicked away, so the previous page navigated away for privacy/security”). You can change the copy or add real “wipe cookies” behaviour later if needed.

---

## How to run

Open the HTML files in a browser (double-click or “Open with” browser). For `privacy crash.html` → `poc.html` to work, keep both files in the same folder (or change the path in the script).

---

## Summary

| File | What it does |
|------|------------------|
| **privacy crash.html** | On tab switch or window blur → redirect to `poc.html`. |
| **privacy pop up.html** | On tab switch or window blur → black overlay “PRIVACY ACTIVATED”, hide content; on return → restore. |
| **poc.html** | Disclaimer: “can crash client / wipe cookies, proof of concept.” |

All of this is client-side only; no server or build step.
