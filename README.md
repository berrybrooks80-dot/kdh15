# KO DOHYUN · Player File

A single-file progressive web app — a scouting dossier and film archive for **Ko Dohyun (고도현)**, right defence, born August 2013.

No build step. No dependencies except Google Fonts. Upload the folder and it runs.

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire app — markup, CSS, JS and the 69-video data array |
| `report.html` | Long-form written report (print-friendly) |
| `manifest.webmanifest` | Install metadata: name, icons, theme colour |
| `sw.js` | Service worker — offline cache of the app shell |
| `icons/` | Home-screen icons (192, 512, 512-maskable, 180 for iOS) |
| `.nojekyll` | Tells GitHub Pages to skip Jekyll processing |

---

## Deploy to GitHub Pages

1. Create a **public** repository named `godohyun`.
2. Upload every file above, keeping `icons/` as a folder.
   - Drag-and-drop upload **skips dotfiles** — create `.nojekyll` manually via *Add file → Create new file* if it does not appear.
3. **Settings → Pages → Source: Deploy from a branch → `main` / `(root)` → Save.**
4. The site appears at `https://<username>.github.io/godohyun/` within a minute or two.

Custom domain: enter it under *Settings → Pages → Custom domain* and point a `CNAME` record at `<username>.github.io`. HTTPS is issued automatically.

---

## Install as an app

| Platform | Steps |
|---|---|
| iPhone / iPad | Open in **Safari** → Share → *Add to Home Screen* |
| Android | Open in **Chrome** → ⋮ → *Install app* |

Launches full-screen without browser chrome. The app shell is cached, so the profile, career and record sections remain readable offline; video thumbnails and YouTube links need a connection.

---

## Installing (and why it sometimes offers nothing)

The app is a PWA — a real installable app, not a bookmark. Requirements, all of which the deployed site meets:

- served over **HTTPS from a real domain** (GitHub Pages qualifies)
- a **manifest** with 192px and 512px icons and `display: standalone`
- a **registered service worker** with an `install` handler
- opened in a **full browser** — not an in-app webview

If the install prompt never appears:

| Cause | Fix |
|---|---|
| Opened from KakaoTalk, Instagram, or an in-app viewer | Tap ⋯ → *Open in browser* (Safari / Chrome) |
| Preview or tunnel URL | Deploy to GitHub Pages and install from that address; tunnels and `file://` cannot be installed |
| iPhone | Safari only — Share → *Add to Home Screen* |
| Android | Chrome ⋮ → *Install app* |
| Install banner dismissed earlier | Clear `ko-install-dismissed` in localStorage, or use the ⤓ icon in the top bar |

The app detects all of the above by itself: a **⤓ install button** appears in the top bar, a banner appears on the home screen, and tapping either opens a step-by-step guide matched to the current browser — including a "you are in an in-app browser, copy this link" warning when that is the blocker.

`beforeinstallprompt` is captured and replayed on tap, so the native install dialog opens immediately rather than being deferred silently by the browser.

---

## Updating the archive

All content lives in one array inside `index.html`. Search for `var DATA = [` and add an entry at the top of the list:

```js
{"id":"YOUTUBE_ID","cat":"kwangwoon","t":"Kwangwoon vs Kyunghee","s":"71st National Championship","v":0,"k":"HL","y":"2026","d":"2026.04.07"}
```

| Field | Meaning |
|---|---|
| `id` | YouTube video ID (the part after `watch?v=`) |
| `cat` | `clip` · `intl` · `suwon` · `kwangwoon` · `kyunghee` |
| `t` | Title shown in the list |
| `s` | Tournament or competition |
| `v` | View count (optional, `0` hides it) |
| `k` | `HL` highlight or `FULL` full game |
| `y` | Season year |
| `d` | Date `YYYY.MM.DD` (optional, used for sorting) |

Ordering is automatic:

- **Goal clips** are renumbered chronologically as `Goal clip #01, #02, …` — never edit those titles by hand.
- **Team games** are sorted newest first within each category.

After editing, **bump the cache name in `sw.js`** (`godohyun-v2` → `v3`). Without it, installed copies keep serving the old shell.

---

## Themes

Two themes ship in the same file — no separate build.

- **Dark** (default): near-black `#07090c`, ice-blue accent `#7fd4ff`.
- **Light** (paper): `#eef1f5` ground, cobalt accent `#0b57c9`, white cards.

The sun / moon button in the top bar switches them; the choice is stored in `localStorage` under `ko-theme` and shared with `report.html`, so the app and the report always open in the same theme. To force a theme for a link or a screenshot, append `?theme=light` or `?theme=dark` to the URL.

All colour lives in CSS custom properties at the top of `index.html`. To retune a palette, edit the `:root` block (dark) and the `html[data-theme="light"]` block — nothing else in the stylesheet hard-codes a colour except the federation tag tints, which carry light-mode overrides directly beneath them.

---

## Design system

| Token | Value | Use |
|---|---|---|
| `--ink` | `#07090c` / `#eef1f5` | Page ground (dark / light) |
| `--ice` | `#7fd4ff` / `#0b57c9` | Primary accent, active states |
| `--red` | `#ff2d40` | Current club, alerts |
| `--gold` | `#e8c07a` | Medals |
| Display | Archivo 800–900 | Headlines, numerals |
| Body | Archivo 400–500 | Copy |
| Mono | JetBrains Mono | Labels, dates, metadata |

Square corners, hairline borders, rink-diagram motif in the background layer.
