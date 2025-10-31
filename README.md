<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Noobs in Combat — HTML Only (Single File, Turn-Based Tactics)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #0f1220;
      --panel: #171a2a;
      --accent: #49a6ff;
      --accent-2: #74ffba;
      --danger: #ff5d78;
      --warn: #ffcc66;
      --grid: #242943;
      --text: #eaf1ff;
      --muted: #9fb4d1;
      --tile: #2b3157;
      --tile-alt: #323967;
      --tile-highlight: #244663;
      --tile-move: #143f57;
      --tile-attack: #5a1f31;
      --tile-path: #1e6d9e;
      --obstacle: #3a2a2a;
      --blue: #4fb2ff;
      --red: #ff6b6b;
      --green: #54d68a;
    }

    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0;
      background: radial-gradient(1200px 600px at 20% 0%, #11162a, #0c0f1d 60%, #0a0d19 100%);
      color: var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
      display: grid;
      grid-template-rows: auto 1fr auto;
      grid-template-columns: 100%;
    }

    header {
      padding: 14px 16px;
      background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.00));
      border-bottom: 1px solid rgba(255,255,255,0.08);
      display: flex;
      align-items: center;
      gap: 16px;
    }
    header h1 {
      font-size: 18px;
      margin: 0;
      letter-spacing: 0.5px;
    }
    header .controls {
      margin-left: auto;
      display: flex;
      gap: 8px;
    }
    .btn {
      background: var(--panel);
      color: var(--text);
      border: 1px solid rgba(255,255,255,0.12);
      outline: none;
      padding: 8px 12px;
      border-radius: 8px;
      font-weight: 600;
      cursor: pointer;
      transition: 0.15s ease;
    }
    .btn:hover { border-color: var(--accent); color: #fff; }
    .btn.primary {
      background: linear-gradient(180deg, #2380ff, #1a5fd1);
      border: none;
      color: #fff;
      box-shadow: 0 8px 24px rgba(35, 128, 255, 0.25);
    }
    .btn.warn {
      background: linear-gradient(180deg, #f5a623, #d78900);
      border: none;
      color: #141820;
      font-weight: 700;
    }
    .btn.danger {
      background: linear-gradient(180deg, #ff4d6d, #c73a54);
      border: none; color: #fff;
    }
    .btn:disabled {
      opacity: 0.5; pointer-events: none;
    }

    main {
      display: grid;
      grid-template-columns: 320px 1fr 320px;
      gap: 12px;
      padding: 12px;
    }

    .panel {
      background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.00));
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 12px;
      padding: 12px;
      display: grid;
      gap: 12px;
    }
    .panel h2 {
      font-size: 14px; margin: 0; color: var(--muted); letter-spacing: 0.4px;
    }

    /* GRID */
    #board {
      display: grid;
      place-content: center;
    }
    .grid {
      display: grid;
      gap: 4px;
      background: transparent;
      padding: 8px;
      border-radius: 16px;
      box-shadow: 0 30px 80px rgba(0,0,0,0.35);
    }
    .tile {
      width: 48px; height: 48px;
      background: var(--tile);
      border: 1px solid rgba(255,255,255,0.06);
      border-radius: 8px;
      display: grid; place-content: center;
      position: relative;
      cursor: pointer;
      transition: 0.12s ease;
    }
    .tile:nth-child(even) { background: var(--tile-alt); }
    .tile.obstacle {
      background: var(--obstacle);
      border-color: rgba(255,255,255,0.04);
    }
    .tile:hover { filter: brightness(1.06); }
    .tile.selected { outline: 2px solid var(--accent); }
    .tile.move { background: var(--tile-move); }
    .tile.attack { background: var(--tile-attack); }
    .tile.path { box-shadow: inset 0 0 0 2px var(--tile-path); }

    /* UNIT BADGES */
    .unit {
      width: 36px; height: 36px;
      border-radius: 8px;
      display: grid; place-content: center;
      font-weight: 800;
      color: #0c111b;
      position: relative;
      box-shadow: 0 10px 22px rgba(0,0,0,0.25);
      transition: transform 0.12s ease;
    }
    .unit.player { background: linear-gradient(180deg, #66c0ff, #2d8dff); color: #061321; }
    .unit.enemy  { background: linear-gradient(180deg, #ff8f8f, #ff5b5b); color: #2b0d13; }
    .unit:after {
      content: attr(data-hp) " HP";
      position: absolute; bottom: -18px; left: 50%; transform: translateX(-50%);
      font-size: 10px; color: var(--muted);
      background: rgba(0,0,0,0.35);
      padding: 2px 6px; border-radius: 10px;
      backdrop-filter: blur(6px);
    }

    /* RIGHT PANEL */
    .statline {
      display: grid; grid-template-columns: 1fr auto;
      padding: 8px 10px;
      background: rgba(255,255,255,0.04);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 10px;
    }
    .kv { color: var(--muted); font-size: 12px; }
    .hl { font-weight: 700; }

    .actions { display: grid; gap: 8px; }
    .actions .hint { color: var(--muted); font-size: 12px; }

    /* LOG */
    #log {
      height: 160px; overflow: auto;
      padding: 8px; border-radius: 8px;
      background: rgba(10,14,24,0.5);
      border: 1px solid rgba(255,255,255,0.08);
      font-size: 13px; line-height: 1.45;
    }
    .log-item { margin: 4px 0; color: var(--muted); }
    .log-item strong { color: var(--text); }

    footer {
      padding: 8px 12px;
      color: var(--muted);
      font-size: 12px;
      display: flex; align-items: center; gap: 10px;
      border-top: 1px solid rgba(255,255,255,0.08);
    }
    .badge {
      padding: 4px 8px; border-radius: 999px;
      background: rgba(255,255,255,0.06);
      border: 1px solid rgba(255,255,255,0.08);
      color: var(--text); font-weight: 700; font-size: 11px;
    }

    /* HIGHLIGHT CURRENT TURN */
    .turn-player { color: var(--blue); font-weight: 800; }
    .turn-enemy { color: var(--red); font-weight: 800; }

    /* TOOLTIP FOR TILE COORDS */
    .tile::before {
      content: attr(data-coord);
      position: absolute; top: 4px; left: 6px;
      font-size: 10px; color: rgba(255,255,255,0.35);
    }

    /* Responsive */
    @media (max-width: 1100px) {
      main { grid-template-columns: 1fr; }
      #board { order: -1; }
    }
  </style>
</head>
<body>
<header>
  <h1>Noobs in Combat — Single‑file HTML</h1>
  <div class="controls">
    <button class="btn" id="restartBtn">Restart</button>
    <button class="btn warn" id="shuffleBtn">Shuffle Obstacles</button>
    <button class="btn primary" id="endTurnBtn">End Turn</button>
  </div>
</header>

<main>
  <section class="panel" id="left">
    <h2>Squad</h2>
    <div id="squadList"></div>
    <h2>Battle log</h2>
    <div id="log" aria-live="polite"></div>
  </section>

  <section id="board" aria-label="Battlefield">
    <div id="grid" class="grid"></div>
  </section>

  <section class="panel" id="right">
    <h2>Unit details</h2>
    <div id="details" class="statline">
      <div class="kv">Selected</div><div class="hl">None</div>
      <div class="kv">HP</div><div>—</div>
      <div class="kv">AP</div><div>—</div>
      <div class="kv">Move</div><div>—</div>
      <div class="kv">Range</div><div>—</div>
      <div class="kv">DMG</div><div>—</div>
    </div>

    <h2>Actions</h2>
    <div class="actions">
      <div class="hint" id="hint">Select a blue unit, then choose a tile to move or a red unit to attack.</div>
      <button class="btn" id="moveBtn" disabled>Move</button>
      <button class="btn danger" id="attackBtn" disabled>Attack</button>
      <button class="btn" id="waitBtn" disabled>Wait</button>
    </div>

    <h2>Turn</h2>
    <div class="statline">
      <div class="kv">Current</div><div class="hl" id="turnLabel"><span class="turn-player">Player</span></div>
      <div class="kv">Round</div><div id="roundLabel">1</div>
    </div>
  </section>
</main>

<footer>
  <span class="badge">10×10 grid</span>
  <span class="badge">Turn‑based</span>
  <span class="badge">Pathfinding, ranges, AI</span>
  <span class="badge">HTML + CSS + JS (single file)</span>
</footer>

<script>
/* =========================================================
   Noobs in Combat — Single‑file HTML
   Turn-based tactics with movement, attack ranges, simple AI
   ========================================================= */

// Config
const GRID_W = 10;
const GRID_H = 10;
const MAX_ROUNDS = 50;
const OBSTACLE_COUNT = 14;

const defaultUnits = () => ([
  // Player squad
  { id: 'P1', team: 'player', name: 'Rifle', hp: 18, maxHP: 18, ap: 2, move: 4, range: 3, dmg: 6, x: 0, y: 9 },
  { id: 'P2', team: 'player', name: 'Scout', hp: 14, maxHP: 14, ap: 2, move: 5, range: 2, dmg: 4, x: 2, y: 9 },
  { id: 'P3', team: 'player', name: 'Heavy', hp: 22, maxHP: 22, ap: 2, move: 3, range: 4, dmg: 7, x: 4, y: 9 },

  // Enemy squad
  { id: 'E1', team: 'enemy', name: 'Grunt', hp: 16, maxHP: 16, ap: 2, move: 3, range: 2, dmg: 5, x: 9, y: 0 },
  { id: 'E2', team: 'enemy', name: 'Archer', hp: 12, maxHP: 12, ap: 2, move: 3, range: 4, dmg: 4, x: 7, y: 1 },
  { id: 'E3', team: 'enemy', name: 'Brute', hp: 20, maxHP: 20, ap: 2, move: 2, range: 1, dmg: 8, x: 8, y: 2 }
]);

// State
let state = {
  round: 1,
  turn: 'player',
  units: [],
  obstacles: new Set(),
  selectedId: null,
  hover: null,
  gridEl: null,
  pathCache: new Map()
};

// DOM
const gridEl = document.getElementById('grid');
const logEl = document.getElementById('log');
const detailsEl = document.getElementById('details');
const hintEl = document.getElementById('hint');
const squadListEl = document.getElementById('squadList');
const turnLabelEl = document.getElementById('turnLabel');
const roundLabelEl = document.getElementById('roundLabel');
const endTurnBtn = document.getElementById('endTurnBtn');
const restartBtn = document.getElementById('restartBtn');
const shuffleBtn = document.getElementById('shuffleBtn');
const moveBtn = document.getElementById('moveBtn');
const attackBtn = document.getElementById('attackBtn');
const waitBtn = document.getElementById('waitBtn');

state.gridEl = gridEl;

// Init
init();

restartBtn.addEventListener('click', () => {
  init();
  writeLog('Battle restarted.');
});
shuffleBtn.addEventListener('click', () => {
  shuffleObstacles();
  render();
  writeLog('Obstacles shuffled.');
});
endTurnBtn.addEventListener('click', () => {
  if (state.turn !== 'player') return;
  endPlayerTurn();
});

moveBtn.addEventListener('click', onMoveConfirm);
attackBtn.addEventListener('click', onAttackConfirm);
waitBtn.addEventListener('click', onWait);

function init() {
  state.round = 1;
  state.turn = 'player';
  state.units = defaultUnits().map(u => ({...u, ap: 2}));
  state.selectedId = null;
  state.obstacles = generateObstacles(OBSTACLE_COUNT);
  state.pathCache.clear();
  buildGrid(GRID_W, GRID_H);
  render();
  writeLog('Deploy: Blue vs Red. Your move.');
}

/* ------------------------------
   Grid creation and rendering
------------------------------ */
function buildGrid(w, h) {
  gridEl.style.gridTemplateColumns = `repeat(${w}, 48px)`;
  gridEl.style.gridTemplateRows = `repeat(${h}, 48px)`;
  gridEl.innerHTML = '';
  for (let y = 0; y < h; y++) {
    for (let x = 0; x < w; x++) {
      const tile = document.createElement('button');
      tile.className = 'tile';
      tile.dataset.x = x;
      tile.dataset.y = y;
      tile.dataset.coord = `${x},${y}`;
      tile.setAttribute('aria-label', `Tile ${x},${y}`);
      gridEl.appendChild(tile);
    }
  }
  gridEl.addEventListener('click', onGridClick);
  gridEl.addEventListener('mousemove', onGridHover);
}

function render() {
  // Clear tile classes
  for (const tile of gridEl.children) {
    tile.className = 'tile';
    const x = +tile.dataset.x, y = +tile.dataset.y;
    if (isObstacle(x, y)) tile.classList.add('obstacle');
  }

  // Draw ranges for selected
  const sel = selectedUnit();
  if (sel && sel.team === state.turn) {
    const moves = reachableTiles(sel);
    const attacks = attackableTiles(sel);
    for (const {x,y} of moves) tileAt(x,y)?.classList.add('move');
    for (const {x,y} of attacks) tileAt(x,y)?.classList.add('attack');
    const t = tileAt(sel.x, sel.y);
    t?.classList.add('selected');
  }

  // Units
  for (const u of state.units) {
    const t = tileAt(u.x, u.y);
    if (!t) continue;
    const badge = document.createElement('div');
    badge.className = `unit ${u.team === 'player' ? 'player' : 'enemy'}`;
    badge.dataset.hp = u.hp;
    badge.textContent = u.name[0];
    t.appendChild(badge);
  }

  // Squad list
  renderSquad();

  // Details
  renderDetails();

  // Turn label
  turnLabelEl.innerHTML = state.turn === 'player'
    ? '<span class="turn-player">Player</span>'
    : '<span class="turn-enemy">Enemy</span>';
  roundLabelEl.textContent = String(state.round);

  // Actions availability
  const canAct = !!sel && sel.team === 'player' && sel.ap > 0 && state.turn === 'player';
  moveBtn.disabled = !canAct;
  attackBtn.disabled = !canAct;
  waitBtn.disabled = !canAct;

  checkVictory();
}

function renderDetails() {
  const sel = selectedUnit();
  const pairs = [
    ['Selected', sel ? `${sel.name} (${sel.id})` : 'None'],
    ['HP', sel ? `${sel.hp}/${sel.maxHP}` : '—'],
    ['AP', sel ? String(sel.ap) : '—'],
    ['Move', sel ? String(sel.move) : '—'],
    ['Range', sel ? String(sel.range) : '—'],
    ['DMG', sel ? String(sel.dmg) : '—']
  ];
  detailsEl.innerHTML = pairs.map(([k,v]) =>
    `<div class="kv">${k}</div><div>${v}</div>`
  ).join('');
}

function renderSquad() {
  const teamSort = [...state.units].sort((a,b) => (a.team > b.team) ? 1 : (a.team < b.team) ? -1 : 0);
  squadListEl.innerHTML = teamSort.map(u => `
    <div class="statline" data-id="${u.id}">
      <div class="kv">${u.team === 'player' ? 'Blue' : 'Red'}</div><div class="hl">${u.name}</div>
      <div class="kv">HP</div><div>${u.hp}/${u.maxHP}</div>
      <div class="kv">AP</div><div>${u.ap}</div>
      <div class="kv">Pos</div><div>${u.x},${u.y}</div>
    </div>
  `).join('');
  squadListEl.querySelectorAll('.statline').forEach(el => {
    el.addEventListener('click', () => {
      const id = el.dataset.id;
      selectUnit(id);
    });
  });
}

/* ------------------------------
   Interaction
------------------------------ */
function onGridClick(e) {
  const t = e.target.closest('.tile');
  if (!t) return;
  const x = +t.dataset.x, y = +t.dataset.y;
  const unitHere = unitAt(x, y);
  const sel = selectedUnit();

  if (unitHere) {
    selectUnit(unitHere.id);
    hintEl.textContent = unitHere.team === 'player'
      ? 'Choose a blue unit; then pick a tile to move or an enemy to attack.'
      : 'Enemy selected. You can target it if any selected unit is in range.';
    render();
    return;
  }

  if (!sel || state.turn !== sel.team) {
    hintEl.textContent = 'Select a blue unit first.';
    return;
  }

  // Attempt move if tile is reachable and AP > 0
  const moves = reachableTiles(sel);
  if (sel.ap > 0 && moves.some(m => m.x === x && m.y === y)) {
    const path = findPath(sel.x, sel.y, x, y);
    animateMove(sel, path);
    sel.ap -= 1;
    writeLog(`Move: ${sel.id} → (${x},${y})`);
    render();
    return;
  }

  hintEl.textContent = 'Tile not reachable this turn.';
}

function onGridHover(e) {
  const t = e.target.closest('.tile');
  if (!t) { state.hover = null; return; }
  state.hover = { x: +t.dataset.x, y: +t.dataset.y };
  const sel = selectedUnit();
  if (!sel) return;

  // Preview path
  for (const tile of gridEl.children) tile.classList.remove('path');
  const moves = reachableTiles(sel);
  if (moves.some(m => m.x === state.hover.x && m.y === state.hover.y)) {
    const path = findPath(sel.x, sel.y, state.hover.x, state.hover.y);
    for (const p of path) tileAt(p.x, p.y)?.classList.add('path');
  }
}

function selectUnit(id) {
  state.selectedId = id;
  render();
}

function onMoveConfirm() {
  const sel = selectedUnit();
  if (!sel || sel.team !== 'player' || sel.ap <= 0) return;
  hintEl.textContent = 'Click a highlighted tile to move.';
}

function onAttackConfirm() {
  const sel = selectedUnit();
  if (!sel || sel.team !== 'player' || sel.ap <= 0) return;
  hintEl.textContent = 'Click an enemy unit within red tiles to attack.';
  // Make attack if next click is on enemy in range
  gridEl.once = true;
  const clickOnce = (e) => {
    gridEl.removeEventListener('click', clickOnce);
    const t = e.target.closest('.tile');
    if (!t) return;
    const x = +t.dataset.x, y = +t.dataset.y;
    const target = unitAt(x,y);
    if (!target || target.team === 'player') return;
    const inRange = manhattan(sel.x, sel.y, target.x, target.y) <= sel.range && lineOfSight(sel, target);
    if (inRange) {
      applyAttack(sel, target);
      sel.ap -= 1;
      render();
    } else {
      hintEl.textContent = 'Target not in line-of-sight or range.';
    }
  };
  gridEl.addEventListener('click', clickOnce);
}

function onWait() {
  const sel = selectedUnit();
  if (!sel || sel.team !== 'player') return;
  sel.ap = 0;
  writeLog(`Wait: ${sel.id} holds position.`);
  render();
}

function endPlayerTurn() {
  // Exhaust remaining AP of player units
  for (const u of state.units.filter(u => u.team === 'player')) {
    if (u.ap > 0) u.ap = 0;
  }
  state.turn = 'enemy';
  render();
  enemyTurn().then(() => {
    // New round
    state.round += 1;
    for (const u of state.units) u.ap = 2;
    state.turn = 'player';
    render();
    writeLog(`Round ${state.round} — Your move.`);
  });
}

/* ------------------------------
   Combat core
------------------------------ */
function applyAttack(attacker, target) {
  const dmg = attacker.dmg;
  target.hp = Math.max(0, target.hp - dmg);
  writeLog(`Attack: ${attacker.id} hits ${target.id} for ${dmg}.`);
  if (target.hp <= 0) {
    writeLog(`KO: ${target.id} is eliminated.`);
    // remove target
    state.units = state.units.filter(u => u !== target);
    if (state.selectedId === target.id) state.selectedId = null;
  }
}

function checkVictory() {
  const players = state.units.filter(u => u.team === 'player');
  const enemies = state.units.filter(u => u.team === 'enemy');
  if (players.length === 0) {
    hintEl.textContent = 'Defeat. Restart to try again.';
    endTurnBtn.disabled = true;
    moveBtn.disabled = true;
    attackBtn.disabled = true;
    waitBtn.disabled = true;
    writeLog('Battle over — Red wins.');
  } else if (enemies.length === 0) {
    hintEl.textContent = 'Victory! All enemies defeated.';
    endTurnBtn.disabled = true;
    moveBtn.disabled = true;
    attackBtn.disabled = true;
    waitBtn.disabled = true;
    writeLog('Battle over — Blue wins.');
  } else if (state.round > MAX_ROUNDS) {
    hintEl.textContent = 'Stalemate reached.';
    writeLog('Stalemate — maximum rounds exceeded.');
  }
}

/* ------------------------------
   Ranges, pathfinding, LOS
------------------------------ */
function reachableTiles(unit) {
  const maxSteps = unit.move;
  const start = { x: unit.x, y: unit.y };
  const visited = new Set([key(start.x, start.y)]);
  const q = [{...start, d: 0}];
  const tiles = [];

  while (q.length) {
    const cur = q.shift();
    if (cur.d > 0) tiles.push({ x: cur.x, y: cur.y });
    if (cur.d === maxSteps) continue;
    for (const [nx, ny] of neighbors(cur.x, cur.y)) {
      if (!inBounds(nx, ny)) continue;
      const k = key(nx, ny);
      if (visited.has(k)) continue;
      // cannot pass through obstacles or units (except start)
      if (!(nx === start.x && ny === start.y) && (isObstacle(nx, ny) || unitAt(nx, ny))) continue;
      visited.add(k);
      q.push({ x: nx, y: ny, d: cur.d + 1 });
    }
  }
  return tiles;
}

function attackableTiles(unit) {
  const tiles = [];
  for (let y = 0; y < GRID_H; y++) {
    for (let x = 0; x < GRID_W; x++) {
      const dist = manhattan(unit.x, unit.y, x, y);
      if (dist <= unit.range) {
        const target = unitAt(x, y);
        if (target && target.team !== unit.team && lineOfSight(unit, target)) {
          tiles.push({x, y});
        }
      }
    }
  }
  return tiles;
}

function lineOfSight(a, b) {
  // Bresenham-like LOS: block if obstacle strictly between centers
  const cells = rasterLine(a.x, a.y, b.x, b.y);
  for (const c of cells) {
    if ((c.x === a.x && c.y === a.y) || (c.x === b.x && c.y === b.y)) continue;
    if (isObstacle(c.x, c.y)) return false;
  }
  return true;
}

function rasterLine(x0, y0, x1, y1) {
  const cells = [];
  const dx = Math.abs(x1 - x0), dy = Math.abs(y1 - y0);
  const sx = x0 < x1 ? 1 : -1;
  const sy = y0 < y1 ? 1 : -1;
  let err = dx - dy;
  let x = x0, y = y0;
  while (true) {
    cells.push({x, y});
    if (x === x1 && y === y1) break;
    const e2 = 2 * err;
    if (e2 > -dy) { err -= dy; x += sx; }
    if (e2 < dx) { err += dx; y += sy; }
  }
  return cells;
}

function findPath(sx, sy, tx, ty) {
  const cacheKey = `${sx},${sy}->${tx},${ty}`;
  if (state.pathCache.has(cacheKey)) return state.pathCache.get(cacheKey);

  const start = { x: sx, y: sy };
  const target = { x: tx, y: ty };
  const open = [start];
  const came = new Map();
  const g = new Map([[key(sx, sy), 0]]);
  const h = (x,y) => Math.abs(x - tx) + Math.abs(y - ty);

  function f(x,y) { return (g.get(key(x,y)) ?? Infinity) + h(x,y); }

  while (open.length) {
    // pick lowest f
    open.sort((A,B) => f(A.x,A.y) - f(B.x,B.y));
    const cur = open.shift();
    if (!cur) break;
    if (cur.x === tx && cur.y === ty) break;

    for (const [nx, ny] of neighbors(cur.x, cur.y)) {
      if (!inBounds(nx, ny)) continue;
      if (isObstacle(nx, ny) || unitAt(nx, ny)) continue;
      const nk = key(nx, ny), ck = key(cur.x, cur.y);
      const tentativeG = (g.get(ck) ?? Infinity) + 1;
      if (tentativeG < (g.get(nk) ?? Infinity)) {
        came.set(nk, ck);
        g.set(nk, tentativeG);
        if (!open.some(o => o.x === nx && o.y === ny)) open.push({x:nx,y:ny});
      }
    }
  }

  const path = [];
  let curKey = key(tx, ty);
  if (!came.has(curKey) && !(sx === tx && sy === ty)) return [];
  while (curKey) {
    const [cx, cy] = curKey.split(',').map(Number);
    path.unshift({x: cx, y: cy});
    curKey = came.get(curKey);
  }
  state.pathCache.set(cacheKey, path);
  return path;
}

function animateMove(unit, path) {
  if (!path.length) return;
  // Skip first node if it's current position
  let idx = 0;
  if (path[0].x === unit.x && path[0].y === unit.y) idx = 1;
  const step = () => {
    if (idx >= path.length) return render();
    const p = path[idx++];
    unit.x = p.x; unit.y = p.y;
    render();
    if (idx < path.length) setTimeout(step, 60);
  };
  step();
}

/* ------------------------------
   Enemy AI
------------------------------ */
async function enemyTurn() {
  writeLog('Enemy turn begins.');
  const enemies = state.units.filter(u => u.team === 'enemy');
  for (const e of enemies) {
    if (state.units.filter(u => u.team === 'player').length === 0) break;

    // Try attack if any player is in range and LOS
    const playerTargets = state.units.filter(u => u.team === 'player');
    let target = playerTargets
      .filter(p => manhattan(e.x, e.y, p.x, p.y) <= e.range && lineOfSight(e, p))
      .sort((a,b) => a.hp - b.hp)[0];

    if (target && e.ap > 0) {
      await delay(250);
      applyAttack(e, target);
      e.ap -= 1;
      render();
    }

    // Move toward nearest player if AP remains
    if (e.ap > 0) {
      const nearest = playerTargets.sort((a,b) =>
        manhattan(e.x, e.y, a.x, a.y) - manhattan(e.x, e.y, b.x, b.y)
      )[0];
      if (nearest) {
        const best = bestApproachTile(e, nearest);
        if (best) {
          const path = findPath(e.x, e.y, best.x, best.y).slice(0, e.move);
          await delay(200);
          animateMove(e, path);
          e.ap -= 1;
        }
      }
    }

    // Second attack attempt after move
    target = state.units
      .filter(u => u.team === 'player')
      .filter(p => manhattan(e.x, e.y, p.x, p.y) <= e.range && lineOfSight(e, p))
      .sort((a,b) => a.hp - b.hp)[0];

    if (target && e.ap > 0) {
      await delay(250);
      applyAttack(e, target);
      e.ap -= 1;
      render();
    }

    // Wait if nothing else
    if (e.ap > 0) {
      e.ap = 0;
      writeLog(`Enemy ${e.id} waits.`);
    }

    await delay(150);
  }
  writeLog('Enemy turn ends.');
}

/* ------------------------------
   Helpers
------------------------------ */
function tileAt(x, y) {
  return [...gridEl.children].find(el => +el.dataset.x === x && +el.dataset.y === y) || null;
}
function unitAt(x, y) {
  return state.units.find(u => u.x === x && u.y === y) || null;
}
function selectedUnit() {
  return state.units.find(u => u.id === state.selectedId) || null;
}
function neighbors(x, y) {
  return [[x+1,y],[x-1,y],[x,y+1],[x,y-1]];
}
function key(x,y){ return `${x},${y}`; }
function inBounds(x,y){ return x>=0 && y>=0 && x<GRID_W && y<GRID_H; }
function manhattan(x1,y1,x2,y2){ return Math.abs(x1-x2)+Math.abs(y1-y2); }
function delay(ms){ return new Promise(r => setTimeout(r, ms)); }

function writeLog(msg) {
  const div = document.createElement('div');
  div.className = 'log-item';
  const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
  div.innerHTML = `<strong>${time}</strong> — ${msg}`;
  logEl.appendChild(div);
  logEl.scrollTop = logEl.scrollHeight;
}

function generateObstacles(count) {
  const set = new Set();
  let attempts = 0;
  while (set.size < count && attempts < 1000) {
    attempts++;
    const x = Math.floor(Math.random() * GRID_W);
    const y = Math.floor(Math.random() * GRID_H);
    // Avoid starting areas
    const nearPlayerStart = y > GRID_H - 3;
    const nearEnemyStart = y < 3;
    if (nearPlayerStart || nearEnemyStart) continue;
    // Avoid unit cells
    if (defaultUnits().some(u => u.x === x && u.y === y)) continue;
    set.add(key(x,y));
  }
  return set;
}

function shuffleObstacles() {
  state.obstacles = generateObstacles(OBSTACLE_COUNT);
}

function isObstacle(x,y) { return state.obstacles.has(key(x,y)); }

function bestApproachTile(from, target) {
  // Pick a reachable tile that reduces distance, prefers LOS
  const moves = reachableTiles(from);
  const sorted = moves
    .map(m => {
      const dist = manhattan(m.x, m.y, target.x, target.y);
      const los = lineOfSight({x:m.x,y:m.y}, target) ? 1 : 0;
      const score = dist - los * 0.5;
      return { ...m, score };
    })
    .sort((a,b) => a.score - b.score);
  return sorted[0] || null;
}

/* ------------------------------
   Accessibility: one-time listener helper
------------------------------ */
Object.defineProperty(Element.prototype, 'once', {
  set(value) {
    // no-op (placeholder for intent marking)
  }
});
</script>
</body>
</html>
```
