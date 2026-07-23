# ORIGIN

Configurable personal tracking & planning system built on the **Action Trace Principle**.
Single-file web application — no build step, no server, no dependencies at runtime.
Data never leaves the device: config in `localStorage`, timeline in `IndexedDB`.

Part of the ORIGIN personal evolution system (data collection front-end, v1.x roadmap: data-first, analytics-later).

## Structure

```
origin-app/
├── www/
│   ├── index.html          # the entire app (HTML + CSS + JS)
│   ├── sw.js               # service worker (offline shell)
│   ├── manifest.webmanifest
│   └── icons/              # 180 / 192 / 512 / 1024 px
├── capacitor.config.json   # native iOS shell config
├── package.json
└── README.md
```

## Run it

Any of the following:

1. **Directly** — open `www/index.html` in a browser (service worker disabled on `file://`, everything else works).
2. **Local server** — `npm run serve` → http://localhost:8080 (full PWA behavior).
3. **GitHub Pages** — publish the `www/` folder; the app becomes installable from Safari/Chrome.

## Install on iPhone (PWA — fastest path)

1. Host `www/` on any HTTPS URL (GitHub Pages works).
2. Open it in **Safari** on the iPhone.
3. Share → **Add to Home Screen**.

You get a full-screen, offline-capable app with the ORIGIN icon. IndexedDB and localStorage persist per-site.

## Native iOS build (Capacitor)

Requirements: macOS, Xcode 15+, Node 18+.

```bash
npm install
npm run ios:add     # generates ios/ project (first time only)
npm run ios:sync    # copies www/ into the native shell
npm run ios:open    # opens Xcode — set your signing team, then Run
```

The web app runs unchanged inside WKWebView. Notes:

- **Voice notes** use the Web Speech API. Inside WKWebView support is limited; on-device `SFSpeechRecognizer` via a Capacitor plugin is the planned native upgrade. The text field always works manually.
- **Data location**: WKWebView storage is sandboxed per app — use Exchange → Full Export for backups and device transfer.
- App ID / name are set in `capacitor.config.json` (`com.origin.tracker`).

## Data schemas

Three self-contained JSON export formats (each carries `schema` + `schemaVersion`):

| schema | content | purpose |
|---|---|---|
| `origin-config` | Layer A: objects, aliases, canvases, lists, tags, settings | device transfer |
| `origin-timeline` | Layer B: records for a period, `previousExportAt` for wave analytics | weekly ORIGIN engine analysis |
| `origin-full` | config + full timeline | complete backup |

Records reference objects by immutable UUID (`objectId` / `aliasId`), never by name. Every edit is logged (`edited`, `editedAt`). Importers must read all previous schema versions.

## Version

**1.2.0** — see in-app Settings for schema version. UI languages: EN (base), RU, IT, UA.
