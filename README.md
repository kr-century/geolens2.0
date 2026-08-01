# GEOLENS — GPS Camera

A browser-based GPS camera. GEOLENS reads your device's most accurate available location **continuously** and stamps it — live — onto photos and video. No app install, no account, no backend server: everything runs and stays inside your browser tab.

![No backend](https://img.shields.io/badge/backend-none-4cff9e)
![Vanilla JS](https://img.shields.io/badge/stack-HTML%2FCSS%2FJS-informational)
![License: MIT](https://img.shields.io/badge/license-MIT-blue)

---

## Features

- **Continuous high-accuracy GPS lock** via the browser's Geolocation API (`watchPosition` + `enableHighAccuracy`), with a live accuracy-radius indicator on the viewfinder
- **Photo capture** with a burned-in geotag — coordinates, altitude, accuracy radius, and timestamp
- **Video recording** with a *continuously live-updating* geotag overlay (not a single stamp applied once — the coordinates keep refreshing frame by frame)
- **Front/back camera switching**, with optional torch/flashlight control on supporting devices
- **Settings**: metric/imperial units, geotag on/off, overlay style, JPEG quality, rule-of-thirds grid, auto-open gallery, shutter/record sounds — all saved locally on your device
- **One-tap coordinate copy** and direct hand-off to Google Maps
- **Share or download** captured files directly from the in-app gallery — nothing is ever uploaded anywhere
- **On-device diagnostics overlay** (tap the bug icon, top right) that shows the raw error names/messages from camera and GPS failures — useful when you can't attach a desktop devtools console to a phone
- **In-app-browser warning** — detects WhatsApp/Instagram/Facebook/generic WebView shells, where camera access is commonly blocked, and tells you to open the link in Chrome or Firefox
- Fully responsive, mobile-first, works down to small phone screens

## Live demo

Once deployed (see [Deployment](#deployment-github-pages) below), your app will be reachable at:

```
https://<your-username>.github.io/<repo-name>/
```

## Why it needs HTTPS

Camera access (`getUserMedia`) and precise location (`geolocation.watchPosition`) are both blocked by every modern browser outside a **secure context** — that means `https://` or `http://localhost`. Opening `index.html` directly as a local file (`file://`) will **not** work; GitHub Pages serves everything over HTTPS automatically, which is the easiest way to run this for real.

## Running locally

Camera (`getUserMedia`) and precise location (`geolocation.watchPosition`) require a secure context, so you still need a local static server rather than opening `index.html` directly as a `file://` path:

```bash
# Option 1: Node
npx serve .

# Option 2: Python
python3 -m http.server 8000
```

Then open `http://localhost:PORT` in your browser — not the file directly.

## Deployment (GitHub Pages)

Since this is a single `index.html` file, you don't need any GitHub Actions workflow — GitHub Pages' built-in **"Deploy from a branch"** option is enough.

1. Push this repo to GitHub (or upload `index.html` directly via the GitHub web UI).
2. Go to **Settings → Pages** in your repository.
3. Under **Build and deployment → Source**, select **Deploy from a branch**.
4. Under **Branch**, select `main` and folder `/ (root)` → **Save**.
5. Wait ~1 minute, then your site will be live at `https://<your-username>.github.io/<repo-name>/`.

## Project structure

Everything — HTML, CSS, and JavaScript — is combined into a **single `index.html` file**. This is the simplest possible thing to upload to GitHub: just drop this one file (plus, optionally, `README.md`/`LICENSE`/`CONTRIBUTING.md`) into your repo's root.

Internally, the JavaScript is still organized as clearly separated, commented sections (diagnostics, panel manager, WebView detection, settings, camera, geolocation, overlay, capture, recorder, gallery, boot) — it's just packaged into one file for easy deployment rather than split across multiple files.

## Android compatibility notes

Two Android-specific failure modes are handled explicitly:

- **Camera opens site-level but is still blocked** — Android requires the browser *app itself* to have OS-level camera permission (**Settings → Apps → your browser → Permissions → Camera**), separately from the in-page prompt. If `getUserMedia()` throws `NotAllowedError` after a permission that was already granted at the site level, GEOLENS shows OS-level instructions instead of a generic "permission denied" message.
- **In-app browsers (WhatsApp, Instagram, Facebook, generic WebView)** — `getUserMedia` is unreliable or blocked in many of these. GEOLENS detects common in-app-browser signatures via `navigator.userAgent` and shows a banner telling you to open the link in Chrome or Firefox directly.
- **Lost user-activation after a slow permission prompt** — on some Android devices, `video.play()` can be blocked by autoplay policy if too much time passes between the user's tap and the call. GEOLENS treats this as recoverable (the camera stream itself is fine) and shows a one-tap "resume preview" gate rather than reporting a camera failure.

If something still doesn't work, tap the diagnostics icon (top right, looks like a bug/terminal) to see the exact error names and messages GEOLENS is receiving from the browser — this is designed to be readable on a phone without any desktop tooling.

## Tech stack

Plain HTML, CSS, and JavaScript — no build step, no framework, no dependencies to install, split across ES modules. Uses:

- [`getUserMedia`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) — camera + microphone access
- [`Geolocation API`](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API) — continuous high-accuracy location
- `Canvas` compositing — burns the live geotag into photos and, frame-by-frame, into recorded video
- [`MediaRecorder`](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) — records the composited canvas stream to `.webm`
- [`Permissions API`](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API) — queried before requesting the camera, to distinguish site-level from OS-level permission states
- `localStorage` — remembers your settings between visits (nothing else is stored, and nothing leaves your device)

## Browser support

Works in current versions of Chrome, Edge, Firefox, and Safari on both desktop and mobile. A few notes:

- Torch/flashlight control depends on the device's camera capabilities and is hidden automatically if unsupported.
- Video recording requires `MediaRecorder` + `canvas.captureStream()` support — available in all modern browsers, but test on Safari specifically if you're targeting iOS, since support landed there more recently.
- Reported GPS accuracy depends entirely on the device's own hardware (GNSS chip, Wi-Fi/cell fusion) — this is the best a browser can surface, not survey-grade positioning.
- Tested explicitly on Chrome for Android, Samsung Internet, and Firefox for Android; behavior inside in-app browsers (WhatsApp/Instagram/Facebook WebViews) is flagged with an on-screen warning rather than silently failing.

## Privacy

Nothing about your location, camera feed, or captured files is sent anywhere. There is no backend, no analytics, and no third-party API calls. Settings are saved only in your own browser's `localStorage`. Captured photos/videos live only in the current tab's memory until you explicitly download or share them.

## Contributing

Issues and pull requests are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Released under the [MIT License](LICENSE).

---

This site is created by Keshav Kumar
यह साइट केशव कुमार द्वारा बनाई गई है
