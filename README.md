<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Indiana Sectional Predictor</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; }

    body {
      font-family: "Inter", system-ui, -apple-system, Segoe UI, Roboto, Arial;
      margin: 0;
      background: #eef2f7;
      color: #0f172a;
      line-height: 1.5;
      min-height: 100vh;
    }
    h1 { margin: 0 0 6px; font-size: 30px; letter-spacing: -0.02em; }
    .muted { color: #64748b; font-size: 13px; }

    .tabs {
      display: inline-flex;
      gap: 6px;
      margin-top: 12px;
      flex-wrap: wrap;
      padding: 6px;
      border-radius: 999px;
      background: #e2e8f0;
      border: 1px solid #d3dae6;
    }
    .tabbtn {
      padding: 9px 16px;
      border: none;
      border-radius: 999px;
      background: transparent;
      cursor: pointer;
      font-weight: 600;
      color: #0f172a;
      transition: all 0.2s ease;
    }
    .tabbtn:hover { background: rgba(255, 255, 255, 0.6); }
    .tabbtn.active {
      background: #fff;
      color: #1d4ed8;
      box-shadow: 0 8px 16px rgba(15, 23, 42, 0.12);
    }

    .panel { display: none; margin-top: 12px; }
    .panel.active { display: block; }

    .row { display: flex; gap: 16px; flex-wrap: wrap; align-items: flex-start; }
    .card {
      border: 1px solid #e2e8f0;
      border-radius: 16px;
      padding: 16px;
      background: #fff;
      box-shadow: 0 10px 22px rgba(15, 23, 42, 0.06);
    }

    .left { flex: 1 1 520px; min-width: 280px; max-width: 100%; }
    .right { flex: 1 1 340px; min-width: 280px; max-width: 100%; }

    .btn {
      padding: 9px 12px;
      border: 1px solid #cbd5f5;
      background: #fff;
      border-radius: 10px;
      cursor: pointer;
      font-weight: 600;
      color: #0f172a;
      transition: all 0.2s ease;
    }
    .btn:hover { background: #eef2ff; }
    .btn:focus-visible {
      outline: 2px solid #93c5fd;
      outline-offset: 2px;
    }
    .btn:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }
    .btn.primary {
      background: linear-gradient(135deg, #2563eb, #1d4ed8);
      border-color: #1d4ed8;
      color: #fff;
      box-shadow: 0 10px 18px rgba(37, 99, 235, 0.25);
    }
    .btn.primary:hover { filter: brightness(1.05); }
    .btn.win {
      border-color: #0f172a;
      box-shadow: inset 0 0 0 2px #0f172a;
      font-weight: 800;
    }

    .controls { display: flex; gap: 10px; flex-wrap: wrap; align-items: center; }
    .pill {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      padding: 4px 10px;
      border-radius: 999px;
      background: #f1f5f9;
      font-size: 12px;
      font-weight: 600;
      color: #475569;
    }
    input[type="number"], select {
      padding: 8px 10px;
      border-radius: 10px;
      border: 1px solid #cbd5f5;
      background: #fff;
      max-width: 100%;
    }
    input[type="number"]:focus,
    select:focus {
      outline: 2px solid #bfdbfe;
      border-color: #93c5fd;
    }
    input[type="number"] { width: 110px; }
    select { max-width: 260px; }
    .note { margin-top: 6px; font-size: 12px; color: #64748b; }

    .game {
      border: 1px solid #e2e8f0;
      border-radius: 14px;
      padding: 12px;
      margin: 10px 0;
      overflow: hidden;
      background: #f8fafc;
    }
    .game-title { font-weight: 700; margin-bottom: 6px; color: #0f172a; }
    .split { display: grid; grid-template-columns: minmax(0, 1fr) 120px minmax(0, 1fr); gap: 8px; align-items: center; }
    .center { text-align: center; font-size: 12px; color: #444; }
    .bracket { display: grid; grid-template-columns: 1fr; gap: 10px; }
    .small { font-size: 12px; color: #64748b; }
    .hr { height: 1px; background: #e2e8f0; margin: 10px 0; }
    .page { max-width: 1200px; margin: 32px auto; padding: 0 20px 48px; }
    .header {
      display: flex;
      flex-direction: column;
      gap: 4px;
      padding: 20px 24px;
      border-radius: 18px;
      background: #fff;
      border: 1px solid #e2e8f0;
      box-shadow: 0 12px 22px rgba(15, 23, 42, 0.08);
    }
    .header .muted { font-size: 14px; }

    .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    @media (max-width: 980px) { .grid2 { grid-template-columns: 1fr; } }

    pre { white-space: pre-wrap; word-break: break-word; }

    @media (max-width: 720px) {
      .page { margin: 16px auto; }
      .tabs { width: 100%; justify-content: center; }
      .tabbtn { width: 100%; }
      input[type="number"], select { width: 100%; max-width: 100%; }
      select { max-width: 100%; }
      .controls > * { flex: 1 1 auto; }
      .split { grid-template-columns: 1fr; }
      .center { text-align: left; }
      .btn { width: 100%; }
    }

    @media (max-width: 420px) {
      .page { margin: 12px auto; }
      .btn { padding: 10px 10px; }
    }
  </style>
</head>
<body>
  <div class="page">
    <div class="header">
      <h1>Sectional Predictor</h1>
      <div class="muted">Two tabs: (1) one sectional at a time with your own picks + odds. (2) full 4-sectional tournament bracket with your own picks + odds.</div>
    </div>

    <div class="card" style="margin-top:16px;">
      <div class="controls">
        <span class="pill">Home adv</span>
        <input type="number" id="homeAdv" value="1.8" step="0.5" />
        <span class="pill">Sims</span>
        <input type="number" id="simCount" value="20000" min="1000" step="1000" />
      </div>
      <div class="note">Home advantage applies only when the host team is in the game. In tournament tab, sectional hosts are fixed.</div>
    </div>

  <div class="tabs">
    <button class="tabbtn active" id="tabSectional">Single Sectional</button>
    <button class="tabbtn" id="tabTournament">Full Tournament</button>
  </div>

  <div class="panel active" id="panelSectional">
    <div class="card">
      <div class="controls">
        <span class="pill">Sectional</span>
        <select id="sectionalSelect"></select>

        <span class="pill">Host</span>
        <select id="hostSelect"></select>

        <button class="btn" id="genDraw">Generate Draw</button>
        <button class="btn" id="resetPicks">Reset Picks</button>
        <button class="btn primary" id="runSims">Run Odds</button>
      </div>
      <div class="note" id="formatNote"></div>
    </div>

    <div class="row" style="margin-top:12px;">
      <div class="card left">
        <h3 style="margin:0 0 10px;">Bracket</h3>
        <div class="small" id="drawInfo">Pick a sectional and click Generate Draw.</div>
        <div class="bracket" id="bracket"></div>
      </div>

      <div class="card right">
        <h3 style="margin:0 0 8px;">Odds</h3>
        <div class="muted">Odds reflect your locked picks. Unpicked games get simulated from ratings.</div>
        <div style="margin-top:8px;" id="simOutput"><span class="muted">Run odds to see results.</span></div>
      </div>
    </div>
  </div>

  <div class="panel" id="panelTournament">
    <div class="card">
      <div class="controls">
        <button class="btn" id="tourGenAll">Generate All Sectional Draws</button>
        <button class="btn" id="tourDrawRegionals">Draw Regionals</button>
        <button class="btn" id="tourReset">Reset Tournament Picks</button>
        <button class="btn primary" id="tourRunOdds">Run Tournament Odds</button>
      </div>
      <div class="note" id="tourNote">Step 1: Generate sectional draws. Step 2: Draw regionals. Step 3: pick winners anywhere and run odds.</div>
    </div>

    <div class="row" style="margin-top:12px;">
      <div class="card left">
        <h3 style="margin:0 0 10px;">Big Bracket</h3>
        <div class="small" id="tourDrawInfo">Generate sectional draws to start.</div>
        <div class="grid2" id="tourSectionalGrid"></div>
        <div class="hr"></div>
        <div id="tourRegionalBlock"></div>
      </div>

      <div class="card right">
        <h3 style="margin:0 0 8px;">Tournament Odds</h3>
        <div class="muted">Odds reflect your locked picks across all sectionals + regionals + final.</div>
        <div style="margin-top:8px;" id="tourOdds"><span class="muted">Run tournament odds to see results.</span></div>
      </div>
    </div>
  </div>
    <footer>
      <div style="margin-top:40px;padding-top:12px;border-top:1px solid #e2e8f0;font-size:12px;color:#64748b;">
        Ratings Source: Jeff Sagarin™ Indiana High School Boys Basketball Ratings<br />
        © 2025 Jeff Sagarin. Data provided by John Harrell.
      </div>
    </footer>
  </div>

<script>
  const SECTIONALS = {
    "Elkhart": {
      hostDefault: "Elkhart",
      teams: [
        { name: "Northridge", rating: 87.13 },
        { name: "Goshen", rating: 82.88 },
        { name: "Elkhart", rating: 79.53 },
        { name: "Warsaw", rating: 76.69 },
        { name: "Concord", rating: 64.94 }
      ]
    },
    "Crown Point": {
      hostDefault: "Crown Point",
      teams: [
        { name: "Crown Point", rating: 95.15 },
        { name: "Lake Central", rating: 83.04 },
        { name: "Munster", rating: 60.65 },
        { name: "Hammond Morton", rating: 57.41 },
        { name: "Hammond Central", rating: 59.02 }
      ]
    },
    "Chesterton": {
      hostDefault: "Chesterton",
      teams: [
        { name: "Chesterton", rating: 88.75 },
        { name: "Portage", rating: 81.09 },
        { name: "Valparaiso", rating: 73.05 },
        { name: "Merrillville", rating: 69.61 },
        { name: "Hobart", rating: 52.78 }
      ]
    },
    "Mishawaka": {
      hostDefault: "Mishawaka",
      teams: [
        { name: "Penn", rating: 90.72 },
        { name: "South Bend Riley", rating: 87.34 },
        { name: "South Bend St. Joseph", rating: 82.99 },
        { name: "Michigan City", rating: 80.24 },
        { name: "South Bend Adams", rating: 73.56 },
        { name: "LaPorte", rating: 70.02 },
        { name: "Mishawaka", rating: 62.33 }
      ]
    },
    "Huntington North": {
      hostDefault: "Huntington North",
      teams: [
        { name: "Huntington North", rating: 87.17 },
        { name: "Homestead", rating: 82.89 },
        { name: "Fort Wayne Wayne", rating: 80.84 },
        { name: "Fort Wayne South", rating: 78.95 }
      ]
    },
    "Lafayette Jeff": {
      hostDefault: "Lafayette Jeff",
      teams: [
        { name: "Harrison (West Lafayette)", rating: 81.45 },
        { name: "Kokomo", rating: 78.84 },
        { name: "Lafayette Jeff", rating: 73.57 },
        { name: "McCutcheon", rating: 57.59 }
      ]
    },
    "Noblesville": {
      hostDefault: "Noblesville",
      teams: [
        { name: "Fishers", rating: 98.34 },
        { name: "Carmel", rating: 96.34 },
        { name: "Noblesville", rating: 92.35 },
        { name: "Zionsville", rating: 89.15 },
        { name: "Westfield", rating: 87.67 },
        { name: "Hamilton Southeastern", rating: 85.12 }
      ]
    },
    "Greenfield-Central": {
      hostDefault: "Greenfield-Central",
      teams: [
        { name: "Mount Vernon (Fortville)", rating: 95.16 },
        { name: "Pendleton Heights", rating: 83.68 },
        { name: "Anderson", rating: 81.88 },
        { name: "Muncie Central", rating: 74.08 },
        { name: "Greenfield-Central", rating: 73.22 },
        { name: "Richmond", rating: 65.61 }
      ]
    },
    "Indianapolis Arsenal Tech": {
      hostDefault: "Indianapolis Arsenal Tech",
      teams: [
        { name: "Lawrence North", rating: 93.85 },
        { name: "Lawrence Central", rating: 80.41 },
        { name: "North Central", rating: 80.36 },
        { name: "Arsenal Tech", rating: 72.93 },
        { name: "Warren Central", rating: 71.80 }
      ]
    },
    "Plainfield": {
      hostDefault: "Plainfield",
      teams: [
        { name: "Plainfield", rating: 98.84 },
        { name: "Pike", rating: 92.97 },
        { name: "Ben Davis", rating: 88.17 },
        { name: "Brownsburg", rating: 83.02 },
        { name: "Avon", rating: 80.11 }
      ]
    },
    "Mooresville": {
      hostDefault: "Mooresville",
      teams: [
        { name: "Decatur Central", rating: 86.68 },
        { name: "Southport", rating: 82.99 },
        { name: "Center Grove", rating: 80.76 },
        { name: "Mooresville", rating: 75.10 },
        { name: "Franklin Central", rating: 73.73 },
        { name: "Perry Meridian", rating: 71.53 }
      ]
    },
    "Martinsville": {
      hostDefault: "Martinsville",
      teams: [
        { name: "Bloomington North", rating: 87.99 },
        { name: "Terre Haute North", rating: 84.98 },
        { name: "Bloomington South", rating: 80.68 },
        { name: "Martinsville", rating: 72.13 },
        { name: "Terre Haute South", rating: 63.90 }
      ]
    },
    "Columbus North": {
      hostDefault: "Columbus North",
      teams: [
        { name: "Franklin", rating: 80.48 },
        { name: "Columbus East", rating: 79.04 },
        { name: "Columbus North", rating: 78.94 },
        { name: "Whiteland", rating: 75.77 },
        { name: "East Central", rating: 64.38 }
      ]
    },
    "Seymour": {
      hostDefault: "Seymour",
      teams: [
        { name: "New Albany", rating: 90.05 },
        { name: "Jeffersonville", rating: 80.93 },
        { name: "Floyd Central", rating: 80.30 },
        { name: "Scottsburg", rating: 72.82 },
        { name: "Seymour", rating: 71.66 },
        { name: "Bedford North Lawrence", rating: 67.85 }
      ]
    },
    "Evansville North": {
      hostDefault: "Evansville North",
      teams: [
        { name: "Evansville North", rating: 85.42 },
        { name: "Evansville Reitz", rating: 72.91 },
        { name: "Evansville Harrison", rating: 69.59 },
        { name: "Castle", rating: 64.48 }
      ]
    }
  };

  let currentSectionalKey = "Elkhart";
  let teamList = [];
  let rating = {};
  let hostTeam = "";

  let slots = null;
  let games = [];
  let picks = {};

  const tour = {
    sectionals: {},
    pairing: null,
    regionalGames: [],
    regionalPicks: {}
  };

  function $(id) { return document.getElementById(id); }

  function shuffle(arr) {
    const a = arr.slice();
    for (let i = a.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      const tmp = a[i];
      a[i] = a[j];
      a[j] = tmp;
    }
    return a;
  }

  function linesToText(lines) {
    return lines.join("\n");
  }

  function homeAdvPoints() {
    const v = Number($("homeAdv").value);
    return Number.isFinite(v) ? v : 0;
  }

  function simCount() {
    const v = Number($("simCount").value);
    return Math.max(1000, Number.isFinite(v) ? v : 20000);
  }

  function buildRatingAll() {
    const all = {};
    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const k = keys[i];
      const teams = SECTIONALS[k].teams;
      for (let j = 0; j < teams.length; j++) {
        all[teams[j].name] = teams[j].rating;
      }
    }
    return all;
  }

  function winProbFrom(ratingMap, host, adv, teamA, teamB) {
    const k = 0.25;
    let diff = ratingMap[teamA] - ratingMap[teamB];
    if (host && teamA === host && teamB !== host) diff += adv;
    if (host && teamB === host && teamA !== host) diff -= adv;
    return 1 / (1 + Math.pow(10, (-k * diff / 10)));
  }

  function clearPicksObj(obj) {
    const keys = Object.keys(obj);
    for (let i = 0; i < keys.length; i++) delete obj[keys[i]];
  }

  function isTeamRef(x) {
    return typeof x === "object" && x && typeof x.win === "string";
  }

  function resolveTeam(x, winners) {
    if (isTeamRef(x)) {
      if (Object.prototype.hasOwnProperty.call(winners, x.win)) return winners[x.win];
      return null;
    }
    return x;
  }

  function buildGamesFromSlotsLocal(slotsLocal) {
    const n = slotsLocal.length;

    if (n === 4) {
      const T1 = slotsLocal[0], T2 = slotsLocal[1], T3 = slotsLocal[2], T4 = slotsLocal[3];
      return [
        { id: "G1", title: "Semifinal 1", a: T1, b: T2 },
        { id: "G2", title: "Semifinal 2", a: T3, b: T4 },
        { id: "F", title: "Final", a: { win: "G1" }, b: { win: "G2" } }
      ];
    }

    if (n === 5) {
      const A = slotsLocal[0], B = slotsLocal[1], C = slotsLocal[2], D = slotsLocal[3], E = slotsLocal[4];
      return [
        { id: "P", title: "Play-in", a: A, b: B },
        { id: "S1", title: "Semi 1", a: { win: "P" }, b: C },
        { id: "S2", title: "Semi 2", a: D, b: E },
        { id: "F", title: "Final", a: { win: "S1" }, b: { win: "S2" } }
      ];
    }

    if (n === 6) {
      const A = slotsLocal[0], B = slotsLocal[1], C = slotsLocal[2], D = slotsLocal[3], E = slotsLocal[4], F = slotsLocal[5];
      return [
        { id: "Q1", title: "Quarterfinal 1", a: A, b: B },
        { id: "Q2", title: "Quarterfinal 2", a: C, b: D },
        { id: "S1", title: "Semifinal 1", a: { win: "Q1" }, b: E },
        { id: "S2", title: "Semifinal 2", a: { win: "Q2" }, b: F },
        { id: "F", title: "Final", a: { win: "S1" }, b: { win: "S2" } }
      ];
    }

    if (n === 7) {
      const T1 = slotsLocal[0], T2 = slotsLocal[1], T3 = slotsLocal[2], T4 = slotsLocal[3], T5 = slotsLocal[4], T6 = slotsLocal[5], T7 = slotsLocal[6];
      return [
        { id: "G1", title: "Game 1", a: T1, b: T2 },
        { id: "G2", title: "Game 2", a: T3, b: T4 },
        { id: "G3", title: "Game 3", a: T5, b: T6 },
        { id: "G4", title: "Game 4", a: { win: "G1" }, b: T7 },
        { id: "G5", title: "Game 5", a: { win: "G2" }, b: { win: "G3" } },
        { id: "G6", title: "Championship", a: { win: "G4" }, b: { win: "G5" } }
      ];
    }

    return [];
  }

  function computeWinnersFromPicksLocal(gamesLocal, picksLocal) {
    const winners = {};
    for (let i = 0; i < gamesLocal.length; i++) {
      const g = gamesLocal[i];
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);
      if (!a || !b) continue;
      const p = picksLocal[g.id];
      if (p === a || p === b) winners[g.id] = p;
    }
    return winners;
  }

  function validateDownstreamLocal(gamesLocal, picksLocal) {
    const winners = {};
    for (let i = 0; i < gamesLocal.length; i++) {
      const g = gamesLocal[i];
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);

      if (!a || !b) {
        if (picksLocal[g.id]) delete picksLocal[g.id];
        continue;
      }

      if (picksLocal[g.id] && picksLocal[g.id] !== a && picksLocal[g.id] !== b) {
        delete picksLocal[g.id];
        continue;
      }

      if (picksLocal[g.id]) winners[g.id] = picksLocal[g.id];
    }
  }

  function renderGameCardWithContext(opts) {
    const g = opts.g;
    const winners = opts.winners;
    const picksLocal = opts.picksLocal;
    const onPick = opts.onPick;
    const ratingMap = opts.ratingMap;
    const host = opts.host;
    const adv = opts.adv;

    const div = document.createElement("div");
    div.className = "game";

    const title = document.createElement("div");
    title.className = "game-title";
    title.textContent = g.title;
    div.appendChild(title);

    const split = document.createElement("div");
    split.className = "split";

    const a = resolveTeam(g.a, winners);
    const b = resolveTeam(g.b, winners);

    const mid = document.createElement("div");
    mid.className = "center";

    if (!a || !b) {
      mid.textContent = "Waiting";

      const btn1 = document.createElement("button");
      btn1.className = "btn";
      btn1.textContent = a ? ("Pick " + a) : "Pick Winner";
      btn1.disabled = true;

      const btn2 = document.createElement("button");
      btn2.className = "btn";
      btn2.textContent = b ? ("Pick " + b) : "Pick Winner";
      btn2.disabled = true;

      split.appendChild(btn1);
      split.appendChild(mid);
      split.appendChild(btn2);
      div.appendChild(split);

      const info = document.createElement("div");
      info.className = "muted";
      info.style.marginTop = "8px";
      info.textContent = "Waiting on earlier winners.";
      div.appendChild(info);
      return div;
    }

    mid.textContent = picksLocal[g.id] ? "Locked" : "Pick winner";

    const btn1 = document.createElement("button");
    btn1.className = "btn" + (picksLocal[g.id] === a ? " win" : "");
    btn1.textContent = "Pick " + a;

    const btn2 = document.createElement("button");
    btn2.className = "btn" + (picksLocal[g.id] === b ? " win" : "");
    btn2.textContent = "Pick " + b;

    btn1.onclick = function () { onPick(g.id, a); };
    btn2.onclick = function () { onPick(g.id, b); };

    split.appendChild(btn1);
    split.appendChild(mid);
    split.appendChild(btn2);
    div.appendChild(split);

    const pA = winProbFrom(ratingMap, host, adv, a, b);
    const pB = 1 - pA;

    let hostNote = "";
    if (host && (a === host || b === host) && adv !== 0) {
      hostNote = " (host " + host + ", +" + adv.toFixed(1) + " pts)";
    }

    const info = document.createElement("div");
    info.className = "muted";
    info.style.marginTop = "8px";
    info.textContent = a + ": " + (pA * 100).toFixed(1) + "%  |  " + b + ": " + (pB * 100).toFixed(1) + "%" + hostNote;
    div.appendChild(info);

    return div;
  }

  function sectionalFormatLine(slotsLocal) {
    const n = slotsLocal.length;
    if (n === 4) {
      return "Draw: Semifinal 1 = " + slotsLocal[0] + " vs " + slotsLocal[1] + ", Semifinal 2 = " + slotsLocal[2] + " vs " + slotsLocal[3] + ". Winners advance to the final.";
    }
    if (n === 5) {
      return "Draw: Play-in=" + slotsLocal[0] + " vs " + slotsLocal[1] + ". Semis: Winner(Play-in) vs " + slotsLocal[2] + ", and " + slotsLocal[3] + " vs " + slotsLocal[4] + ".";
    }
    if (n === 6) {
      return "Draw: Q1=" + slotsLocal[0] + " vs " + slotsLocal[1] + ", Q2=" + slotsLocal[2] + " vs " + slotsLocal[3] + ". " + slotsLocal[4] + " and " + slotsLocal[5] + " receive byes to the semifinals.";
    }
    if (n === 7) {
      return "Draw: G1=" + slotsLocal[0] + " vs " + slotsLocal[1] + ", G2=" + slotsLocal[2] + " vs " + slotsLocal[3] + ", G3=" + slotsLocal[4] + " vs " + slotsLocal[5] + ". " + slotsLocal[6] + " has the bye into Game 4.";
    }
    return "This sectional has " + n + " teams. Only 4, 5, 6, and 7 teams are supported.";
  }

  function loadSectional(key) {
    currentSectionalKey = key;
    const data = SECTIONALS[key];

    rating = {};
    teamList = [];
    for (let i = 0; i < data.teams.length; i++) {
      rating[data.teams[i].name] = data.teams[i].rating;
      teamList.push(data.teams[i].name);
    }

    const hostSelect = $("hostSelect");
    hostSelect.innerHTML = "";

    const optNeutral = document.createElement("option");
    optNeutral.value = "";
    optNeutral.textContent = "None (neutral)";
    hostSelect.appendChild(optNeutral);

    for (let i = 0; i < teamList.length; i++) {
      const nm = teamList[i];
      const opt = document.createElement("option");
      opt.value = nm;
      opt.textContent = nm;
      hostSelect.appendChild(opt);
    }

    hostTeam = (data.hostDefault && rating[data.hostDefault] !== undefined) ? data.hostDefault : "";
    hostSelect.value = hostTeam || "";

    slots = null;
    games = [];
    picks = {};

    $("simOutput").innerHTML = '<span class="muted">Run odds to see results.</span>';
    $("drawInfo").textContent = "Pick a sectional and click Generate Draw.";
    $("formatNote").textContent = "";

    renderSectional();
  }

  function renderSectional() {
    const bracketEl = $("bracket");
    bracketEl.innerHTML = "";

    const drawInfo = $("drawInfo");
    const formatNote = $("formatNote");

    if (!slots) {
      drawInfo.textContent = "Pick a sectional and click Generate Draw.";
      formatNote.textContent = "";
      return;
    }

    drawInfo.textContent = sectionalFormatLine(slots);

    if (slots.length === 5) formatNote.textContent = "Format: 5-team";
    else if (slots.length === 7) formatNote.textContent = "Format: 7-team";
    else formatNote.textContent = "";

    const winners = computeWinnersFromPicksLocal(games, picks);
    const adv = homeAdvPoints();

    for (let i = 0; i < games.length; i++) {
      bracketEl.appendChild(
        renderGameCardWithContext({
          g: games[i],
          winners: winners,
          picksLocal: picks,
          ratingMap: rating,
          host: hostTeam,
          adv: adv,
          onPick: function (gid, team) {
            picks[gid] = (picks[gid] === team) ? null : team;
            if (!picks[gid]) delete picks[gid];
            validateDownstreamLocal(games, picks);
            renderSectional();
          }
        })
      );
      if (i !== games.length - 1) {
        const hr = document.createElement("div");
        hr.className = "hr";
        bracketEl.appendChild(hr);
      }
    }
  }

  function simulateSectionalOnce(gamesLocal, picksLocal, ratingMap, host, adv) {
    const winners = {};
    for (let i = 0; i < gamesLocal.length; i++) {
      const g = gamesLocal[i];
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);
      if (!a || !b) { winners[g.id] = null; continue; }
      if (picksLocal[g.id] === a || picksLocal[g.id] === b) { winners[g.id] = picksLocal[g.id]; continue; }
      winners[g.id] = (Math.random() < winProbFrom(ratingMap, host, adv, a, b)) ? a : b;
    }
    const last = gamesLocal[gamesLocal.length - 1];
    return winners[last.id] || null;
  }

  function runSectionalOdds() {
    if (!slots || games.length === 0) { alert("Generate the draw first."); return; }
    validateDownstreamLocal(games, picks);

    const n = simCount();
    const champs = {};
    for (let i = 0; i < teamList.length; i++) champs[teamList[i]] = 0;

    const adv = homeAdvPoints();

    for (let i = 0; i < n; i++) {
      const champ = simulateSectionalOnce(games, picks, rating, hostTeam, adv);
      if (champ) champs[champ] += 1;
    }

    const sorted = teamList.slice().sort(function(a,b){ return champs[b] - champs[a]; });
    const lines = [];
    lines.push("Sectional: " + currentSectionalKey);
    lines.push("Simulations: " + n);
    lines.push("");
    for (let i = 0; i < sorted.length; i++) {
      const t = sorted[i];
      lines.push(t.padEnd(22) + ": " + ((champs[t]/n)*100).toFixed(2) + "%");
    }
    $("simOutput").innerHTML = "<pre>" + linesToText(lines) + "</pre>";
  }

  function initTournamentState() {
    tour.sectionals = {};
    tour.pairing = null;
    tour.regionalGames = [];
    tour.regionalPicks = {};

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const skey = keys[i];
      const teams = SECTIONALS[skey].teams;
      const ratingMap = {};
      const names = [];
      for (let j = 0; j < teams.length; j++) {
        ratingMap[teams[j].name] = teams[j].rating;
        names.push(teams[j].name);
      }

      tour.sectionals[skey] = {
        skey: skey,
        host: SECTIONALS[skey].hostDefault || "",
        ratingMap: ratingMap,
        teams: names,
        slots: null,
        games: [],
        picks: {}
      };
    }
  }

  function tourHasAllDraws() {
    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      if (!tour.sectionals[keys[i]].slots) return false;
    }
    return true;
  }

  function tourClearAllPicks() {
    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      clearPicksObj(tour.sectionals[keys[i]].picks);
    }
    clearPicksObj(tour.regionalPicks);
  }

  function tourSectionalChampionFromPicks(sec) {
    if (!sec.slots || sec.games.length === 0) return null;
    validateDownstreamLocal(sec.games, sec.picks);
    const winners = computeWinnersFromPicksLocal(sec.games, sec.picks);
    const last = sec.games[sec.games.length - 1];
    return winners[last.id] || null;
  }

  function tourBuildRegionalGames() {
    if (!tour.pairing) return;

    const keys = tour.pairing.slice();
    const A = keys[0], B = keys[1], C = keys[2], D = keys[3];

    tour.regionalGames = [
      { id: "R1", title: "Regional 1", a: { win: "SEC:" + A }, b: { win: "SEC:" + B } },
      { id: "R2", title: "Regional 2", a: { win: "SEC:" + C }, b: { win: "SEC:" + D } },
      { id: "RF", title: "Final", a: { win: "R1" }, b: { win: "R2" } }
    ];

    validateDownstreamLocal(tour.regionalGames, tour.regionalPicks);
  }

  function tourResolveRegionalTeam(x, winners, secChampWinners) {
    if (typeof x === "object" && x && typeof x.win === "string") {
      if (x.win.indexOf("SEC:") === 0) {
        if (Object.prototype.hasOwnProperty.call(secChampWinners, x.win)) return secChampWinners[x.win];
        return null;
      }
      if (Object.prototype.hasOwnProperty.call(winners, x.win)) return winners[x.win];
      return null;
    }
    return x;
  }

  function tourComputeRegionalWinners() {
    const winners = {};
    const secChampWinners = {};

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const k = keys[i];
      const champ = tourSectionalChampionFromPicks(tour.sectionals[k]);
      secChampWinners["SEC:" + k] = champ;
    }

    for (let i = 0; i < tour.regionalGames.length; i++) {
      const g = tour.regionalGames[i];
      const a = tourResolveRegionalTeam(g.a, winners, secChampWinners);
      const b = tourResolveRegionalTeam(g.b, winners, secChampWinners);
      if (!a || !b) continue;
      const p = tour.regionalPicks[g.id];
      if (p === a || p === b) winners[g.id] = p;
    }

    return { winners: winners, secChampWinners: secChampWinners };
  }

  function tourValidateRegionalDownstream() {
    const winners = {};
    const secChampWinners = {};

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const k = keys[i];
      secChampWinners["SEC:" + k] = tourSectionalChampionFromPicks(tour.sectionals[k]);
    }

    for (let i = 0; i < tour.regionalGames.length; i++) {
      const g = tour.regionalGames[i];
      const a = tourResolveRegionalTeam(g.a, winners, secChampWinners);
      const b = tourResolveRegionalTeam(g.b, winners, secChampWinners);

      if (!a || !b) {
        if (tour.regionalPicks[g.id]) delete tour.regionalPicks[g.id];
        continue;
      }

      if (tour.regionalPicks[g.id] && tour.regionalPicks[g.id] !== a && tour.regionalPicks[g.id] !== b) {
        delete tour.regionalPicks[g.id];
        continue;
      }

      if (tour.regionalPicks[g.id]) winners[g.id] = tour.regionalPicks[g.id];
    }
  }

  function renderTournament() {
    const grid = $("tourSectionalGrid");
    grid.innerHTML = "";

    const adv = homeAdvPoints();

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const skey = keys[i];
      const sec = tour.sectionals[skey];

      const card = document.createElement("div");
      card.className = "card";

      const h = document.createElement("div");
      h.style.fontWeight = "800";
      h.style.marginBottom = "6px";
      h.textContent = skey + " (host: " + (sec.host || "None") + ")";
      card.appendChild(h);

      const info = document.createElement("div");
      info.className = "small";
      info.textContent = sec.slots ? sectionalFormatLine(sec.slots) : "Generate sectional draws to see matchups.";
      card.appendChild(info);

      const bracket = document.createElement("div");
      bracket.className = "bracket";

      if (sec.slots && sec.games.length) {
        validateDownstreamLocal(sec.games, sec.picks);
        const winners = computeWinnersFromPicksLocal(sec.games, sec.picks);

        for (let j = 0; j < sec.games.length; j++) {
          bracket.appendChild(
            renderGameCardWithContext({
              g: sec.games[j],
              winners: winners,
              picksLocal: sec.picks,
              ratingMap: sec.ratingMap,
              host: sec.host,
              adv: adv,
              onPick: function (gid, team) {
                sec.picks[gid] = (sec.picks[gid] === team) ? null : team;
                if (!sec.picks[gid]) delete sec.picks[gid];
                validateDownstreamLocal(sec.games, sec.picks);
                tourValidateRegionalDownstream();
                renderTournament();
              }
            })
          );
          if (j !== sec.games.length - 1) {
            const hr = document.createElement("div");
            hr.className = "hr";
            bracket.appendChild(hr);
          }
        }
      }

      card.appendChild(bracket);
      grid.appendChild(card);
    }

    const regBlock = $("tourRegionalBlock");
    regBlock.innerHTML = "";

    if (!tour.pairing) {
      $("tourDrawInfo").textContent = tourHasAllDraws() ? "All sectionals generated. Draw regionals next." : "Generate sectional draws to start.";
      return;
    }

    $("tourDrawInfo").textContent = "Regional draw: Winner(" + tour.pairing[0] + ") vs Winner(" + tour.pairing[1] + "), and Winner(" + tour.pairing[2] + ") vs Winner(" + tour.pairing[3] + ").";

    const regTitle = document.createElement("div");
    regTitle.style.fontWeight = "800";
    regTitle.style.marginBottom = "6px";
    regTitle.textContent = "Regionals + Final";
    regBlock.appendChild(regTitle);

    const ratingAll = buildRatingAll();

    tourValidateRegionalDownstream();
    const computed = tourComputeRegionalWinners();
    const winners = computed.winners;
    const secChampWinners = computed.secChampWinners;

    for (let i = 0; i < tour.regionalGames.length; i++) {
      const g = tour.regionalGames[i];
      const a = tourResolveRegionalTeam(g.a, winners, secChampWinners);
      const b = tourResolveRegionalTeam(g.b, winners, secChampWinners);

      const holder = document.createElement("div");
      holder.className = "game";

      const t = document.createElement("div");
      t.className = "game-title";
      t.textContent = g.title;
      holder.appendChild(t);

      const split = document.createElement("div");
      split.className = "split";

      const mid = document.createElement("div");
      mid.className = "center";

      if (!a || !b) {
        mid.textContent = "Waiting";

        const b1 = document.createElement("button");
        b1.className = "btn";
        b1.textContent = a ? ("Pick " + a) : "Pick Winner";
        b1.disabled = true;

        const b2 = document.createElement("button");
        b2.className = "btn";
        b2.textContent = b ? ("Pick " + b) : "Pick Winner";
        b2.disabled = true;

        split.appendChild(b1);
        split.appendChild(mid);
        split.appendChild(b2);
        holder.appendChild(split);

        const info = document.createElement("div");
        info.className = "muted";
        info.style.marginTop = "8px";
        info.textContent = "Waiting on sectional champs / earlier winners.";
        holder.appendChild(info);

        regBlock.appendChild(holder);
        if (i !== tour.regionalGames.length - 1) {
          const hr = document.createElement("div");
          hr.className = "hr";
          regBlock.appendChild(hr);
        }
        continue;
      }

      mid.textContent = tour.regionalPicks[g.id] ? "Locked" : "Pick winner";

      const b1 = document.createElement("button");
      b1.className = "btn" + (tour.regionalPicks[g.id] === a ? " win" : "");
      b1.textContent = "Pick " + a;

      const b2 = document.createElement("button");
      b2.className = "btn" + (tour.regionalPicks[g.id] === b ? " win" : "");
      b2.textContent = "Pick " + b;

      b1.onclick = function () {
        tour.regionalPicks[g.id] = (tour.regionalPicks[g.id] === a) ? null : a;
        if (!tour.regionalPicks[g.id]) delete tour.regionalPicks[g.id];
        tourValidateRegionalDownstream();
        renderTournament();
      };

      b2.onclick = function () {
        tour.regionalPicks[g.id] = (tour.regionalPicks[g.id] === b) ? null : b;
        if (!tour.regionalPicks[g.id]) delete tour.regionalPicks[g.id];
        tourValidateRegionalDownstream();
        renderTournament();
      };

      split.appendChild(b1);
      split.appendChild(mid);
      split.appendChild(b2);
      holder.appendChild(split);

      const pA = winProbFrom(ratingAll, "", 0, a, b);
      const pB = 1 - pA;

      const info = document.createElement("div");
      info.className = "muted";
      info.style.marginTop = "8px";
      info.textContent = a + ": " + (pA*100).toFixed(1) + "%  |  " + b + ": " + (pB*100).toFixed(1) + "% (neutral)";
      holder.appendChild(info);

      regBlock.appendChild(holder);
      if (i !== tour.regionalGames.length - 1) {
        const hr = document.createElement("div");
        hr.className = "hr";
        regBlock.appendChild(hr);
      }
    }
  }

  function tourSimOneSectional(sec, adv) {
    const winners = {};
    for (let i = 0; i < sec.games.length; i++) {
      const g = sec.games[i];
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);
      if (!a || !b) { winners[g.id] = null; continue; }
      if (sec.picks[g.id] === a || sec.picks[g.id] === b) { winners[g.id] = sec.picks[g.id]; continue; }
      winners[g.id] = (Math.random() < winProbFrom(sec.ratingMap, sec.host, adv, a, b)) ? a : b;
    }
    const last = sec.games[sec.games.length - 1];
    return winners[last.id] || null;
  }

  function tourSimTournamentOnce() {
    const adv = homeAdvPoints();
    const ratingAll = buildRatingAll();

    const champs = {};
    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const k = keys[i];
      const sec = tour.sectionals[k];
      champs[k] = tourSimOneSectional(sec, adv);
      if (!champs[k]) return null;
    }

    if (!tour.pairing) return null;

    const aK = tour.pairing[0], bK = tour.pairing[1], cK = tour.pairing[2], dK = tour.pairing[3];

    const r1a = champs[aK];
    const r1b = champs[bK];
    const r2a = champs[cK];
    const r2b = champs[dK];

    const r1Pick = tour.regionalPicks["R1"];
    const pR1 = winProbFrom(ratingAll, "", 0, r1a, r1b);
    const R1 = (r1Pick === r1a || r1Pick === r1b) ? r1Pick : ((Math.random() < pR1) ? r1a : r1b);

    const r2Pick = tour.regionalPicks["R2"];
    const pR2 = winProbFrom(ratingAll, "", 0, r2a, r2b);
    const R2 = (r2Pick === r2a || r2Pick === r2b) ? r2Pick : ((Math.random() < pR2) ? r2a : r2b);

    const fPick = tour.regionalPicks["RF"];
    const pF = winProbFrom(ratingAll, "", 0, R1, R2);
    const champ = (fPick === R1 || fPick === R2) ? fPick : ((Math.random() < pF) ? R1 : R2);

    return { champ: champ, champs: champs };
  }

  function runTournamentOdds() {
    if (!tourHasAllDraws()) { alert("Generate all sectional draws first."); return; }
    if (!tour.pairing) { alert("Draw regionals first."); return; }

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const sec = tour.sectionals[keys[i]];
      validateDownstreamLocal(sec.games, sec.picks);
    }
    tourValidateRegionalDownstream();

    const n = simCount();
    const ratingAll = buildRatingAll();

    const allTeams = Object.keys(ratingAll);
    const champCounts = {};
    for (let i = 0; i < allTeams.length; i++) champCounts[allTeams[i]] = 0;

    const sectionalCounts = {};
    for (let i = 0; i < keys.length; i++) {
      const skey = keys[i];
      const names = SECTIONALS[skey].teams.map(function(t){ return t.name; });
      sectionalCounts[skey] = {};
      for (let j = 0; j < names.length; j++) sectionalCounts[skey][names[j]] = 0;
    }

    for (let i = 0; i < n; i++) {
      const res = tourSimTournamentOnce();
      if (!res) continue;

      champCounts[res.champ] += 1;

      for (let j = 0; j < keys.length; j++) {
        const skey = keys[j];
        const w = res.champs[skey];
        if (w) sectionalCounts[skey][w] += 1;
      }
    }

    const lines = [];
    lines.push("Simulations: " + n);
    lines.push("");

    lines.push("SECTIONAL WIN ODDS");
    for (let i = 0; i < keys.length; i++) {
      const skey = keys[i];
      lines.push("");
      lines.push(skey.toUpperCase());

      const names = Object.keys(sectionalCounts[skey]);
      const sortedSec = names.slice().sort(function(a,b){ return sectionalCounts[skey][b] - sectionalCounts[skey][a]; });

      for (let j = 0; j < sortedSec.length; j++) {
        const t = sortedSec[j];
        lines.push(t.padEnd(22) + ": " + ((sectionalCounts[skey][t]/n)*100).toFixed(2) + "%");
      }
    }

    lines.push("");
    lines.push("TOURNAMENT CHAMP ODDS");

    const sortedChamp = allTeams.slice().sort(function(a,b){ return champCounts[b] - champCounts[a]; }).slice(0, 20);
    for (let i = 0; i < sortedChamp.length; i++) {
      const t = sortedChamp[i];
      lines.push(t.padEnd(22) + ": " + ((champCounts[t]/n)*100).toFixed(2) + "%");
    }

    $("tourOdds").innerHTML = "<pre>" + linesToText(lines) + "</pre>";
  }

  function setTab(which) {
    const a = which === "sectional";
    $("tabSectional").classList.toggle("active", a);
    $("tabTournament").classList.toggle("active", !a);
    $("panelSectional").classList.toggle("active", a);
    $("panelTournament").classList.toggle("active", !a);
  }

  function assert(cond, msg) { if (!cond) throw new Error(msg); }

  function runTests() {
    assert(Object.keys(SECTIONALS).length >= 4, "Expected at least 4 sectionals");
    assert(SECTIONALS.Mishawaka.teams.length === 7, "Mishawaka must be a 7-team sectional");
    assert(SECTIONALS.Elkhart.teams.length === 5, "Elkhart must be a 5-team sectional");
    assert(SECTIONALS["Huntington North"].teams.length === 4, "Huntington North must be a 4-team sectional");
    assert(SECTIONALS.Noblesville.teams.length === 6, "Noblesville must be a 6-team sectional");

    const ratingAll = buildRatingAll();
    assert(Object.keys(ratingAll).length > 0, "ratingAll should not be empty");

    const p = winProbFrom(ratingAll, "", 0, "Penn", "Mishawaka");
    assert(p > 0 && p < 1, "winProbFrom should return a probability");

    const slots5 = shuffle(SECTIONALS.Elkhart.teams.map(function(t){ return t.name; }));
    const games5 = buildGamesFromSlotsLocal(slots5);
    assert(games5.length === 4, "5-team should create 4 games");

    const slots7 = shuffle(SECTIONALS.Mishawaka.teams.map(function(t){ return t.name; }));
    const games7 = buildGamesFromSlotsLocal(slots7);
    assert(games7.length === 6, "7-team should create 6 games");

    const bad = buildGamesFromSlotsLocal(["A","B","C","D"]);
    assert(Array.isArray(bad) && bad.length === 0, "unsupported size should return []");

    const txt = linesToText(["A", "B", "C"]);
    assert(txt === "A\nB\nC", "linesToText must join with \\n");

    initTournamentState();
    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const sec = tour.sectionals[keys[i]];
      sec.slots = shuffle(sec.teams);
      sec.games = buildGamesFromSlotsLocal(sec.slots);
    }
    assert(tourHasAllDraws(), "tour should have all draws after setup");

    tour.pairing = shuffle(Object.keys(SECTIONALS));
    tourBuildRegionalGames();
    assert(tour.regionalGames.length === 3, "tournament should have 3 regional/final games");
  }

  function initUI() {
    $("tabSectional").onclick = function () { setTab("sectional"); };
    $("tabTournament").onclick = function () { setTab("tournament"); };

    const sectionalSelect = $("sectionalSelect");
    sectionalSelect.innerHTML = "";

    const keys = Object.keys(SECTIONALS);
    for (let i = 0; i < keys.length; i++) {
      const key = keys[i];
      const opt = document.createElement("option");
      opt.value = key;
      opt.textContent = key;
      sectionalSelect.appendChild(opt);
    }

    sectionalSelect.value = currentSectionalKey;
    sectionalSelect.onchange = function () { loadSectional(sectionalSelect.value); };

    $("hostSelect").onchange = function (e) {
      hostTeam = e.target.value || "";
      validateDownstreamLocal(games, picks);
      renderSectional();
    };

    $("homeAdv").onchange = function () {
      validateDownstreamLocal(games, picks);
      renderSectional();
      renderTournament();
    };

    $("genDraw").onclick = function () {
      for (let i = 0; i < teamList.length; i++) {
        const t = teamList[i];
        if (!Number.isFinite(rating[t])) { alert("Missing/bad rating for: " + t); return; }
      }

      slots = shuffle(teamList);
      games = buildGamesFromSlotsLocal(slots);
      picks = {};
      validateDownstreamLocal(games, picks);

      $("simOutput").innerHTML = '<span class="muted">Run odds to see results.</span>';
      renderSectional();
    };

    $("resetPicks").onclick = function () {
      if (!slots) return;
      picks = {};
      validateDownstreamLocal(games, picks);
      $("simOutput").innerHTML = '<span class="muted">Run odds to see results.</span>';
      renderSectional();
    };

    $("runSims").onclick = function () { runSectionalOdds(); };

    $("tourGenAll").onclick = function () {
      initTournamentState();
      const keys = Object.keys(SECTIONALS);
      for (let i = 0; i < keys.length; i++) {
        const k = keys[i];
        const sec = tour.sectionals[k];
        sec.slots = shuffle(sec.teams);
        sec.games = buildGamesFromSlotsLocal(sec.slots);
        clearPicksObj(sec.picks);
      }
      tour.pairing = null;
      tour.regionalGames = [];
      clearPicksObj(tour.regionalPicks);
      $("tourOdds").innerHTML = '<span class="muted">Run tournament odds to see results.</span>';
      renderTournament();
    };

    $("tourDrawRegionals").onclick = function () {
      if (!tourHasAllDraws()) { alert("Generate all sectional draws first."); return; }
      tour.pairing = shuffle(Object.keys(SECTIONALS));
      tourBuildRegionalGames();
      clearPicksObj(tour.regionalPicks);
      $("tourOdds").innerHTML = '<span class="muted">Run tournament odds to see results.</span>';
      renderTournament();
    };

    $("tourReset").onclick = function () {
      if (!tourHasAllDraws()) return;
      tourClearAllPicks();
      $("tourOdds").innerHTML = '<span class="muted">Run tournament odds to see results.</span>';
      renderTournament();
    };

    $("tourRunOdds").onclick = function () { runTournamentOdds(); };

    initTournamentState();
    loadSectional(currentSectionalKey);
    renderTournament();
  }

  // runTests();
  initUI();
</script>
</body>
</html>
