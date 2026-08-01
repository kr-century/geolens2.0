# Contributing to GEOLENS

Issues and pull requests are welcome.

## Project layout

See the "Project structure" section in `README.md` — the app is split into
single-purpose ES modules under `js/`. Please keep new features in their own
module rather than growing an existing file into a second monolith.

## Guidelines

- No backend, ever. Nothing about a user's location, camera feed, or captured
  files should leave the browser tab.
- Panel visibility (Settings, Gallery, Diagnostics) must go through
  `js/panelManager.js` with a named trigger — don't set `.hidden` on a panel
  element directly from elsewhere.
- Log meaningful failures (camera, geolocation, recording) through
  `diag.log/warn/error` in `js/diagnostics.js`, not just `console.*`, so
  issues are visible in the on-device diagnostics overlay.
- Test on at least one real Android device (Chrome for Android, Samsung
  Internet, or Firefox for Android) before submitting camera/GPS changes —
  many of the trickiest bugs here only reproduce on-device.

## Local development

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

A local static server is required — the app loads via ES modules, which
browsers block from `file://`.
