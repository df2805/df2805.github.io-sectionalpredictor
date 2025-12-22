<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Indiana Sectional Predictor</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; }

    body { font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial; margin: 20px; }
    h1 { margin: 0 0 10px; }
    .muted { color: #666; font-size: 13px; }

    .tabs { display: flex; gap: 10px; margin-top: 12px; flex-wrap: wrap; }
    .tabbtn { padding: 10px 12px; border: 1px solid #ccc; border-radius: 10px; background: #fff; cursor: pointer; }
    .tabbtn.active { border-color: #111; font-weight: 800; }

    .panel { display: none; margin-top: 12px; }
    .panel.active { display: block; }

    .row { display: flex; gap: 16px; flex-wrap: wrap; align-items: flex-start; }
    .card { border: 1px solid #ddd; border-radius: 10px; padding: 12px; background: #fff; }

    .left { flex: 1 1 520px; min-width: 280px; max-width: 100%; }
    .right { flex: 1 1 340px; min-width: 280px; max-width: 100%; }

    .btn { padding: 8px 10px; border: 1px solid #ccc; background: #fff; border-radius: 8px; cursor: pointer; }
    .btn:hover { background: #f6f6f6; }
    .btn.win { border-color: #111; font-weight: 800; }

    .controls { display: flex; gap: 10px; flex-wrap: wrap; align-items: center; }
    .pill { display: inline-block; padding: 2px 8px; border-radius: 999px; background: #f2f2f2; font-size: 12px; }
    input[type="number"], select { padding: 6px; border-radius: 8px; border: 1px solid #ccc; max-width: 100%; }
    input[type="number"] { width: 110px; }
    select { max-width: 260px; }
    .note { margin-top: 6px; font-size: 12px; color: #555; }

    .game { border: 1px solid #eee; border-radius: 10px; padding: 10px; margin: 10px 0; overflow: hidden; }
    .game-title { font-weight: 800; margin-bottom: 6px; }
    .split { display: grid; grid-template-columns: minmax(0, 1fr) 120px minmax(0, 1fr); gap: 8px; align-items: center; }
    .center { text-align: center; font-size: 12px; color: #444; }
    .bracket { display: grid; grid-template-columns: 1fr; gap: 10px; }
    .small { font-size: 12px; color: #555; }
    .hr { height: 1px; background: #eee; margin: 10px 0; }

    .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    @media (max-width: 980px) { .grid2 { grid-template-columns: 1fr; } }

    pre { white-space: pre-wrap; word-break: break-word; }

    footer { margin-top: 40px; padding-top: 12px; border-top: 1px solid #eee; font-size: 12px; color: #555; }

    @media (max-width: 720px) {
      body { margin: 12px; }
      .tabbtn { width: 100%; }
      input[type="number"], select { width: 100%; max-width: 100%; }
      select { max-width: 100%; }
      .controls > * { flex: 1 1 auto; }
      .split { grid-template-columns: 1fr; }
      .center { text-align: left; }
      .btn { width: 100%; }
    }

    @media (max-width: 420px) {
      body { margin: 10px; }
      .btn { padding: 10px 10px; }
    }
  </style>
</head>
<body>
  <h1>Sectional Predictor</h1>
  <div class="muted">Two tabs: (1) one sectional at a time with your own picks + odds. (2) full 4-sectional tournament bracket with your own picks + odds.</div>

  <!-- CONTENT UNCHANGED -->

  <footer>
    <div>
      Ratings Source: Jeff Sagarin™ Indiana High School Boys Basketball Ratings<br />
      © 2025 Jeff Sagarin. Data provided by John Harrell.
    </div>
  </footer>
</body>
</html>
