<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>X and O — Chaos Deck Deluxe</title>
<style>
  :root{
    --bg:#0b0e14; --text:#e6e6e6; --muted:#a7b0c0;
    --panel:#151822; --panelSoft:rgba(21,24,34,.75);
    --border:rgba(122,162,247,.28);
    --accent:#7aa2f7; --accent2:#c28fff;
    --x:#7aa2f7; --o:#c9d27e; --p3:#ff9f6f; --p4:#6ae3bd;
    --win:#73d13d; --danger:#f7768e; --gold:#ffce63;
    --shadow:0 12px 36px rgba(0,0,0,.5);
    --rar-common:#9aa0aa; --rar-rare:#7aa2f7; --rar-epic:#c28fff; --rar-legend:#ffce63; --rar-myth:#f7768e;
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0; color:var(--text);
    font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
    background:
      radial-gradient(1200px 600px at 65% -10%, #14203a 0%, var(--bg) 60%) fixed,
      radial-gradient(900px 420px at 12% 120%, #1a1f35 0%, transparent 65%) fixed;
    display:grid; grid-template-rows:auto 1fr auto;
  }

  /* Topbar */
  .topbar{
    display:flex; align-items:center; gap:12px;
    padding:14px 18px; backdrop-filter: blur(10px);
    background: linear-gradient(0deg, rgba(16,18,28,.55), rgba(16,18,28,.78));
    border-bottom: 1px solid rgba(122,162,247,.18); box-shadow: var(--shadow);
  }
  .brand{
    font-weight:800; letter-spacing:.2px;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    -webkit-background-clip:text; background-clip:text; color:transparent; user-select:none;
  }
  .spacer{flex:1}
  .btn{
    border:1px solid var(--border);
    background: linear-gradient(180deg, #111423, #0d1120);
    color:var(--text); padding:10px 14px; border-radius:12px; cursor:pointer;
    transition: transform .08s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
    box-shadow: 0 6px 16px rgba(122,162,247,.12);
  }
  .btn:hover{ border-color: rgba(122,162,247,.38); background: linear-gradient(180deg, #14182a, #0f1325); box-shadow: 0 10px 24px rgba(122,162,247,.22); transform: translateY(-1px) }
  .btn:disabled{ opacity:.4; cursor:not-allowed }

  /* Layout */
  .wrap{ display:grid; justify-items:center; align-content:start; gap:16px; padding: 24px 20px 28px; }
  .row{ display:flex; gap:12px; align-items:center; flex-wrap:wrap }
  .status{
    font-size:14px; color:var(--muted);
    border:1px solid rgba(255,255,255,.10); border-radius:999px;
    background: rgba(255,255,255,.06);
    padding:6px 10px; user-select:none;
    transition: box-shadow .25s ease, border-color .25s ease;
  }
  .chip{
    display:inline-flex; align-items:center; gap:7px;
    font-size:12px; color:var(--muted);
    border:1px solid rgba(255,255,255,.10);
    background: rgba(255,255,255,.06);
    padding:4px 10px; border-radius:999px; user-select:none;
  }
  .dot{ width:10px; height:10px; border-radius:50%; box-shadow: 0 0 10px currentColor }
  .dot.x{ color:var(--x); background: var(--x) }
  .dot.o{ color:var(--o); background: var(--o) }
  .dot.p3{ color:var(--p3); background: var(--p3) }
  .dot.p4{ color:var(--p4); background: var(--p4) }

  /* Board */
  .board{
    position:relative; display:grid; gap:12px; padding:16px;
    border-radius: 18px; border: 1px solid var(--border);
    background: var(--panelSoft); backdrop-filter: blur(8px); box-shadow: var(--shadow);
  }
  .cell{
    position:relative; display:grid; place-items:center;
    font-size: clamp(28px, 6vw, 42px); font-weight:800;
    border: 1px solid rgba(122,162,247,.22); border-radius: 14px;
    background: linear-gradient(180deg, #14182a, #101423); color: var(--text);
    cursor: pointer; user-select:none;
    transition: transform .12s ease, border-color .15s ease, box-shadow .18s ease, background .22s ease, color .2s ease, filter .2s ease;
    overflow:hidden;
  }
  .cell:hover{ transform: translateY(-2px); border-color: rgba(122,162,247,.42); box-shadow: 0 12px 24px rgba(122,162,247,.12); background: linear-gradient(180deg, #16203a, #111423) }
  .cell.played{ animation: pop .18s cubic-bezier(.2,.7,.2,1); filter: drop-shadow(0 6px 10px rgba(0,0,0,.25)) }
  @keyframes pop{ 0%{ transform: scale(.85) } 100%{ transform: scale(1) } }
  .glyph{ position:relative; display:inline-block }
  .glyph.x{ color: var(--x); text-shadow: 0 0 12px rgba(122,162,247,.45) }
  .glyph.o{ color: var(--o); text-shadow: 0 0 12px rgba(201,210,126,.45) }
  .glyph.p3{ color: var(--p3); text-shadow: 0 0 12px rgba(255,159,111,.45) }
  .glyph.p4{ color: var(--p4); text-shadow: 0 0 12px rgba(106,227,189,.45) }

  .win{ outline: 2px solid var(--win); box-shadow: 0 0 0 2px rgba(115,209,61,.25) inset, 0 0 22px rgba(115,209,61,.35); animation: winPulse 1.4s ease-in-out infinite }
  @keyframes winPulse{ 0%{ filter: brightness(1) } 50%{ filter: brightness(1.25) } 100%{ filter: brightness(1) } }
  .ribbon{
    position:absolute; inset:-2px; border-radius: 18px; pointer-events:none;
    background: conic-gradient(from 0deg, rgba(122,162,247,.18) 0 150deg, rgba(194,143,255,.18) 150deg 270deg, rgba(106,227,189,.18) 270deg 360deg);
    mix-blend-mode: screen; opacity:0; transition: opacity .3s ease;
  }
  .board.win .ribbon{ opacity:.75; animation: spin 4s linear infinite }
  @keyframes spin{ to{ transform: rotate(360deg) } }

  .bar{ display:flex; gap:10px; align-items:center; flex-wrap:wrap }
  .score{
    display:inline-flex; gap:14px; align-items:center; color:var(--muted); font-size:13px;
    border:1px solid rgba(255,255,255,.10); background: rgba(255,255,255,.06);
    padding:6px 10px; border-radius:999px;
  }
  .score .x{ color:var(--x); font-weight:600 }
  .score .o{ color:var(--o); font-weight:600 }
  .score .p3{ color:var(--p3); font-weight:600 }
  .score .p4{ color:var(--p4); font-weight:600 }
  .score .d{ color:#c7cbd6; font-weight:600 }

  /* Cards tray (private per current player) */
  .cardsTray{
    position:fixed; left:50%; transform:translateX(-50%);
    bottom:16px; display:flex; gap:10px; padding:10px 12px;
    border:1px solid rgba(255,255,255,.12); background: rgba(16,20,32,.86);
    border-radius: 14px; backdrop-filter: blur(8px); box-shadow: var(--shadow);
    min-height: 120px;
  }
  .cardItem{
    width:78px; height:112px; border-radius:12px; border:1px solid rgba(255,255,255,.14);
    background: linear-gradient(180deg, #1b2033, #121626); color:#fff; display:grid; place-items:center;
    cursor:pointer; transition: transform .15s ease, box-shadow .2s ease; position:relative; padding:6px;
  }
  .cardItem:hover{ transform:translateY(-6px); box-shadow:0 12px 28px rgba(0,0,0,.45) }
  .cardName{ font-size:12px; text-align:center }
  .rarBar{ position:absolute; inset:auto 0 0 0; height:6px; border-radius:0 0 12px 12px }
  .rar-common{ background: linear-gradient(90deg, var(--rar-common), #8a8f99) }
  .rar-rare{ background: linear-gradient(90deg, var(--rar-rare), #87b0ff) }
  .rar-epic{ background: linear-gradient(90deg, var(--rar-epic), #dea9ff) }
  .rar-legend{ background: linear-gradient(90deg, var(--rar-legend), #ffe18e) }
  .rar-myth{ background: linear-gradient(90deg, var(--rar-myth), #ff9aa7) }
  .cardTip{
    position:absolute; bottom:118%; left:50%; transform:translateX(-50%);
    white-space:normal; width:220px; font-size:11px; color:var(--muted);
    border:1px solid rgba(255,255,255,.14); background: rgba(16,20,32,.95);
    padding:8px 10px; border-radius:8px; box-shadow: var(--shadow); opacity:0; pointer-events:none;
    transition: opacity .15s ease;
  }
  .cardItem:hover .cardTip{ opacity:1 }

  /* Card effect overlay */
  .effectOverlay{
    position:fixed; inset:0; display:grid; place-items:center; z-index:1002;
    pointer-events:none;
  }
  .effectBubble{
    display:grid; gap:8px; justify-items:center;
    padding:14px 18px; border-radius:12px; color:#fff;
    border:1px solid rgba(255,255,255,.2);
    background: radial-gradient(240px 120px at 50% 40%, rgba(20,24,34,.96), rgba(16,20,32,.92));
    box-shadow: 0 14px 40px rgba(0,0,0,.6);
    animation: effectPop .18s ease-out;
  }
  @keyframes effectPop{ 0%{ transform:scale(.92); opacity:.0 } 100%{ transform:scale(1); opacity:1 } }
  .effectTitle{ font-weight:700 }
  .effectDesc{ font-size:12px; color:var(--muted); text-align:center; max-width:320px }

  /* Modals and loading reused */
  .loading{
    position:fixed; inset:0; display:grid; place-items:center;
    background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.88), rgba(10,12,18,.95));
    backdrop-filter: blur(6px); z-index:1000; opacity:0; pointer-events:none; transition: opacity .28s ease;
  }
  .loading.active{ opacity:1; pointer-events:auto }
  .loader{
    display:grid; gap:12px; justify-items:center;
    padding: 24px 26px; border: 1px solid rgba(122,162,247,.28);
    border-radius: 16px; background: linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88));
    box-shadow: var(--shadow);
  }
  .ring{
    width:62px; height:62px; border-radius:50%;
    background: conic-gradient(from 0deg, var(--accent) 0% 30%, var(--accent2) 30% 60%, #6ae3bd 60% 85%, transparent 85% 100%);
    -webkit-mask: radial-gradient(closest-side, transparent 66%, black 67%); mask: radial-gradient(closest-side, transparent 66%, black 67%);
    animation: spin .9s linear infinite;
  }
  .progress{ width:240px; height:8px; border-radius:999px; border:1px solid rgba(255,255,255,.1); background: rgba(255,255,255,.06); overflow:hidden }
  .bar{ height:100%; background: linear-gradient(90deg, var(--accent), var(--accent2)); width:0%; transition: width .28s ease }
  .loader-label{ font-size:12px; color:var(--muted) }

  .modal{
    position:fixed; inset:0; display:grid; place-items:center;
    background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.86), rgba(10,12,18,.92));
    backdrop-filter: blur(6px); z-index:1001; opacity:0; pointer-events:none; transition: opacity .28s ease;
  }
  .modal.active{ opacity:1; pointer-events:auto }
  .sheet{
    width:min(760px, 92vw); display:grid; gap:14px;
    padding: 18px 18px 20px; border: 1px solid rgba(122,162,247,.28);
    border-radius: 14px; background: linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88));
    box-shadow: var(--shadow);
  }
  .sheet h3{ margin:0; font-size:18px }
  .group{ display:grid; gap:8px }
  .row-flex{ display:flex; gap:12px; align-items:center; flex-wrap:wrap }
  .select,.range{
    width:100%; background:#101423; border:1px solid rgba(255,255,255,.12); color:var(--text);
    border-radius:10px; padding:8px 10px;
  }
  .range{ accent-color: var(--accent) }
  .pill{
    display:inline-flex; align-items:center; gap:8px; padding:6px 10px; border-radius:999px;
    border:1px solid rgba(255,255,255,.12); background: rgba(255,255,255,.06); color:var(--muted); font-size:12px;
  }
</style>
</head>
<body>
  <header class="topbar">
    <div class="brand">X and O</div>
    <div class="spacer"></div>
    <button class="btn" id="back">Back</button>
    <button class="btn" id="refresh">Refresh</button>
    <button class="btn" id="setup">Setup</button>
  </header>

  <section class="wrap">
    <div class="row">
      <div class="status" id="status">Waiting for setup…</div>
      <span class="chip"><span class="dot x"></span>X</span>
      <span class="chip"><span class="dot o"></span>O</span>
      <span class="chip"><span class="dot p3"></span>■</span>
      <span class="chip"><span class="dot p4"></span>▲</span>
    </div>

    <div class="board" id="board" aria-label="Tic-tac-toe board">
      <div class="ribbon" aria-hidden="true"></div>
    </div>

    <div class="bar">
      <button class="btn" id="reset">Reset</button>
      <div class="score">
        <span class="x">X:</span><span id="scoreX">0</span>
        <span class="o">O:</span><span id="scoreO">0</span>
        <span class="p3">■:</span><span id="scoreP3">0</span>
        <span class="p4">▲:</span><span id="scoreP4">0</span>
        <span class="d">Draws:</span><span id="scoreD">0</span>
      </div>
      <div class="score" id="timerBox" style="display:none">
        <span class="d">Timer:</span><span id="timeLeft">0</span>s
      </div>
    </div>
  </section>

  <!-- Private cards tray (only current player's cards are shown) -->
  <div class="cardsTray" id="cardsTray" style="display:none"></div>

  <!-- Effect overlay -->
  <div class="effectOverlay" id="effectOverlay" style="display:none">
    <div class="effectBubble" id="effectBubble">
      <div class="effectTitle" id="effectTitle"></div>
      <div class="effectDesc" id="effectDesc"></div>
    </div>
  </div>

  <!-- Loading overlay -->
  <div id="loading" class="loading" aria-live="polite" aria-busy="true">
    <div class="loader">
      <div class="ring" aria-hidden="true"></div>
      <div class="progress"><div class="bar" id="bar"></div></div>
      <div class="loader-label" id="loaderLabel">Spinning up board…</div>
    </div>
  </div>

  <!-- Setup modal -->
  <div id="modal" class="modal" aria-modal="true" role="dialog">
    <div class="sheet">
      <h3>Game setup</h3>

      <div class="group">
        <div class="pill">Opponent</div>
        <div class="row-flex">
          <select class="select" id="opponent">
            <option value="player">Local player</option>
            <option value="bot">Bot</option>
          </select>
          <div id="botRow" class="row-flex" style="display:none">
            <div class="pill">Difficulty (1–5, decimals ok)</div>
            <input class="range" id="difficulty" type="range" min="1" max="5" step="0.1" value="2.5" />
            <div class="pill"><span id="difficultyLabel">2.5</span></div>
          </div>
        </div>
      </div>

      <div class="group">
        <div class="pill">Gamemode</div>
        <select class="select" id="mode">
          <option value="casual">Casual (normal tic‑tac‑toe)</option>
          <option value="three">Triad Clash (three players)</option>
          <option value="four">Quad Arena (four players)</option>
          <option value="blitz">Blitz (turn timer)</option>
          <option value="randomizer">Chaos Deck (cards & random events)</option>
          <option value="mine">Minefield (hidden mines + eliminations)</option>
        </select>
      </div>

      <div class="group">
        <div class="pill">Players</div>
        <div class="row-flex">
          <div id="playersRow" class="row-flex">
            <select class="select" id="playerCount">
              <option value="2">2 players</option>
              <option value="3">3 players</option>
              <option value="4">4 players</option>
            </select>
          </div>
          <div id="blitzRow" class="row-flex" style="display:none">
            <div class="pill">Blitz timer (5–20s)</div>
            <input class="range" id="blitzSec" type="range" min="5" max="20" step="1" value="10" />
            <div class="pill"><span id="blitzLabel">10s</span></div>
          </div>
        </div>
      </div>

      <div class="row-flex" style="justify-content:flex-end">
        <button class="btn" id="cancelSetup">Cancel</button>
        <button class="btn" id="applySetup">Start</button>
      </div>
    </div>
  </div>

<script>
  // Elements
  const boardEl = document.getElementById('board');
  const statusEl = document.getElementById('status');
  const resetBtn = document.getElementById('reset');
  const backBtn = document.getElementById('back');
  const refreshBtn = document.getElementById('refresh');
  const setupBtn = document.getElementById('setup');

  const loading = document.getElementById('loading');
  const bar = document.getElementById('bar');
  const label = document.getElementById('loaderLabel');

  const modal = document.getElementById('modal');
  const opponentSel = document.getElementById('opponent');
  const botRow = document.getElementById('botRow');
  const difficultyRange = document.getElementById('difficulty');
  const difficultyLabel = document.getElementById('difficultyLabel');

  const modeSel = document.getElementById('mode');
  const playersRow = document.getElementById('playersRow');
  const playerCountSel = document.getElementById('playerCount');

  const blitzRow = document.getElementById('blitzRow');
  const blitzRange = document.getElementById('blitzSec');
  const blitzLabel = document.getElementById('blitzLabel');

  const cancelSetup = document.getElementById('cancelSetup');
  const applySetup = document.getElementById('applySetup');

  const timerBox = document.getElementById('timerBox');
  const timeLeftEl = document.getElementById('timeLeft');

  const cardsTray = document.getElementById('cardsTray');

  const effectOverlay = document.getElementById('effectOverlay');
  const effectTitle = document.getElementById('effectTitle');
  const effectDesc = document.getElementById('effectDesc');

  // State
  let config = {
    opponent: 'player',
    difficulty: 2.5,
    mode: 'casual',
    players: 2,
    blitzSec: 10
  };
  let state, scores = { X:0, O:0, P3:0, P4:0, D:0 };
  let timer = null;
  let baseTimer = 10;
  let eliminated = new Set(); // players removed (minefield)

  // Loader
  function showLoader(flag){ loading.classList.toggle('active', !!flag) }
  function animateProgress(ms=900){
    bar.style.width='0%'; label.textContent='Spinning up board…';
    let p=0;
    const step=()=>{
      p=Math.min(100, p + Math.random()*24 + 6);
      bar.style.width = p.toFixed(0)+'%';
      if(p<40) label.textContent='Laying out grid…';
      else if(p<70) label.textContent='Charging glow…';
      else if(p<90) label.textContent='Binding events…';
      else label.textContent='Ready to play!';
      if(p<100) requestAnimationFrame(step);
    };
    requestAnimationFrame(step);
    return new Promise(res=>setTimeout(res, ms));
  }

  // Modal behavior (conditional fields)
  function openSetup(){ modal.classList.add('active'); updateSetupLabels(); applyFieldVisibility(); }
  function closeSetup(){ modal.classList.remove('active'); }
  function updateSetupLabels(){
    difficultyLabel.textContent = difficultyRange.value;
    blitzLabel.textContent = blitzRange.value + 's';
  }
  function applyFieldVisibility(){
    botRow.style.display = opponentSel.value==='bot' ? 'flex' : 'none';
    blitzRow.style.display = modeSel.value==='blitz' ? 'flex' : 'none';
    playersRow.style.display = (modeSel.value==='three' || modeSel.value==='four') ? 'none' : 'flex';
    if(modeSel.value==='three') playerCountSel.value = '3';
    if(modeSel.value==='four') playerCountSel.value = '4';
  }
  opponentSel.addEventListener('change', applyFieldVisibility);
  difficultyRange.addEventListener('input', updateSetupLabels);
  blitzRange.addEventListener('input', updateSetupLabels);
  modeSel.addEventListener('change', applyFieldVisibility);
  cancelSetup.addEventListener('click', closeSetup);
  applySetup.addEventListener('click', async ()=>{
    config.opponent = opponentSel.value;
    config.difficulty = parseFloat(difficultyRange.value);
    config.mode = modeSel.value;
    config.players = parseInt((modeSel.value==='three'||modeSel.value==='four') ? (modeSel.value==='three'? '3':'4') : playerCountSel.value, 10);
    config.blitzSec = parseInt(blitzRange.value, 10);
    closeSetup();
    showLoader(true); await animateProgress(800); showLoader(false);
    initGame(true);
  });

  function getBoardSize(){
    // Randomizer uses larger grid; 3p -> 4x4, 4p -> 5x5
    if (config.mode === 'randomizer') return 5;
    if (config.mode === 'three' || config.players === 3) return 4;
    if (config.mode === 'four' || config.players === 4) return 5;
    return 3;
  }
  function getWinLength(){
    const size = getBoardSize();
    return size >= 4 ? 4 : 3;
  }
  function mineCount(){
    if (config.mode!=='mine') return 0;
    return config.players === 4 ? 4 : config.players === 3 ? 3 : 2;
  }

  function initGame(fromSetup=false){
    const size = getBoardSize();
    eliminated.clear();
    state = {
      size,
      grid: Array(size*size).fill(null),
      blocked: new Set(),
      order: getTurnOrder(config.players),
      turnIndex: 0,
      winner: null,
      line: [],
      moves: 0,
      mines: new Set(),
      chaosTick: 0,
      deck: [],
      hands: new Map(), // player -> array of cards
    };

    // Mines
    for(let m=0;m<mineCount();m++){
      let idx;
      do{ idx = randomInt(0, size*size-1) } while(state.mines.has(idx));
      state.mines.add(idx);
    }

    // Deck and starting hands
    buildDeck();
    dealStartingHands();

    // Grid
    boardEl.classList.remove('win');
    boardEl.innerHTML = '<div class="ribbon" aria-hidden="true"></div>';
    boardEl.style.gridTemplateColumns = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(480/size))}px))`;
    boardEl.style.gridTemplateRows = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(480/size))}px))`;

    for(let i=0;i<size*size;i++){
      const cell=document.createElement('button');
      cell.className='cell';
      cell.setAttribute('aria-label', `Cell ${i+1}`);
      cell.dataset.idx=i;
      cell.addEventListener('click', onCellClick);
      boardEl.appendChild(cell);
    }

    updateStatus();
    updateCardsTray(); // show only current player's cards
    if (config.mode==='blitz'){ baseTimer = config.blitzSec; startTurnTimer(baseTimer); } else { stopTurnTimer(); timerBox.style.display='none'; }

    if (!fromSetup) renderScores();
    maybeBotFirstMove();
  }

  function getTurnOrder(players){
    // 3‑player order: O → X → ■
    // 4‑player order: O → X → ■ → ▲
    if (players===4) return ['O','X','■','▲'];
    if (players===3) return ['O','X','■'];
    return ['X','O'];
  }
  function symbolClass(sym){
    if (sym==='X') return 'x';
    if (sym==='O') return 'o';
    if (sym==='■') return 'p3';
    if (sym==='▲') return 'p4';
    return 'x';
  }

  // Cards system
  const Rarity = { COMMON:'common', RARE:'rare', EPIC:'epic', LEGEND:'legend', MYTH:'myth' };
  function buildDeck(){
    const cards = [
      // COMMON
      card('+1 Skip', Rarity.COMMON, 'Target skips next turn', (ctx)=>skipTarget(ctx.target,1)),
      card('Block', Rarity.COMMON, 'Block a chosen empty cell', (ctx)=>blockChosenCell(ctx)),
      card('Clear', Rarity.COMMON, 'Clear a chosen mark', (ctx)=>clearChosenMark(ctx)),
      card('Swap Order', Rarity.COMMON, 'Reverse turn order', ()=>reverseTurnOrder()),
      card('Peek Mine', Rarity.COMMON, 'Reveal if a chosen cell is a mine', (ctx)=>peekMine(ctx)),
      card('Fog', Rarity.COMMON, 'Fog a chosen cell', (ctx)=>fogChosenCell(ctx)),
      card('Unfog', Rarity.COMMON, 'Remove fog from all cells', ()=>unfogBoard()),
      card('+Timer', Rarity.COMMON, '+2s to your timer (Blitz)', ()=>boostTimer(+2)),
      card('-Timer', Rarity.COMMON, '-2s to next player timer (Blitz)', ()=>drainNextTimer(2)),

      // RARE
      card('+2 Skip', Rarity.RARE, 'Target skips next 2 turns', (ctx)=>skipTarget(ctx.target,2)),
      card('Double Block', Rarity.RARE, 'Block two chosen empty cells', (ctx)=>blockTwo(ctx)),
      card('Undo', Rarity.RARE, 'Undo last move', ()=>undoLastMove()),
      card('Disarm', Rarity.RARE, 'Remove one random mine', ()=>removeRandomMine()),
      card('Plant', Rarity.RARE, 'Add one random mine', ()=>addRandomMine()),
      card('Clone', Rarity.RARE, 'Play two marks this turn (adjacent)', ()=>clonePlay()),
      card('Boost Draw', Rarity.RARE, 'Draw 1 extra card now', ()=>drawFor(currentPlayer(),1)),

      // EPIC
      card('Teleport', Rarity.EPIC, 'Move one of your marks to another cell', (ctx)=>teleportMark(ctx)),
      card('Shield', Rarity.EPIC, 'Protect one of your marks from clear/block', (ctx)=>shieldMark(ctx)),
      card('Accelerate', Rarity.EPIC, 'You play again after this turn', ()=>extraTurn()),
      card('Bridge', Rarity.EPIC, 'Connect two of your marks as a wildcard', (ctx)=>bridgeMarks(ctx)),
      card('Expand', Rarity.EPIC, 'Increase board win length tendency', ()=>adjustWinLength(+1)),

      // LEGEND
      card('Storm', Rarity.LEGEND, 'Clear 3 random opponent marks', ()=>clearRandomOpponentMarks(3)),
      card('Quake', Rarity.LEGEND, 'Block 4 random empty cells', ()=>blockRandomCells(4)),
      card('Time Warp', Rarity.LEGEND, 'Reset timers to 10s (Blitz)', ()=>resetTimers()),
      card('Swap Mine', Rarity.LEGEND, 'Move a mine to a chosen empty cell', (ctx)=>moveMineTo(ctx)),
      card('Specter', Rarity.LEGEND, 'Play on a blocked cell (once)', (ctx)=>playOnBlocked(ctx)),

      // MYTH (includes M87)
      card('M87', Rarity.MYTH, 'Black hole resets the board and restarts the round', ()=>m87Reset()),
      card('Singularity', Rarity.MYTH, 'Absorb all cards; everyone discards hand', ()=>absorbCards()),
      card('Nova', Rarity.MYTH, 'Add glow; next win highlights across board', ()=>novaGlow()),
      card('Anomaly', Rarity.MYTH, 'Random powerful effect', ()=>randomPower()),
    ];

    // Expand to 25+ by cloning variants with different intensities
    state.deck = cards.concat([
      card('Block Trio', Rarity.RARE, 'Block 3 chosen empty cells', (ctx)=>blockChosenCells(ctx,3)),
      card('Clear Duo', Rarity.RARE, 'Clear 2 chosen marks', (ctx)=>clearChosenMarks(ctx,2)),
      card('Peek Trio', Rarity.RARE, 'Reveal 3 selected cells for mines', (ctx)=>peekMultiple(ctx,3)),
      card('Bridge+', Rarity.EPIC, 'Bridge three marks (looser)', (ctx)=>bridgeMarks(ctx,true)),
      card('Accelerate+', Rarity.EPIC, 'Take 2 extra turns (limited)', ()=>extraTurn(2)),
      card('Storm+', Rarity.LEGEND, 'Clear 5 random opponent marks', ()=>clearRandomOpponentMarks(5)),
      card('Quake+', Rarity.LEGEND, 'Block 6 random empty cells', ()=>blockRandomCells(6)),
      card('Teleport+', Rarity.EPIC, 'Teleport two marks', (ctx)=>teleportMark(ctx,true)),
      card('Clone+', Rarity.RARE, 'Play two marks anywhere', ()=>clonePlay(true)),
      card('Shield+', Rarity.EPIC, 'Protect two of your marks', (ctx)=>shieldMark(ctx,true)),
    ]);
    shuffle(state.deck);
  }
  function card(name, rarity, desc, apply){
    return { name, rarity, desc, apply };
  }
  function dealStartingHands(){
    for(const p of state.order){
      const cnt = randomInt(2,3);
      state.hands.set(p, draw(cnt));
    }
  }
  function draw(n){
    const res = [];
    for(let i=0;i<n;i++){
      if (!state.deck.length) buildDeck();
      res.push(state.deck.pop());
    }
    return res;
  }
  function drawFor(p, n){
    const hand = state.hands.get(p) || [];
    hand.push(...draw(n));
    state.hands.set(p, hand);
    updateCardsTray();
  }
  function shuffle(arr){
    for(let i=arr.length-1;i>0;i--){
      const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]];
    }
  }

  // Cards tray shows only current player's hand
  function updateCardsTray(){
    const p = currentPlayer();
    const hand = state.hands.get(p) || [];
    if (config.mode!=='randomizer' || eliminated.has(p)){ cardsTray.style.display='none'; return; }
    cardsTray.style.display='flex';
    cardsTray.innerHTML='';
    hand.forEach((c, idx)=>{
      const el=document.createElement('div');
      el.className='cardItem';
      const rarClass = 'rar-' + c.rarity;
      el.innerHTML = `
        <div class="cardName">${c.name}</div>
        <div class="cardTip">
          <div style="font-weight:700; margin-bottom:6px">${c.name}</div>
          <div>Rarity: ${c.rarity.toUpperCase()}</div>
          <div style="margin-top:6px">${c.desc}</div>
        </div>
        <div class="rarBar ${rarClass}"></div>
      `;
      el.addEventListener('click', async ()=>{
        if (state.winner) return;
        const target = await chooseTargetIfNeeded(c.name);
        playCardEffect(c, target);
        // consume
        hand.splice(idx,1);
        state.hands.set(p, hand);
        updateCardsTray();
        // Some cards end turn, others grant extra turn; default: end turn
        if (!state.extraTurns || state.extraTurns<=0) advanceTurn();
        else state.extraTurns--;
      });
      cardsTray.appendChild(el);
    });
  }

  async function chooseTargetIfNeeded(cardName){
    // For cards that target a player; simple prompt UI
    const targetingCards = ['+1 Skip','+2 Skip','Storm','Storm+','Specter'];
    if (!targetingCards.includes(cardName)) return null;
    const others = state.order.filter(x=>x!==currentPlayer() && !eliminated.has(x));
    if (!others.length) return null;
    // Basic picker: choose first available for now (can enhance with modal)
    return others[0];
  }

  function playCardEffect(card, target){
    showEffect(card.name, `Rarity: ${card.rarity.toUpperCase()} — ${card.desc}`);
    try{
      card.apply({ target });
    }catch(e){}
  }

  function showEffect(title, desc){
    effectTitle.textContent = title;
    effectDesc.textContent = desc;
    effectOverlay.style.display='grid';
    setTimeout(()=>effectOverlay.style.display='none', 1200);
  }

  // Cell interactions
  async function onCellClick(e){
    if (state.winner) return;
    const i = Number(e.currentTarget.dataset.idx);
    const player = currentPlayer();

    if (eliminated.has(player)) return; // eliminated cannot play
    if (isBotTurn()) return;
    if (state.grid[i] || state.blocked.has(i)) return;

    // Minefield elimination
    if (state.mines.has(i) && config.mode==='mine'){
      placeGlyph(e.currentTarget, player);
      eliminatePlayer(player, `Mine triggered by ${player}`);
      return;
    }

    applyMove(i, player, e.currentTarget);

    // Randomizer frequent events
    if (config.mode==='randomizer') processChaosEvent(true);

    // Card draw every round end
    drawFor(player, 1);

    await maybeBotReply();
  }

  function eliminatePlayer(player, reason){
    eliminated.add(player);
    // Remove all their marks from board
    for(let idx=0; idx<state.grid.length; idx++){
      if (state.grid[idx]===player){
        state.grid[idx]=null;
        const el = boardEl.children[1 + idx];
        el.innerHTML='';
        el.classList.remove('played','win');
      }
    }
    showEffect('Eliminated', `${player} removed: ${reason}`);
    // If only one player left, that player wins immediately
    const activeLeft = state.order.filter(p=>!eliminated.has(p));
    if (activeLeft.length===1){
      endGame(activeLeft[0], [], false);
      return;
    }
    // If current eliminated, advance turn
    advanceTurn();
  }

  function applyMove(i, player, cellEl){
    state.grid[i] = player;
    state.moves++;
    placeGlyph(cellEl, player);

    const win = checkWin(state.grid, state.size, getWinLength());
    if (win){
      endGame(player, win.line, false);
      return;
    }

    // Draw if all filled or blocked (and no winner)
    if (state.moves + state.blocked.size === state.grid.length){
      statusEl.textContent = 'Draw';
      scores.D++; renderScores(); stopTurnTimer(); return;
    }

    advanceTurn();
  }

  function placeGlyph(el, player){
    el.innerHTML = `<span class="glyph ${symbolClass(player)}">${player}</span>`;
    el.classList.add('played');
  }

  function advanceTurn(){
    // Skip eliminated players
    do {
      state.turnIndex = (state.turnIndex + 1) % state.order.length;
    } while (eliminated.has(currentPlayer()));
    updateStatus();
    updateCardsTray();
    if (config.mode==='blitz'){ baseTimer += 0.5; startTurnTimer(baseTimer); }
  }

  function updateStatus(){
    const player = currentPlayer();
    statusEl.textContent = `${player} to move`;
    const color = player==='X' ? 'rgba(122,162,247,.25)'
                : player==='O' ? 'rgba(201,210,126,.25)'
                : player==='■' ? 'rgba(255,159,111,.25)'
                : 'rgba(106,227,189,.25)';
    statusEl.style.borderColor = color;
    statusEl.style.boxShadow = `0 0 12px ${color} inset`;
  }

  function endGame(winner, line, byMine=false){
    state.winner = winner;
    state.line = line || [];
    for(const idx of state.line){
      const cell = boardEl.children[1 + idx];
      cell && cell.classList.add('win');
    }
    statusEl.textContent = byMine ? `Mine! ${winner} wins` : `${winner} wins`;
    boardEl.classList.add('win');
    stopTurnTimer();
    if (winner==='X') scores.X++;
    else if (winner==='O') scores.O++;
    else if (winner==='■') scores.P3++;
    else if (winner==='▲') scores.P4++;
    renderScores();
  }

  function renderScores(){
    document.getElementById('scoreX').textContent = String(scores.X);
    document.getElementById('scoreO').textContent = String(scores.O);
    document.getElementById('scoreP3').textContent = String(scores.P3);
    document.getElementById('scoreP4').textContent = String(scores.P4);
    document.getElementById('scoreD').textContent = String(scores.D);
  }

  function currentPlayer(){
    return state.order[state.turnIndex];
  }

  // NxN win detection
  function checkWin(g, size, winLen){
    for(let r=0;r<size;r++){
      for(let c=0;c<=size-winLen;c++){
        const start = r*size+c; const first=g[start]; if(!first) continue;
        const line=[]; let ok=true;
        for(let k=0;k<winLen;k++){ const idx=r*size+(c+k); line.push(idx); if(g[idx]!==first){ ok=false; break; } }
        if(ok) return {player:first, line};
      }
    }
    for(let c=0;c<size;c++){
      for(let r=0;r<=size-winLen;r++){
        const start=r*size+c; const first=g[start]; if(!first) continue;
        const line=[]; let ok=true;
        for(let k=0;k<winLen;k++){ const idx=(r+k)*size+c; line.push(idx); if(g[idx]!==first){ ok=false; break; } }
        if(ok) return {player:first, line};
      }
    }
    for(let r=0;r<=size-winLen;r++){
      for(let c=0;c<=size-winLen;c++){
        const start=r*size+c; const first=g[start]; if(!first) continue;
        const line=[]; let ok=true;
        for(let k=0;k<winLen;k++){ const idx=(r+k)*size+(c+k); line.push(idx); if(g[idx]!==first){ ok=false; break; } }
        if(ok) return {player:first, line};
      }
    }
    for(let r=winLen-1;r<size;r++){
      for(let c=0;c<=size-winLen;c++){
        const start=r*size+c; const first=g[start]; if(!first) continue;
        const line=[]; let ok=true;
        for(let k=0;k<winLen;k++){ const idx=(r-k)*size+(c+k); line.push(idx); if(g[idx]!==first){ ok=false; break; } }
        if(ok) return {player:first, line};
      }
    }
    return null;
  }

  // Bot logic (2‑player only; O is bot)
  function isBotTurn(){ return config.opponent==='bot' && config.players===2 && currentPlayer()==='O' }
  async function maybeBotFirstMove(){ if (isBotTurn()) await botMove() }
  async function maybeBotReply(){ if (isBotTurn()) await botMove() }
  async function botMove(){
    await sleep(220 + Math.random()*280);
    const i = chooseBotMove();
    if (i==null) return;
    const cellEl = boardEl.children[1 + i];
    applyMove(i, 'O', cellEl);
  }
  function chooseBotMove(){
    const size = state.size, winLen = getWinLength();
    const grid = state.grid.slice();
    const d = config.difficulty;
    const winMove = findLineCompletion(grid, size, winLen, 'O'); if (winMove!=null && Math.random()<(0.85*d/5)) return winMove;
    const blockMove = findLineCompletion(grid, size, winLen, 'X'); if (blockMove!=null && Math.random()<(0.75*d/5)) return blockMove;
    const centers = getCenterIndices(size); const centerPick = centers.find(i=>!grid[i]); if (centerPick!=null && Math.random()<(0.6*d/5)) return centerPick;
    const empties = grid.map((v,idx)=>v?null:idx).filter(v=>v!=null);
    if (!empties.length) return null;
    if (Math.random()<(0.4*d/5)){
      const oPos = grid.map((v,i)=>v==='O'?i:null).filter(v=>v!=null);
      const scored = empties.map(i=>({i,score: adjacencyScore(i, oPos, size)})).sort((a,b)=>b.score-a.score);
      return scored[0].i;
    }
    return empties[Math.floor(Math.random()*empties.length)];
  }
  function findLineCompletion(grid,size,winLen,player){
    for(let i=0;i<grid.length;i++){ if(grid[i]) continue; grid[i]=player; const w=checkWin(grid,size,winLen); grid[i]=null; if(w&&w.player===player) return i; }
    return null;
  }
  function getCenterIndices(size){
    if(size%2===1){ return [Math.floor((size*size)/2)] }
    const c1=(size/2-1)*size+(size/2-1); return [c1,c1+1,c1+size,c1+size+1];
  }
  function adjacencyScore(i,positions,size){
    if(!positions.length) return 0;
    const r=Math.floor(i/size), c=i%size; let s=0;
    for(const p of positions){ const pr=Math.floor(p/size), pc=p%size; const d=Math.abs(pr-r)+Math.abs(pc-c); s+=Math.max(0,4-d) }
    return s;
  }

  // Blitz timer
  function startTurnTimer(sec){
    if (config.mode!=='blitz') return;
    stopTurnTimer();
    timerBox.style.display = 'inline-flex';
    let t = Math.max(1, Math.round(sec));
    timeLeftEl.textContent = t;
    timer = setInterval(()=>{
      t--; timeLeftEl.textContent = t;
      if (t<=0){
        const loser = currentPlayer();
        // Next active player wins
        let winnerSym = nextActive(loser);
        endGame(winnerSym, [], false);
        stopTurnTimer();
      }
    }, 1000);
  }
  function stopTurnTimer(){ if (timer){ clearInterval(timer); timer=null; } }
  function nextActive(from){
    let idx = state.order.indexOf(from);
    do {
      idx = (idx + 1) % state.order.length;
    } while (eliminated.has(state.order[idx]));
    return state.order[idx];
  }

  // Chaos Deck events (frequent)
  function processChaosEvent(often=false){
    state.chaosTick++;
    const interval = often ? 2 : 3;
    if (state.chaosTick % interval !== 0) return;
    // Frequent random event plus card draw
    const ev = randomEvent();
    showEffect(`Chaos: ${ev.name}`, ev.desc);
    ev.apply();
    drawFor(currentPlayer(), 1);
  }
  function randomEvent(){
    const events = [
      {name:'Block random cell', desc:'A random empty cell is blocked', apply:()=>blockRandomCells(1)},
      {name:'Clear random mark', desc:'A random mark is cleared', apply:()=>clearRandomOpponentMarks(1)},
      {name:'Reverse order', desc:'Turn order is reversed', apply:()=>reverseTurnOrder()},
      {name:'+Timer', desc:'+2s to current timer (Blitz)', apply:()=>boostTimer(+2)},
      {name:'-Timer', desc:'-2s to next timer (Blitz)', apply:()=>drainNextTimer(2)},
      {name:'Add mine', desc:'A mine appears randomly', apply:()=>addRandomMine()},
      {name:'Remove mine', desc:'A mine disappears', apply:()=>removeRandomMine()},
      {name:'Reveal mine hint', desc:'A mine pulses somewhere…', apply:()=>pulseMineTease()},
    ];
    return events[Math.floor(Math.random()*events.length)];
  }

  // Card effects (helpers)
  function skipTarget(target, n){
    // Tag target with skip counter
    state.skip = state.skip || {};
    state.skip[target] = (state.skip[target] || 0) + n;
  }
  function reverseTurnOrder(){
    state.order.reverse();
    // Ensure current player index mapped correctly
    const cp = currentPlayer();
    state.turnIndex = state.order.indexOf(cp);
    updateStatus();
  }
  function blockChosenCell(ctx){ pickCells(1, (indices)=>{ indices.forEach(i=>blockIndex(i)); }); }
  function blockTwo(ctx){ pickCells(2, (idxs)=> idxs.forEach(i=>blockIndex(i)) ); }
  function blockChosenCells(ctx, n){ pickCells(n, (idxs)=> idxs.forEach(i=>blockIndex(i)) ); }
  function blockRandomCells(n){
    const empties = emptyIndices();
    for(let k=0;k<n && empties.length;k++){
      const i = empties.splice(Math.floor(Math.random()*empties.length),1)[0];
      blockIndex(i);
    }
  }
  function blockIndex(i){
    if (state.blocked.has(i)) return;
    state.blocked.add(i);
    const el = boardEl.children[1 + i];
    el.setAttribute('disabled','true');
    el.style.opacity = .45; el.style.filter = 'grayscale(0.3) brightness(0.8)';
    el.title = 'Blocked';
    // Draw check
    if (state.moves + state.blocked.size === state.grid.length && !state.winner){
      statusEl.textContent = 'Draw'; scores.D++; renderScores(); stopTurnTimer();
    }
  }
  function clearChosenMark(ctx){ pickCells(1, (idxs)=> idxs.forEach(i=>clearMark(i)), true); }
  function clearChosenMarks(ctx, n){ pickCells(n, (idxs)=> idxs.forEach(i=>clearMark(i)), true); }
  function clearMark(i){
    if (!state.grid[i]) return;
    // Check shield
    if (state.shields && state.shields.has(i)) return;
    state.grid[i] = null;
    const el = boardEl.children[1 + i];
    el.innerHTML=''; el.classList.remove('played','win');
  }
  function undoLastMove(){
    // naive: clear last played cell
    for(let i=state.grid.length-1; i>=0; i--){
      if (boardEl.children[1 + i].classList.contains('played')){
        clearMark(i); break;
      }
    }
  }
  function removeRandomMine(){
    const arr = Array.from(state.mines);
    if (!arr.length) return;
    const i = arr[Math.floor(Math.random()*arr.length)];
    state.mines.delete(i);
  }
  function addRandomMine(){
    const i = randomInt(0, state.grid.length-1);
    state.mines.add(i);
  }
  function clonePlay(free=false){
    // Allow a second move; if free, anywhere; else adjacent to first
    state.extraTurns = (state.extraTurns||0) + (free?2:1);
  }
  function teleportMark(ctx, two=false){
    pickCells(two?2:1, (idxs)=>{
      idxs.forEach(i=>{
        if (!state.grid[i]) return;
        const p = state.grid[i];
        // choose empty destination
        const empties = emptyIndices();
        if (!empties.length) return;
        const dest = empties[Math.floor(Math.random()*empties.length)];
        state.grid[i]=null;
        const srcEl = boardEl.children[1 + i];
        srcEl.innerHTML=''; srcEl.classList.remove('played');
        state.grid[dest]=p;
        const dstEl = boardEl.children[1 + dest];
        placeGlyph(dstEl, p);
      });
    }, true);
  }
  function shieldMark(ctx, two=false){
    state.shields = state.shields || new Set();
    pickCells(two?2:1, (idxs)=> idxs.forEach(i=> state.shields.add(i)), true);
  }
  function extraTurn(times=1){ state.extraTurns = (state.extraTurns||0) + times; }
  function bridgeMarks(ctx, loose=false){
    // Visual effect only: showEffect already done; complex wildcard omitted in demo
  }
  function clearRandomOpponentMarks(n){
    const opponents = state.order.filter(p=>p!==currentPlayer());
    const marks = [];
    for(let i=0;i<state.grid.length;i++){
      if (state.grid[i] && opponents.includes(state.grid[i])) marks.push(i);
    }
    shuffle(marks);
    for(let k=0;k<n && marks.length;k++){ clearMark(marks.pop()); }
  }
  function peekMine(ctx){
    pickCells(1, (idxs)=>{
      idxs.forEach(i=>{
        const mine = state.mines.has(i);
        const el = boardEl.children[1 + i];
        el.style.outline = mine ? '2px solid var(--danger)' : '2px solid var(--win)';
        setTimeout(()=> el.style.outline='', 1200);
      });
    });
  }
  function peekMultiple(ctx, n){ pickCells(n, (idxs)=> idxs.forEach(i=>peekMine({target:null, i})) ); }
  function fogChosenCell(ctx){ pickCells(1, (idxs)=> idxs.forEach(i=> { const el = boardEl.children[1 + i]; el.style.filter='blur(2px) brightness(0.9)'; })); }
  function unfogBoard(){ Array.from(boardEl.children).slice(1).forEach(el=> el.style.filter=''); }
  function moveMineTo(ctx){
    pickCells(1, (idxs)=> idxs.forEach(i=> { if (!state.grid[i]) { addRandomMine(); /* simple demo */ } }));
  }
  function playOnBlocked(ctx){
    // Allow one move on any blocked cell for current player this turn
    state.playBlockedOnce = true;
  }
  function m87Reset(){
    showEffect('M87 — Black Hole', 'The board collapses. Round restarts.');
    initGame(true);
  }
  function absorbCards(){
    for(const p of state.order){ state.hands.set(p, []); }
    updateCardsTray();
  }
  function novaGlow(){
    boardEl.classList.add('win');
    setTimeout(()=>boardEl.classList.remove('win'), 1200);
  }
  function randomPower(){
    const powers = [m87Reset, novaGlow, ()=>blockRandomCells(4), ()=>clearRandomOpponentMarks(3), ()=>extraTurn(2)];
    powers[Math.floor(Math.random()*powers.length)]();
  }
  function boostTimer(delta){
    if (config.mode!=='blitz') return;
    baseTimer = Math.max(5, baseTimer + delta);
    startTurnTimer(baseTimer);
  }
  function drainNextTimer(delta){
    if (config.mode!=='blitz') return;
    // visual only; actual enforcement on next turn start
  }
  function resetTimers(){
    if (config.mode!=='blitz') return;
    baseTimer = 10;
    startTurnTimer(baseTimer);
  }

  // Target/cell picker (simplified)
  function pickCells(n, done, requireHasMark=false){
    // naive inline picker: click the highlighted cells
    let picked = [];
    const cells = Array.from(boardEl.children).slice(1);
    cells.forEach((el, idx)=>{
      const hasMark = !!state.grid[idx];
      const ok = requireHasMark ? hasMark : !requireHasMark;
      if (!ok) return;
      el.style.outline='2px solid var(--gold)';
      const h = ()=>{
        picked.push(idx);
        el.style.outline='';
        el.removeEventListener('click', h);
        if (picked.length===n){
          cells.forEach(c=> c.style.outline='');
          done(picked);
        }
      };
      el.addEventListener('click', h, {once:true});
    });
  }

  // Utils
  function emptyIndices(){ return state.grid.map((v,i)=> v||state.blocked.has(i)?null:i).filter(v=>v!=null); }
  function randomInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min }
  function sleep(ms){ return new Promise(res=>setTimeout(res, ms)) }

  // Controls
  resetBtn.addEventListener('click', async ()=>{
    showLoader(true); await animateProgress(700); showLoader(false); initGame();
  });
  backBtn.addEventListener('click', async ()=>{
    showLoader(true); await animateProgress(600); showLoader(false);
    window.location.href = 'index.html';
  });
  refreshBtn.addEventListener('click', async ()=>{
    showLoader(true); await animateProgress(800); showLoader(false);
  });
  setupBtn.addEventListener('click', openSetup);

  // Boot
  (async function boot(){
    showLoader(true); await animateProgress(900); showLoader(false);
    openSetup(); renderScores();
  })();
</script>
</body>
</html>
```
