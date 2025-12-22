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

    .controls { display: flex; gap: 10px; flex-wrap: wrap; align-items: center; }
    .pill { display: inline-block; padding: 2px 8px; border-radius: 999px; background: #f2f2f2; font-size: 12px; }

    input[type="number"], select {
      padding: 6px;
      border-radius: 8px;
      border: 1px solid #ccc;
      max-width: 100%;
    }
    input[type="number"] { width: 110px; }
    select { max-width: 260px; }
    .note { margin-top: 6px; font-size: 12px; color: #555; }

    .btn{
      padding: 8px 10px;
      border: 1px solid #ccc;
      background: #fff;
      border-radius: 8px;
      cursor: pointer;

      /* stops vertical letter stacking */
      white-space: normal;
      overflow-wrap: normal;
      word-break: normal;

      /* gives team names room */
      min-width: 160px;
    }
    .btn:hover { background: #f6f6f6; }
    .btn.win { border-color: #111; font-weight: 800; }

    .game { border: 1px solid #eee; border-radius: 10px; padding: 10px; margin: 10px 0; overflow: hidden; }
    .game-title { font-weight: 800; margin-bottom: 6px; }

    .split{
      display: grid;
      grid-template-columns: minmax(160px, 1fr) max-content minmax(160px, 1fr);
      gap: 12px;
      align-items: center;
    }

    .center { text-align: center; font-size: 12px; color: #444; }
    .bracket { display: grid; grid-template-columns: 1fr; gap: 10px; }
    .small { font-size: 12px; color: #555; }
    .hr { height: 1px; background: #eee; margin: 10px 0; }

    .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    @media (max-width: 980px) { .grid2 { grid-template-columns: 1fr; } }

    pre { white-space: pre-wrap; word-break: break-word; }

    @media (max-width: 720px) {
      body { margin: 12px; }
      .tabbtn { width: 100%; }
      input[type="number"], select { width: 100%; max-width: 100%; }
      select { max-width: 100%; }
      .controls > * { flex: 1 1 auto; }

      /* on mobile: stack vertically */
      .split { grid-template-columns: 1fr; }
      .center { text-align: left; }
      .btn { width: 100%; min-width: 0; }
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

  <div class="card" style="margin-top:12px;">
    <div class="controls">
      <span class="pill">Home adv</span>
      <input type="number" id="homeAdv" value="2.0" step="


<script>
  const SECTIONALS = {
    "Elkhart": {
      hostDefault: "Elkhart",
      teams: [
        { name: "Northridge", rating: 88.20 },
        { name: "Goshen", rating: 86.18 },
        { name: "Elkhart", rating: 80.35 },
        { name: "Warsaw", rating: 74.70 },
        { name: "Concord", rating: 65.57 },
      ]
    },
    "Crown Point": {
      hostDefault: "Crown Point",
      teams: [
        { name: "Crown Point", rating: 89.72 },
        { name: "Lake Central", rating: 81.51 },
        { name: "Munster", rating: 66.98 },
        { name: "Hammond Morton", rating: 60.27 },
        { name: "Hammond Central", rating: 60.06 },
      ]
    },
    "Chesterton": {
      hostDefault: "Chesterton",
      teams: [
        { name: "Chesterton", rating: 92.29 },
        { name: "Portage", rating: 80.79 },
        { name: "Valparaiso", rating: 74.72 },
        { name: "Merrillville", rating: 66.34 },
        { name: "Hobart", rating: 59.81 },
      ]
    },
    "Mishawaka": {
      hostDefault: "Mishawaka",
      teams: [
        { name: "Penn", rating: 91.39 },
        { name: "South Bend Riley", rating: 89.05 },
        { name: "South Bend St. Joseph", rating: 83.80 },
        { name: "Michigan City", rating: 81.36 },
        { name: "South Bend Adams", rating: 76.06 },
        { name: "LaPorte", rating: 72.66 },
        { name: "Mishawaka", rating: 63.57 },
      ]
    },
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
    regionalPicks: {},
  };

  function $(id) { return document.getElementById(id); }

  function shuffle(arr) {
    const a = arr.slice();
    for (let i = a.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [a[i], a[j]] = [a[j], a[i]];
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
    for (const k of Object.keys(SECTIONALS)) {
      for (const t of SECTIONALS[k].teams) all[t.name] = t.rating;
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
    for (const k of Object.keys(obj)) delete obj[k];
  }

  function isTeamRef(x) {
    return typeof x === "object" && x && typeof x.win === "string";
  }

  function resolveTeam(x, winners) {
    if (isTeamRef(x)) return winners[x.win] ?? null;
    return x;
  }

  function buildGamesFromSlotsLocal(slotsLocal) {
    const n = slotsLocal.length;

    if (n === 5) {
      const A = slotsLocal[0], B = slotsLocal[1], C = slotsLocal[2], D = slotsLocal[3], E = slotsLocal[4];
      return [
        { id: "P", title: "Play-in", a: A, b: B },
        { id: "S1", title: "Semi 1", a: { win: "P" }, b: C },
        { id: "S2", title: "Semi 2", a: D, b: E },
        { id: "F", title: "Final", a: { win: "S1" }, b: { win: "S2" } },
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
        { id: "G6", title: "Championship", a: { win: "G4" }, b: { win: "G5" } },
      ];
    }

    return [];
  }

  function computeWinnersFromPicksLocal(gamesLocal, picksLocal) {
    const winners = {};
    for (const g of gamesLocal) {
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
    for (const g of gamesLocal) {
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
    const { g, winners, picksLocal, onPick, ratingMap, host, adv } = opts;

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
      btn1.textContent = a ? `Pick ${a}` : "Pick Winner";
      btn1.disabled = true;

      const btn2 = document.createElement("button");
      btn2.className = "btn";
      btn2.textContent = b ? `Pick ${b}` : "Pick Winner";
      btn2.disabled = true;

      split.append(btn1, mid, btn2);
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
    btn1.textContent = `Pick ${a}`;

    const btn2 = document.createElement("button");
    btn2.className = "btn" + (picksLocal[g.id] === b ? " win" : "");
    btn2.textContent = `Pick ${b}`;

    btn1.onclick = () => onPick(g.id, a);
    btn2.onclick = () => onPick(g.id, b);

    split.append(btn1, mid, btn2);
    div.appendChild(split);

    const pA = winProbFrom(ratingMap, host, adv, a, b);
    const pB = 1 - pA;
    const hostNote = (host && (a === host || b === host) && adv !== 0)
      ? ` (host ${host}, +${adv.toFixed(1)} pts)`
      : "";

    const info = document.createElement("div");
    info.className = "muted";
    info.style.marginTop = "8px";
    info.textContent = `${a}: ${(pA*100).toFixed(1)}%  |  ${b}: ${(pB*100).toFixed(1)}%${hostNote}`;
    div.appendChild(info);

    return div;
  }

  function sectionalFormatLine(slotsLocal) {
    const n = slotsLocal.length;
    if (n === 5) {
      return `Draw: Play-in=${slotsLocal[0]} vs ${slotsLocal[1]}. Semis: Winner(Play-in) vs ${slotsLocal[2]}, and ${slotsLocal[3]} vs ${slotsLocal[4]}.`;
    }
    if (n === 7) {
      return `Draw: G1=${slotsLocal[0]} vs ${slotsLocal[1]}, G2=${slotsLocal[2]} vs ${slotsLocal[3]}, G3=${slotsLocal[4]} vs ${slotsLocal[5]}. ${slotsLocal[6]} has the bye into Game 4.`;
    }
    return `This sectional has ${n} teams. Only 5-team and 7-team are supported.`;
  }

  function loadSectional(key) {
    currentSectionalKey = key;
    const data = SECTIONALS[key];

    rating = {};
    teamList = [];
    for (const t of data.teams) {
      rating[t.name] = t.rating;
      teamList.push(t.name);
    }

    const hostSelect = $("hostSelect");
    hostSelect.innerHTML = "";

    const optNeutral = document.createElement("option");
    optNeutral.value = "";
    optNeutral.textContent = "None (neutral)";
    hostSelect.appendChild(optNeutral);

    for (const nm of teamList) {
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

    $("simOutput").innerHTML = `<span class="muted">Run odds to see results.</span>`;
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
          winners,
          picksLocal: picks,
          ratingMap: rating,
          host: hostTeam,
          adv,
          onPick: (gid, team) => {
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
    for (const g of gamesLocal) {
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);
      if (!a || !b) { winners[g.id] = null; continue; }
      if (picksLocal[g.id] === a || picksLocal[g.id] === b) { winners[g.id] = picksLocal[g.id]; continue; }
      winners[g.id] = (Math.random() < winProbFrom(ratingMap, host, adv, a, b)) ? a : b;
    }
    const last = gamesLocal[gamesLocal.length - 1];
    return winners[last.id] ?? null;
  }

  function runSectionalOdds() {
    if (!slots || games.length === 0) { alert("Generate the draw first."); return; }
    validateDownstreamLocal(games, picks);

    const n = simCount();
    const champs = Object.fromEntries(teamList.map(t => [t, 0]));
    const adv = homeAdvPoints();

    for (let i = 0; i < n; i++) {
      const champ = simulateSectionalOnce(games, picks, rating, hostTeam, adv);
      if (champ) champs[champ] += 1;
    }

    const sorted = teamList.slice().sort((a,b) => champs[b] - champs[a]);
    const lines = [];
    lines.push(`Sectional: ${currentSectionalKey}`);
    lines.push(`Simulations: ${n}`);
    lines.push("");
    for (const t of sorted) lines.push(`${t.padEnd(22)}: ${(champs[t]/n*100).toFixed(2)}%`);
    $("simOutput").innerHTML = `<pre>${linesToText(lines)}</pre>`;
  }

  function initTournamentState() {
    tour.sectionals = {};
    tour.pairing = null;
    tour.regionalGames = [];
    tour.regionalPicks = {};

    for (const skey of Object.keys(SECTIONALS)) {
      tour.sectionals[skey] = {
        skey,
        host: SECTIONALS[skey].hostDefault || "",
        ratingMap: Object.fromEntries(SECTIONALS[skey].teams.map(t => [t.name, t.rating])),
        teams: SECTIONALS[skey].teams.map(t => t.name),
        slots: null,
        games: [],
        picks: {},
      };
    }
  }

  function tourHasAllDraws() {
    for (const k of Object.keys(SECTIONALS)) {
      if (!tour.sectionals[k].slots) return false;
    }
    return true;
  }

  function tourClearAllPicks() {
    for (const k of Object.keys(SECTIONALS)) {
      clearPicksObj(tour.sectionals[k].picks);
    }
    clearPicksObj(tour.regionalPicks);
  }

  function tourSectionalChampionFromPicks(sec) {
    if (!sec.slots || sec.games.length === 0) return null;
    validateDownstreamLocal(sec.games, sec.picks);
    const winners = computeWinnersFromPicksLocal(sec.games, sec.picks);
    const last = sec.games[sec.games.length - 1];
    return winners[last.id] ?? null;
  }

  function tourBuildRegionalGames() {
    if (!tour.pairing) return;
    const keys = tour.pairing.slice();
    const A = keys[0], B = keys[1], C = keys[2], D = keys[3];

    tour.regionalGames = [
      { id: "R1", title: "Regional 1", a: { win: `SEC:${A}` }, b: { win: `SEC:${B}` } },
      { id: "R2", title: "Regional 2", a: { win: `SEC:${C}` }, b: { win: `SEC:${D}` } },
      { id: "RF", title: "Final", a: { win: "R1" }, b: { win: "R2" } },
    ];

    validateDownstreamLocal(tour.regionalGames, tour.regionalPicks);
  }

  function tourResolveRegionalTeam(x, winners, secChampWinners) {
    if (typeof x === "object" && x && typeof x.win === "string") {
      if (x.win.startsWith("SEC:")) {
        return secChampWinners[x.win] ?? null;
      }
      return winners[x.win] ?? null;
    }
    return x;
  }

  function tourComputeRegionalWinners() {
    const winners = {};
    const secChampWinners = {};

    for (const k of Object.keys(SECTIONALS)) {
      const champ = tourSectionalChampionFromPicks(tour.sectionals[k]);
      secChampWinners[`SEC:${k}`] = champ;
    }

    for (const g of tour.regionalGames) {
      const a = tourResolveRegionalTeam(g.a, winners, secChampWinners);
      const b = tourResolveRegionalTeam(g.b, winners, secChampWinners);
      if (!a || !b) continue;
      const p = tour.regionalPicks[g.id];
      if (p === a || p === b) winners[g.id] = p;
    }

    return { winners, secChampWinners };
  }

  function tourValidateRegionalDownstream() {
    const winners = {};
    const secChampWinners = {};

    for (const k of Object.keys(SECTIONALS)) {
      secChampWinners[`SEC:${k}`] = tourSectionalChampionFromPicks(tour.sectionals[k]);
    }

    for (const g of tour.regionalGames) {
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

    for (const skey of Object.keys(SECTIONALS)) {
      const sec = tour.sectionals[skey];

      const card = document.createElement("div");
      card.className = "card";

      const h = document.createElement("div");
      h.style.fontWeight = "800";
      h.style.marginBottom = "6px";
      h.textContent = `${skey} (host: ${sec.host || "None"})`;
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

        for (let i = 0; i < sec.games.length; i++) {
          bracket.appendChild(
            renderGameCardWithContext({
              g: sec.games[i],
              winners,
              picksLocal: sec.picks,
              ratingMap: sec.ratingMap,
              host: sec.host,
              adv,
              onPick: (gid, team) => {
                sec.picks[gid] = (sec.picks[gid] === team) ? null : team;
                if (!sec.picks[gid]) delete sec.picks[gid];
                validateDownstreamLocal(sec.games, sec.picks);
                tourValidateRegionalDownstream();
                renderTournament();
              }
            })
          );
          if (i !== sec.games.length - 1) {
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
      $("tourDrawInfo").textContent = tourHasAllDraws()
        ? "All sectionals generated. Draw regionals next."
        : "Generate sectional draws to start.";
      return;
    }

    $("tourDrawInfo").textContent = `Regional draw: Winner(${tour.pairing[0]}) vs Winner(${tour.pairing[1]}), and Winner(${tour.pairing[2]}) vs Winner(${tour.pairing[3]}).`;

    const regTitle = document.createElement("div");
    regTitle.style.fontWeight = "800";
    regTitle.style.marginBottom = "6px";
    regTitle.textContent = "Regionals + Final";
    regBlock.appendChild(regTitle);

    const ratingAll = buildRatingAll();

    tourValidateRegionalDownstream();
    const { winners, secChampWinners } = tourComputeRegionalWinners();

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
        b1.textContent = a ? `Pick ${a}` : "Pick Winner";
        b1.disabled = true;

        const b2 = document.createElement("button");
        b2.className = "btn";
        b2.textContent = b ? `Pick ${b}` : "Pick Winner";
        b2.disabled = true;

        split.append(b1, mid, b2);
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
      b1.textContent = `Pick ${a}`;

      const b2 = document.createElement("button");
      b2.className = "btn" + (tour.regionalPicks[g.id] === b ? " win" : "");
      b2.textContent = `Pick ${b}`;

      b1.onclick = () => {
        tour.regionalPicks[g.id] = (tour.regionalPicks[g.id] === a) ? null : a;
        if (!tour.regionalPicks[g.id]) delete tour.regionalPicks[g.id];
        tourValidateRegionalDownstream();
        renderTournament();
      };

      b2.onclick = () => {
        tour.regionalPicks[g.id] = (tour.regionalPicks[g.id] === b) ? null : b;
        if (!tour.regionalPicks[g.id]) delete tour.regionalPicks[g.id];
        tourValidateRegionalDownstream();
        renderTournament();
      };

      split.append(b1, mid, b2);
      holder.appendChild(split);

      const pA = winProbFrom(ratingAll, "", 0, a, b);
      const pB = 1 - pA;

      const info = document.createElement("div");
      info.className = "muted";
      info.style.marginTop = "8px";
      info.textContent = `${a}: ${(pA*100).toFixed(1)}%  |  ${b}: ${(pB*100).toFixed(1)}% (neutral)`;
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
    for (const g of sec.games) {
      const a = resolveTeam(g.a, winners);
      const b = resolveTeam(g.b, winners);
      if (!a || !b) { winners[g.id] = null; continue; }
      if (sec.picks[g.id] === a || sec.picks[g.id] === b) { winners[g.id] = sec.picks[g.id]; continue; }
      winners[g.id] = (Math.random() < winProbFrom(sec.ratingMap, sec.host, adv, a, b)) ? a : b;
    }
    const last = sec.games[sec.games.length - 1];
    return winners[last.id] ?? null;
  }

  function tourSimTournamentOnce() {
    const adv = homeAdvPoints();
    const ratingAll = buildRatingAll();

    const champs = {};
    for (const k of Object.keys(SECTIONALS)) {
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

    return { champ, champs };
  }

  function runTournamentOdds() {
    if (!tourHasAllDraws()) { alert("Generate all sectional draws first."); return; }
    if (!tour.pairing) { alert("Draw regionals first."); return; }

    for (const k of Object.keys(SECTIONALS)) {
      const sec = tour.sectionals[k];
      validateDownstreamLocal(sec.games, sec.picks);
    }
    tourValidateRegionalDownstream();

    const n = simCount();
    const ratingAll = buildRatingAll();

    const allTeams = Object.keys(ratingAll);
    const champCounts = Object.fromEntries(allTeams.map(t => [t, 0]));

    const sectionalCounts = {};
    for (const skey of Object.keys(SECTIONALS)) {
      const names = SECTIONALS[skey].teams.map(t => t.name);
      sectionalCounts[skey] = Object.fromEntries(names.map(t => [t, 0]));
    }

    for (let i = 0; i < n; i++) {
      const res = tourSimTournamentOnce();
      if (!res) continue;

      champCounts[res.champ] += 1;

      for (const skey of Object.keys(SECTIONALS)) {
        const w = res.champs[skey];
        if (w) sectionalCounts[skey][w] += 1;
      }
    }

    const lines = [];
    lines.push(`Simulations: ${n}`);
    lines.push("");

    lines.push("SECTIONAL WIN ODDS");
    for (const skey of Object.keys(SECTIONALS)) {
      lines.push("");
      lines.push(skey.toUpperCase());

      const names = Object.keys(sectionalCounts[skey]);
      const sortedSec = names
        .slice()
        .sort((a,b) => sectionalCounts[skey][b] - sectionalCounts[skey][a]);

      for (const t of sortedSec) {
        lines.push(`${t.padEnd(22)}: ${(sectionalCounts[skey][t]/n*100).toFixed(2)}%`);
      }
    }

    lines.push("");
    lines.push("TOURNAMENT CHAMP ODDS");

    const sortedChamp = allTeams
      .slice()
      .sort((a,b) => champCounts[b] - champCounts[a])
      .slice(0, 20);

    for (const t of sortedChamp) lines.push(`${t.padEnd(22)}: ${(champCounts[t]/n*100).toFixed(2)}%`);

    $("tourOdds").innerHTML = `<pre>${linesToText(lines)}</pre>`;
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
    assert(Object.keys(SECTIONALS).length === 4, "Expected exactly 4 sectionals");
    assert(SECTIONALS.Mishawaka.teams.length === 7, "Mishawaka must be a 7-team sectional");
    assert(SECTIONALS.Elkhart.teams.length === 5, "Elkhart must be a 5-team sectional");

    const ratingAll = buildRatingAll();
    assert(Object.keys(ratingAll).length > 0, "ratingAll should not be empty");

    const p = winProbFrom(ratingAll, "", 0, "Penn", "Mishawaka");
    assert(p > 0 && p < 1, "winProbFrom should return a probability");

    const slots5 = shuffle(SECTIONALS.Elkhart.teams.map(t => t.name));
    const games5 = buildGamesFromSlotsLocal(slots5);
    assert(games5.length === 4, "5-team should create 4 games");

    const slots7 = shuffle(SECTIONALS.Mishawaka.teams.map(t => t.name));
    const games7 = buildGamesFromSlotsLocal(slots7);
    assert(games7.length === 6, "7-team should create 6 games");

    const txt = linesToText(["A", "B", "C"]);
    assert(txt === "A\nB\nC", "linesToText must join with \\n");

    initTournamentState();
    for (const k of Object.keys(SECTIONALS)) {
      const sec = tour.sectionals[k];
      sec.slots = shuffle(sec.teams);
      sec.games = buildGamesFromSlotsLocal(sec.slots);
    }
    assert(tourHasAllDraws(), "tour should have all draws after setup");

    tour.pairing = shuffle(Object.keys(SECTIONALS));
    tourBuildRegionalGames();
    assert(tour.regionalGames.length === 3, "tournament should have 3 regional/final games");
  }

  function initUI() {
    $("tabSectional").onclick = () => setTab("sectional");
    $("tabTournament").onclick = () => setTab("tournament");

    const sectionalSelect = $("sectionalSelect");
    sectionalSelect.innerHTML = "";
    for (const key of Object.keys(SECTIONALS)) {
      const opt = document.createElement("option");
      opt.value = key;
      opt.textContent = key;
      sectionalSelect.appendChild(opt);
    }

    sectionalSelect.value = currentSectionalKey;
    sectionalSelect.onchange = () => loadSectional(sectionalSelect.value);

    $("hostSelect").onchange = (e) => {
      hostTeam = e.target.value || "";
      validateDownstreamLocal(games, picks);
      renderSectional();
    };

    $("homeAdv").onchange = () => {
      validateDownstreamLocal(games, picks);
      renderSectional();
      renderTournament();
    };

    $("genDraw").onclick = () => {
      for (const t of teamList) {
        if (!Number.isFinite(rating[t])) { alert(`Missing/bad rating for: ${t}`); return; }
      }

      slots = shuffle(teamList);
      games = buildGamesFromSlotsLocal(slots);
      picks = {};
      validateDownstreamLocal(games, picks);

      $("simOutput").innerHTML = `<span class="muted">Run odds to see results.</span>`;
      renderSectional();
    };

    $("resetPicks").onclick = () => {
      if (!slots) return;
      picks = {};
      validateDownstreamLocal(games, picks);
      $("simOutput").innerHTML = `<span class="muted">Run odds to see results.</span>`;
      renderSectional();
    };

    $("runSims").onclick = () => runSectionalOdds();

    $("tourGenAll").onclick = () => {
      initTournamentState();
      for (const k of Object.keys(SECTIONALS)) {
        const sec = tour.sectionals[k];
        sec.slots = shuffle(sec.teams);
        sec.games = buildGamesFromSlotsLocal(sec.slots);
        clearPicksObj(sec.picks);
      }
      tour.pairing = null;
      tour.regionalGames = [];
      clearPicksObj(tour.regionalPicks);
      $("tourOdds").innerHTML = `<span class="muted">Run tournament odds to see results.</span>`;
      renderTournament();
    };

    $("tourDrawRegionals").onclick = () => {
      if (!tourHasAllDraws()) { alert("Generate all sectional draws first."); return; }
      tour.pairing = shuffle(Object.keys(SECTIONALS));
      tourBuildRegionalGames();
      clearPicksObj(tour.regionalPicks);
      $("tourOdds").innerHTML = `<span class="muted">Run tournament odds to see results.</span>`;
      renderTournament();
    };

    $("tourReset").onclick = () => {
      if (!tourHasAllDraws()) return;
      tourClearAllPicks();
      $("tourOdds").innerHTML = `<span class="muted">Run tournament odds to see results.</span>`;
      renderTournament();
    };

    $("tourRunOdds").onclick = () => runTournamentOdds();

    initTournamentState();
    loadSectional(currentSectionalKey);
    renderTournament();
  }

  runTests();
  initUI();
</script>
</body>
</html>
