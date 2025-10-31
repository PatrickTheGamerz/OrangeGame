# Tic‑Tac‑Toe (X and O) — Deluxe visuals, modal setup, gamemodes, bot difficulty scale

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>X and O — Deluxe</title>
  <style>
    :root{
      --bg:#0b0e14;
      --text:#e6e6e6; --muted:#a7b0c0;
      --panel:#151822; --panel-soft:rgba(21,24,34,.75);
      --border:rgba(122,162,247,.28);
      --accent:#7aa2f7; --accent-2:#c28fff;
      --x:#7aa2f7; --o:#c9d27e; --p3:#ff9f6f; --p4:#6ae3bd;
      --win:#73d13d; --danger:#f7768e;
      --shadow:0 12px 36px rgba(0,0,0,.5);
    }
    *{ box-sizing:border-box }
    html,body{ height:100% }
    body{
      margin:0; color:var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
      background:
        radial-gradient(1200px 600px at 65% -10%, #14203a 0%, var(--bg) 60%) fixed,
        radial-gradient(900px 420px at 12% 120%, #1a1f35 0%, transparent 65%) fixed;
      display:grid; grid-template-rows: auto 1fr auto;
    }

    /* Topbar */
    .topbar{
      display:flex; align-items:center; gap:12px;
      padding:14px 18px;
      backdrop-filter: blur(10px);
      background: linear-gradient(0deg, rgba(16,18,28,.55), rgba(16,18,28,.78));
      border-bottom: 1px solid rgba(122,162,247,.18);
      box-shadow: var(--shadow);
    }
    .brand{
      font-weight:800; letter-spacing:.2px;
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
      -webkit-background-clip:text; background-clip:text; color:transparent;
      user-select:none;
    }
    .spacer{ flex:1 }
    .btn{
      border:1px solid var(--border);
      background: linear-gradient(180deg, #111423, #0d1120);
      color:var(--text); padding:10px 14px; border-radius:12px; cursor:pointer;
      transition: transform .08s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
      box-shadow: 0 6px 16px rgba(122,162,247,.12);
    }
    .btn:hover{
      border-color: rgba(122,162,247,.38);
      background: linear-gradient(180deg, #14182a, #0f1325);
      box-shadow: 0 10px 24px rgba(122,162,247,.22);
      transform: translateY(-1px);
    }
    .btn:active{ transform: translateY(1px) }

    /* Wrapper */
    .wrap{
      display:grid; justify-items:center; align-content:start; gap:16px;
      padding: 24px 20px 28px;
    }

    /* Status + chips */
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
      padding:4px 10px; border-radius:999px;
      user-select:none;
    }
    .dot{ width:10px; height:10px; border-radius:50%; box-shadow: 0 0 10px currentColor }
    .dot.x{ color:var(--x); background: var(--x) }
    .dot.o{ color:var(--o); background: var(--o) }
    .dot.p3{ color:var(--p3); background: var(--p3) }
    .dot.p4{ color:var(--p4); background: var(--p4) }

    /* Board */
    .board{
      position:relative;
      display:grid;
      grid-template-columns: repeat(3, min(18vw, 110px));
      grid-template-rows: repeat(3, min(18vw, 110px));
      gap: 12px;
      padding: 16px;
      border-radius: 18px;
      border: 1px solid var(--border);
      background: var(--panel-soft);
      backdrop-filter: blur(8px);
      box-shadow: var(--shadow);
    }
    .cell{
      position:relative;
      display:grid; place-items:center;
      font-size: clamp(28px, 6vw, 42px);
      font-weight:800;
      border: 1px solid rgba(122,162,247,.22);
      border-radius: 14px;
      background: linear-gradient(180deg, #14182a, #101423);
      color: var(--text);
      cursor: pointer; user-select:none;
      transition: transform .12s ease, border-color .15s ease, box-shadow .18s ease, background .22s ease, color .2s ease, filter .2s ease;
      overflow:hidden;
    }
    .cell::after{
      content:"";
      position:absolute; inset:0;
      background: radial-gradient(120px 60px at 50% 30%, rgba(122,162,247,.22), transparent 60%);
      opacity:0; transition: opacity .25s ease;
    }
    .cell:hover{
      transform: translateY(-2px);
      border-color: rgba(122,162,247,.42);
      box-shadow: 0 12px 24px rgba(122,162,247,.12);
      background: linear-gradient(180deg, #16203a, #111423);
    }
    .cell:hover::after{ opacity:.5 }
    .cell.played{
      animation: pop .18s cubic-bezier(.2, .7, .2, 1);
      filter: drop-shadow(0 6px 10px rgba(0,0,0,.25));
    }
    @keyframes pop{ 0%{ transform: scale(.85) } 100%{ transform: scale(1) } }

    .glyph{ position:relative; display:inline-block }
    .glyph.x{ color: var(--x); text-shadow: 0 0 12px rgba(122,162,247,.45) }
    .glyph.o{ color: var(--o); text-shadow: 0 0 12px rgba(201,210,126,.45) }
    .glyph.p3{ color: var(--p3); text-shadow: 0 0 12px rgba(255,159,111,.45) }
    .glyph.p4{ color: var(--p4); text-shadow: 0 0 12px rgba(106,227,189,.45) }

    /* Win */
    .win{
      outline: 2px solid var(--win);
      box-shadow: 0 0 0 2px rgba(115,209,61,.25) inset,
                  0 0 22px rgba(115,209,61,.35);
      animation: winPulse 1.4s ease-in-out infinite;
    }
    @keyframes winPulse{
      0%{ filter: brightness(1) }
      50%{ filter: brightness(1.25) }
      100%{ filter: brightness(1) }
    }
    .ribbon{
      position:absolute; inset:-2px; border-radius: 18px; pointer-events:none;
      background:
        conic-gradient(from 0deg,
          rgba(122,162,247,.18) 0deg 150deg,
          rgba(194,143,255,.18) 150deg 270deg,
          rgba(106,227,189,.18) 270deg 360deg);
      mix-blend-mode: screen;
      opacity:0; transition: opacity .3s ease;
    }
    .board.win .ribbon{ opacity:.75; animation: spin 4s linear infinite }
    @keyframes spin{ to{ transform: rotate(360deg) } }

    /* Controls + scoreboard */
    .bar{ display:flex; gap:10px; align-items:center; flex-wrap:wrap }
    .score{
      display:inline-flex; gap:14px; align-items:center; color:var(--muted); font-size:13px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(255,255,255,.06);
      padding:6px 10px; border-radius:999px;
    }
    .score .x{ color:var(--x); font-weight:600 }
    .score .o{ color:var(--o); font-weight:600 }
    .score .p3{ color:var(--p3); font-weight:600 }
    .score .p4{ color:var(--p4); font-weight:600 }
    .score .d{ color:#c7cbd6; font-weight:600 }

    /* Loading overlay */
    .loading{
      position:fixed; inset:0; display:grid; place-items:center;
      background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.88), rgba(10,12,18,.95));
      backdrop-filter: blur(6px);
      z-index:1000;
      opacity:0; pointer-events:none;
      transition: opacity .28s ease;
    }
    .loading.active{ opacity:1; pointer-events:auto }
    .loader{
      display:grid; gap:12px; justify-items:center;
      padding: 24px 26px; border: 1px solid rgba(122,162,247,.28);
      border-radius: 16px; background: linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88));
      box-shadow: var(--shadow);
    }
    .ring{
      width: 62px; height: 62px; border-radius: 50%;
      background:
        conic-gradient(from 0deg,
          var(--accent) 0% 30%,
          var(--accent-2) 30% 60%,
          #6ae3bd 60% 85%,
          transparent 85% 100%);
      -webkit-mask: radial-gradient(closest-side, transparent 66%, black 67%);
              mask: radial-gradient(closest-side, transparent 66%, black 67%);
      animation: spin .9s linear infinite;
    }
    .progress{ width:240px; height:8px; border-radius:999px; border:1px solid rgba(255,255,255,.1); background: rgba(255,255,255,.06); overflow:hidden }
    .bar{ height:100%; background: linear-gradient(90deg, var(--accent), var(--accent-2)); width:0%; transition: width .28s ease }
    .loader-label{ font-size:12px; color:var(--muted) }

    /* Modal setup */
    .modal{
      position:fixed; inset:0; display:grid; place-items:center;
      background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.86), rgba(10,12,18,.92));
      backdrop-filter: blur(6px);
      z-index:1001;
      opacity:0; pointer-events:none;
      transition: opacity .28s ease;
    }
    .modal.active{ opacity:1; pointer-events:auto }
    .sheet{
      width:min(720px, 92vw);
      display:grid; gap:14px;
      padding: 18px 18px 20px;
      border: 1px solid rgba(122,162,247,.28);
      border-radius: 14px;
      background: linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88));
      box-shadow: var(--shadow);
    }
    .sheet h3{ margin:0; font-size:18px }
    .group{ display:grid; gap:8px }
    .row-flex{ display:flex; gap:12px; align-items:center; flex-wrap:wrap }
    .select, .range{
      width:100%; background:#101423; border:1px solid rgba(255,255,255,.12); color:var(--text);
      border-radius:10px; padding:8px 10px;
    }
    .range{ accent-color: var(--accent) }
    .pill{
      display:inline-flex; align-items:center; gap:8px; padding:6px 10px; border-radius:999px;
      border:1px solid rgba(255,255,255,.12); background: rgba(255,255,255,.06); color:var(--muted); font-size:12px;
    }
    .sheet .actions{ display:flex; gap:10px; justify-content:flex-end }
  </style>
</head>
<body>
  <header class="topbar">
    <div class="brand">X and O</div>
    <div class="spacer"></div>
    <button class="btn" id="back">Back to selector</button>
    <button class="btn" id="refresh">Refresh</button>
    <button class="btn" id="setup">Setup</button>
  </header>

  <section class="wrap">
    <div class="row">
      <div class="status" id="status">X to move</div>
      <span class="chip"><span class="dot x"></span>X starts</span>
      <span class="chip"><span class="dot o"></span>3 in a row wins</span>
    </div>

    <div class="board" id="board" aria-label="Tic-tac-toe board">
      <div class="ribbon" aria-hidden="true"></div>
      <!-- Cells injected -->
    </div>

    <div class="bar">
      <button class="btn" id="reset">Reset</button>
      <div class="score">
        <span class="x">X:</span><span id="scoreX">0</span>
        <span class="o">O:</span><span id="scoreO">0</span>
        <span class="p3">P3:</span><span id="scoreP3">0</span>
        <span class="p4">P4:</span><span id="scoreP4">0</span>
        <span class="d">Draws:</span><span id="scoreD">0</span>
      </div>
      <div class="score" id="timerBox" style="display:none">
        <span class="d">Timer:</span><span id="timeLeft">0</span>s
      </div>
    </div>
  </section>

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
          <div class="pill">Difficulty (1–5, decimals ok)</div>
          <input class="range" id="difficulty" type="range" min="1" max="5" step="0.1" value="2.5" />
          <div class="pill"><span id="difficultyLabel">2.5</span></div>
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
          <option value="mine">Minefield (one hidden mine)</option>
        </select>
      </div>

      <div class="group">
        <div class="pill">Players</div>
        <div class="row-flex">
          <select class="select" id="playerCount">
            <option value="2">2 players</option>
            <option value="3">3 players</option>
            <option value="4">4 players</option>
          </select>
          <div class="pill">Blitz timer (5–20s, +0.5s per turn)</div>
          <input class="range" id="blitzSec" type="range" min="5" max="20" step="1" value="10" />
          <div class="pill"><span id="blitzLabel">10s</span></div>
        </div>
      </div>

      <div class="actions">
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
    const difficultyRange = document.getElementById('difficulty');
    const difficultyLabel = document.getElementById('difficultyLabel');
    const modeSel = document.getElementById('mode');
    const playerCountSel = document.getElementById('playerCount');
    const blitzRange = document.getElementById('blitzSec');
    const blitzLabel = document.getElementById('blitzLabel');
    const cancelSetup = document.getElementById('cancelSetup');
    const applySetup = document.getElementById('applySetup');

    const timerBox = document.getElementById('timerBox');
    const timeLeftEl = document.getElementById('timeLeft');

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

    // Modal
    function openSetup(){ modal.classList.add('active'); updateSetupLabels(); }
    function closeSetup(){ modal.classList.remove('active'); }
    function updateSetupLabels(){
      difficultyLabel.textContent = difficultyRange.value;
      blitzLabel.textContent = blitzRange.value + 's';
    }
    difficultyRange.addEventListener('input', updateSetupLabels);
    blitzRange.addEventListener('input', updateSetupLabels);
    cancelSetup.addEventListener('click', closeSetup);
    applySetup.addEventListener('click', async ()=>{
      config.opponent = opponentSel.value;
      config.difficulty = parseFloat(difficultyRange.value);
      config.mode = modeSel.value;
      config.players = parseInt(playerCountSel.value, 10);
      config.blitzSec = parseInt(blitzRange.value, 10);
      closeSetup();
      showLoader(true); await animateProgress(800); showLoader(false);
      initGame(true);
    });

    // Board dimensions by mode/player count
    function getBoardSize(){
      // Casual default 3x3, three players -> 4x4, four players -> 5x5 (scaled bigger board)
      if (config.mode === 'three' || config.players === 3) return 4;
      if (config.mode === 'four' || config.players === 4) return 5;
      return 3;
    }
    function getWinLength(){
      // Casual 3, bigger boards win length 4 for better challenge
      const size = getBoardSize();
      return size >= 4 ? 4 : 3;
    }

    // Init game
    function initGame(fromSetup=false){
      const size = getBoardSize();
      state = {
        size,
        grid: Array(size*size).fill(null),
        order: getTurnOrder(config.players),
        turnIndex: 0,
        winner: null,
        line: [],
        moves: 0,
        mineIndex: (config.mode==='mine') ? randomInt(0, size*size-1) : null,
        chaosTick: 0
      };

      // Grid layout
      boardEl.classList.remove('win');
      boardEl.querySelector('.ribbon')?.classList.remove('visible');
      boardEl.innerHTML = '<div class="ribbon" aria-hidden="true"></div>';
      boardEl.style.gridTemplateColumns = `repeat(${size}, min(16vw, ${Math.max(80, 360/size)}px))`;
      boardEl.style.gridTemplateRows = `repeat(${size}, min(16vw, ${Math.max(80, 360/size)}px))`;

      for(let i=0;i<size*size;i++){
        const cell=document.createElement('button');
        cell.className='cell';
        cell.setAttribute('aria-label', `Cell ${i+1}`);
        cell.dataset.idx=i;
        cell.addEventListener('click', onMove);
        boardEl.appendChild(cell);
      }

      // Status
      updateStatus();

      // Timer (Blitz)
      if (config.mode==='blitz'){
        baseTimer = config.blitzSec;
        startTurnTimer(baseTimer);
      } else {
        stopTurnTimer();
        timerBox.style.display = 'none';
      }

      // Mine hint (optional visual pulse for suspense)
      if (config.mode==='mine'){
        // No reveal; just visual tension via slight glow on random cells over time
        pulseMineTease();
      }

      // If setup invoked, ensure scores visible but unchanged
      if (!fromSetup) renderScores();

      // If bot starts and opponent is bot in 2-player
      maybeBotFirstMove();
    }

    function getTurnOrder(players){
      // Map players to glyph keys
      if (players===4) return ['X','O','▲','■']; // triangle/ square
      if (players===3) return ['X','O','■'];
      return ['X','O'];
    }
    function symbolClass(sym){
      if (sym==='X') return 'x';
      if (sym==='O') return 'o';
      if (sym==='■') return 'p3';
      if (sym==='▲') return 'p4';
      return 'x';
    }

    // Move handling
    async function onMove(e){
      if (state.winner) return;
      const i = Number(e.currentTarget.dataset.idx);
      const currentPlayer = state.order[state.turnIndex];

      // If bot turn, ignore human clicks
      if (isBotTurn()) return;

      if (state.grid[i]) return;
      if (config.mode==='mine' && i===state.mineIndex){
        placeGlyph(e.currentTarget, currentPlayer);
        // Player stepped on mine -> instant loss; winner is next player
        const loser = currentPlayer;
        const winnerSym = state.order[(state.turnIndex+1) % state.order.length];
        endGame(winnerSym, [], true, `Mine triggered by ${loser}`);
        return;
      }

      applyMove(i, currentPlayer, e.currentTarget);

      // Chaos Deck event tick
      if (config.mode==='randomizer') processChaosEvent();

      // Bot reply if applicable
      await maybeBotReply();
    }

    function applyMove(i, player, cellEl){
      state.grid[i] = player;
      state.moves++;
      placeGlyph(cellEl, player);

      const win = checkWin(state.grid, state.size, getWinLength());
      if (win){
        endGame(player, win.line, false, `${player} wins`);
        return;
      }
      if (state.moves === state.grid.length){
        statusEl.textContent = 'Draw';
        scores.D++; renderScores();
        stopTurnTimer();
        return;
      }

      advanceTurn();
    }

    function placeGlyph(el, player){
      el.innerHTML = `<span class="glyph ${symbolClass(player)}">${player}</span>`;
      el.classList.add('played');
    }

    function advanceTurn(){
      state.turnIndex = (state.turnIndex + 1) % state.order.length;
      updateStatus();
      if (config.mode==='blitz'){
        baseTimer += 0.5; // +0.5s each turn
        startTurnTimer(baseTimer);
      }
    }

    function updateStatus(){
      const player = state.order[state.turnIndex];
      statusEl.textContent = `${player} to move`;
      const color = player==='X' ? 'rgba(122,162,247,.25)'
                  : player==='O' ? 'rgba(201,210,126,.25)'
                  : player==='■' ? 'rgba(255,159,111,.25)'
                  : 'rgba(106,227,189,.25)'; // ▲
      statusEl.style.borderColor = color;
      statusEl.style.boxShadow = `0 0 12px ${color} inset`;
    }

    function endGame(winner, line, byMine=false, labelText=''){
      state.winner = winner;
      state.line = line || [];
      for(const i of state.line){
        const cell = boardEl.children[1 + i]; // +1 due to ribbon element at index 0
        cell && cell.classList.add('win');
      }
      statusEl.textContent = byMine ? `Mine! ${winner} wins` : `${winner} wins`;
      boardEl.classList.add('win');
      stopTurnTimer();

      // Score
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

    // Win detection for NxN with winLength
    function checkWin(g, size, winLen){
      // Check rows
      for(let r=0; r<size; r++){
        for(let c=0; c<=size-winLen; c++){
          const start = r*size+c;
          const line = [];
          const first = g[start];
          if(!first) continue;
          let ok=true;
          for(let k=0;k<winLen;k++){
            const idx = r*size + (c+k);
            line.push(idx);
            if(g[idx]!==first){ ok=false; break; }
          }
          if(ok) return { player:first, line };
        }
      }
      // Check cols
      for(let c=0; c<size; c++){
        for(let r=0; r<=size-winLen; r++){
          const start = r*size+c;
          const line = [];
          const first = g[start];
          if(!first) continue;
          let ok=true;
          for(let k=0;k<winLen;k++){
            const idx = (r+k)*size + c;
            line.push(idx);
            if(g[idx]!==first){ ok=false; break; }
          }
          if(ok) return { player:first, line };
        }
      }
      // Diag down-right
      for(let r=0; r<=size-winLen; r++){
        for(let c=0; c<=size-winLen; c++){
          const start = r*size+c;
          const first = g[start];
          if(!first) continue;
          const line = [];
          let ok=true;
          for(let k=0;k<winLen;k++){
            const idx = (r+k)*size + (c+k);
            line.push(idx);
            if(g[idx]!==first){ ok=false; break; }
          }
          if(ok) return { player:first, line };
        }
      }
      // Diag up-right
      for(let r=winLen-1; r<size; r++){
        for(let c=0; c<=size-winLen; c++){
          const start = r*size+c;
          const first = g[start];
          if(!first) continue;
          const line = [];
          let ok=true;
          for(let k=0;k<winLen;k++){
            const idx = (r-k)*size + (c+k);
            line.push(idx);
            if(g[idx]!==first){ ok=false; break; }
          }
          if(ok) return { player:first, line };
        }
      }
      return null;
    }

    // Bot logic (scaled heuristic by difficulty; supports NxN)
    function isBotTurn(){
      // Bot acts only if opponent is bot and players==2; for 3/4 players, all are human by default
      return config.opponent==='bot' && config.players===2 && state.order[state.turnIndex] === 'O';
    }
    async function maybeBotFirstMove(){
      if (isBotTurn()){
        await botMove();
      }
    }
    async function maybeBotReply(){
      if (isBotTurn()){
        await botMove();
      }
    }
    async function botMove(){
      await sleep(220 + Math.random()*280);
      const i = chooseBotMove();
      if (i==null) return;
      const cellEl = boardEl.children[1 + i]; // +1 for ribbon
      applyMove(i, 'O', cellEl);
    }
    function chooseBotMove(){
      const size = state.size, winLen = getWinLength();
      const grid = state.grid.slice();
      // Difficulty scaling: 1–5 allows decimals; mix randomness with heuristics
      const d = config.difficulty; // 1 = random, 5 = strongest
      // 1) If can win, take it
      const winMove = findLineCompletion(grid, size, winLen, 'O');
      if (winMove!=null && Math.random() < (0.85 * d/5)) return winMove;
      // 2) Block opponent X
      const blockMove = findLineCompletion(grid, size, winLen, 'X');
      if (blockMove!=null && Math.random() < (0.75 * d/5)) return blockMove;
      // 3) Weighted center preference
      const centers = getCenterIndices(size);
      const centerPick = centers.find(i=>!grid[i]);
      if (centerPick!=null && Math.random() < (0.6 * d/5)) return centerPick;
      // 4) Random among empties with small bias toward adjacency
      const empties = grid.map((v,idx)=>v?null:idx).filter(v=>v!=null);
      if (!empties.length) return null;
      if (Math.random() < (0.4 * d/5)){
        // prefer moves near existing O marks
        const oPositions = grid.map((v,i)=>v==='O'?i:null).filter(v=>v!=null);
        const scored = empties.map(i=>({i,score: adjacencyScore(i, oPositions, size)}))
                              .sort((a,b)=>b.score-a.score);
        return scored[0].i;
      }
      return empties[Math.floor(Math.random()*empties.length)];
    }
    function findLineCompletion(grid, size, winLen, player){
      // Try placing player in empties; if results in win, return index
      for(let i=0;i<grid.length;i++){
        if (grid[i]) continue;
        grid[i] = player;
        const w = checkWin(grid, size, winLen);
        grid[i] = null;
        if (w && w.player===player) return i;
      }
      return null;
    }
    function getCenterIndices(size){
      if (size%2===1){
        return [Math.floor((size*size)/2)];
      } else {
        const c1 = (size/2-1)*size + (size/2-1);
        return [c1, c1+1, c1+size, c1+size+1];
      }
    }
    function adjacencyScore(i, positions, size){
      if (!positions.length) return 0;
      const r = Math.floor(i/size), c = i%size;
      let s=0;
      for(const p of positions){
        const pr=Math.floor(p/size), pc=p%size;
        const d = Math.abs(pr-r)+Math.abs(pc-c);
        s += Math.max(0, 4 - d);
      }
      return s;
    }

    // Blitz timer
    function startTurnTimer(sec){
      stopTurnTimer();
      timerBox.style.display = 'inline-flex';
      let t = Math.max(1, Math.round(sec));
      timeLeftEl.textContent = t;
      timer = setInterval(()=>{
        t--; timeLeftEl.textContent = t;
        if (t<=0){
          // Current player loses
          const loser = state.order[state.turnIndex];
          const winnerSym = state.order[(state.turnIndex+1) % state.order.length];
          endGame(winnerSym, [], false, 'Timeout');
          stopTurnTimer();
        }
      }, 1000);
    }
    function stopTurnTimer(){
      if (timer){ clearInterval(timer); timer=null; }
    }

    // Chaos Deck (lightweight demo: random small set, stub for big expansion)
    function processChaosEvent(){
      state.chaosTick++;
      // Every 3 moves, trigger a random event
      if (state.chaosTick % 3 !== 0) return;
      const events = [
        {name:'+2 turn skips', apply: ()=>skipNextTurns(1)},
        {name:'Freeze next', apply: ()=>skipNextTurns(0.5)}, // half skip: visual hint only
        {name:'Reverse order', apply: ()=>reverseTurnOrder()},
        {name:'Block random cell', apply: ()=>blockRandomCell()},
        {name:'Clear one random mark', apply: ()=>clearRandomMark()},
      ];
      const ev = events[Math.floor(Math.random()*events.length)];
      flashStatus(`Chaos: ${ev.name}`);
      ev.apply();
    }
    function skipNextTurns(count){
      // Simple: advance turn extra times
      for(let k=0;k<count;k++){
        state.turnIndex = (state.turnIndex + 1) % state.order.length;
      }
      updateStatus();
    }
    function reverseTurnOrder(){
      state.order.reverse();
      updateStatus();
    }
    function blockRandomCell(){
      const empties = state.grid.map((v,i)=>v?null:i).filter(v=>v!=null);
      if (!empties.length) return;
      const i = empties[Math.floor(Math.random()*empties.length)];
      const el = boardEl.children[1 + i];
      el.setAttribute('disabled','true');
      el.style.opacity = .45;
      el.style.filter = 'grayscale(0.3) brightness(0.8)';
      el.title = 'Blocked';
    }
    function clearRandomMark(){
      const marks = state.grid.map((v,i)=>v?i:null).filter(v=>v!=null);
      if (!marks.length) return;
      const i = marks[Math.floor(Math.random()*marks.length)];
      state.grid[i] = null;
      const el = boardEl.children[1 + i];
      el.innerHTML = '';
      el.classList.remove('played','win');
    }
    function flashStatus(text){
      statusEl.textContent = text;
      statusEl.style.borderColor = 'rgba(255,206,99,.35)';
      statusEl.style.boxShadow = '0 0 12px rgba(255,206,99,.25) inset';
      setTimeout(()=>updateStatus(), 1200);
    }

    // Minefield tease
    function pulseMineTease(){
      // Subtle glow sweeps
      let t=0;
      const sweep = ()=>{
        t++;
        const children = Array.from(boardEl.children).slice(1);
        children.forEach((c,idx)=>{
          c.style.outlineOffset = (Math.sin((t+idx)/12)*2)+'px';
        });
      };
      setTimeout(()=>{ // run brief tease
        const id = setInterval(sweep, 120);
        setTimeout(()=>clearInterval(id), 1600);
      }, 300);
    }

    // Utils
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
      openSetup(); // prompt setup on first load
      renderScores();
    })();
  </script>
</body>
</html>
