# Slow Rails

A contemplative tile-laying game for the [GameDev.tv Jam 2026](https://itch.io/jam/gamedevtv-jam-2026) (theme: **Connections**).

Place tiles to grow a landscape with a rail line winding through it. Your train rolls one tile per day, visiting points of interest along the way. Scoring rewards the *quality* of the journey, not the speed of it.

> **Itch page (live during jam voting):** _link added at submission_

## Build & run

This project pins Godot via [gdvm](https://github.com/adalinesimonian/gdvm):

```bash
# Uses the version in .gdvmrc (4.6.3-stable)
gdvm use
godot
```

To export a web build for itch:

```bash
godot --headless --export-release "Web" export/web/index.html
cd export/web/ && python3 -m http.server 8000  # smoke-test locally
```

See [`planning/slow-rails-gdd.md`](planning/slow-rails-gdd.md) for the design doc.

## Made by

[Lucky Hit Games](https://luckyhitgames.com). Full attributions in [`CREDITS.md`](CREDITS.md).
