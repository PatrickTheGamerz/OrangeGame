# Tic‑Tac‑Toe (X and O)

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
      --border:rgba(122,162,247,.25);
      --accent:#7aa2f7; --accent-2:#c28fff;
      --x:#7aa2f7; --o:#c9d27e;
      --win:#73d13d; --danger:#f7768e;
      --shadow:0 12px 28px rgba(0,0,0,.45);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; color:var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
      background: radial-gradient(1200px 600px at 65% -10%, #14203a 0%, var(--bg) 60%) fixed;
      display:grid; grid-template-rows: auto 1fr auto;
    }

    /* Topbar */
    .topbar{
      display:flex; align-items:center; gap:12px;
      padding:14px 18px;
      backdrop-filter: blur(10px);
      background: linear-gradient(0deg, rgba(16,18,28,.55), rgba(16,18,28,.75));
      border-bottom: 1px solid rgba(122,162,247,.15);
      box-shadow: var(--shadow);
    }
    .brand{
      font-weight:700;
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
      -webkit-background-clip:text; background-clip:text; color:transparent;
    }
    .spacer{flex:1}
    .btn{
      border:1px solid var(--border);
      background: linear-gradient(180deg, #111423, #0d1120);
      color:var(--text); padding:8px 12px; border-radius:10px; cursor:pointer;
      transition: transform .08s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
      box-shadow: 0 6px 16px rgba(122,162,247,.08);
    }
    .btn:hover{
      border-color: rgba(122,162,247,.35);
      background: linear-gradient(180deg, #14182a, #0f1325);
      box-shadow: 0 8px 20px rgba(122,162,247,.18);
    }
    .btn:active{ transform: translateY(1px) }

    /* Container */
    .wrap{
      display:grid; justify-items:center; align-content:start; gap:16px;
      padding: 24px 20px 28px;
    }

    /* Status + legend */
    .row{ display:flex; gap:12px; align-items:center; flex-wrap:wrap }
    .status{
      font-size:14px; color:var(--muted);
      border:1px solid rgba(255,255,255,.08); border-radius:999px;
      background: rgba(255,255,255,.04);
      padding:6px 10px;
    }
    .chip{
      display:inline-flex; align-items:center; gap:7px;
      font-size:12px; color:var(--muted);
      border:1px solid rgba(255,255,255,.08);
      background: rgba(255,255,255,.04);
      padding:4px 8px; border-radius:999px;
    }
    .dot-x,.dot-o{
      width:10px; height:10px; border-radius:50%;
      box-shadow: 0 0 10px currentColor;
    }
    .dot-x{ color:var(--x); background: var(--x) }
    .dot-o{ color:var(--o); background: var(--o) }

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
      font-weight: 800;
      border: 1px solid rgba(122,162,247,.22);
      border-radius: 14px;
      background: linear-gradient(180deg, #14182a, #101423);
      color: var(--text);
      cursor: pointer;
      user-select: none;
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
      color: var(--text);
      filter: drop-shadow(0 6px 10px rgba(0,0,0,.25));
    }
    @keyframes pop{ 0%{ transform: scale(.85) } 100%{ transform: scale(1) } }

    /* X/O glyph glow */
    .glyph{
      position:relative;
      display:inline-block;
    }
    .glyph.x{ color: var(--x); text-shadow: 0 0 12px rgba(122,162,247,.45) }
    .glyph.o{ color: var(--o); text-shadow: 0 0 12px rgba(201,210,126,.45) }

    /* Win highlight */
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

    /* Controls + scoreboard */
    .bar{ display:flex; gap:10px; align-items:center; flex-wrap:wrap }
    .score{
      display:inline-flex; gap:14px; align-items:center; color:var(--muted); font-size:13px;
      border:1px solid rgba(255,255,255,.08);
      background: rgba(255,255,255,.04);
      padding:6px 10px; border-radius:999px;
    }
    .score .x{ color:var(--x); font-weight:600 }
    .score .o{ color:var(--o); font-weight:600 }
    .score .d{ color:#c7cbd6; font-weight:600 }

    /* Celebration ribbon */
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

    /* Loading overlay */
    .loading{
      position:fixed; inset:0; display:grid; place-items:center;
      background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.85), rgba(10,12,18,.92));
      backdrop-filter: blur(6px);
      z-index:1000;
      opacity:0; pointer-events:none;
      transition: opacity .28s ease;
    }
    .loading.active{ opacity:1; pointer-events:auto }
    .loader{
      display:grid; gap:12px; justify-items:center;
      padding: 24px 26px;
      border: 1px solid rgba(122,162,247,.25);
      border-radius: 16px;
      background: linear-gradient(180deg, rgba(16,20,32,.75), rgba(14,18,30,.85));
      box-shadow: var(--shadow);
    }
    .ring{
      width: 58px; height: 58px; border-radius: 50%;
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
    .progress{ width:220px; height:8px; border-radius:999px; border:1px solid rgba(255,255,255,.1); background: rgba(255,255,255,.06); overflow:hidden }
    .bar{ height:100%; background: linear-gradient(90deg, var(--accent), var(--accent-2)); width:0%; transition: width .3s ease }
    .loader-label{ font-size:12px; color:var(--muted) }
  </style>
</head>
<body>
  <header class="topbar">
    <div class="brand">X and O</div>
    <div class="spacer"></div>
    <button class="btn" id="back">Back to selector</button>
    <button class="btn" id="refresh">Refresh</button>
  </header>

  <section class="wrap">
    <div class="row">
      <div class="status" id="status">X to move</div>
      <span class="chip"><span class="dot-x"></span>X starts</span>
      <span class="chip"><span class="dot-o"></span>3 in a row wins</span>
    </div>

    <div class="board" id="board" aria-label="Tic-tac-toe board">
      <div class="ribbon" aria-hidden="true"></div>
      <!-- Cells will be injected -->
    </div>

    <div class="bar">
      <button class="btn" id="reset">Reset</button>
      <div class="score">
        <span class="x">X wins:</span><span id="scoreX">0</span>
        <span class="o">O wins:</span><span id="scoreO">0</span>
        <span class="d">Draws:</span><span id="scoreD">0</span>
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

  <script>
    const boardEl = document.getElementById('board');
    const statusEl = document.getElementById('status');
    const resetBtn = document.getElementById('reset');
    const backBtn = document.getElementById('back');
    const refreshBtn = document.getElementById('refresh');
    const loading = document.getElementById('loading');
    const bar = document.getElementById('bar');
    const label = document.getElementById('loaderLabel');

    let state, scores = { X:0, O:0, D:0 };

    function showLoader(flag){
      loading.classList.toggle('active', !!flag);
    }
    function animateProgress(ms=900){
      bar.style.width='0%';
      label.textContent='Spinning up board…';
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

    function initGame(){
      state = { grid:Array(9).fill(null), turn:'X', winner:null, line:[], moves:0 };
      boardEl.classList.remove('win');
      boardEl.querySelector('.ribbon')?.classList.remove('visible');
      boardEl.querySelectorAll('.cell').forEach(c=>c.remove());
      for(let i=0;i<9;i++){
        const cell=document.createElement('button');
        cell.className='cell';
        cell.setAttribute('aria-label', `Cell ${i+1}`);
        cell.dataset.idx=i;
        cell.addEventListener('click', onMove);
        boardEl.appendChild(cell);
      }
      updateStatus();
    }
    function onMove(e){
      if(state.winner) return;
      const i = Number(e.currentTarget.dataset.idx);
      if(state.grid[i]) return;
      state.grid[i] = state.turn;
      state.moves++;
      e.currentTarget.innerHTML = `<span class="glyph ${state.turn.toLowerCase()}">${state.turn}</span>`;
      e.currentTarget.classList.add('played');

      const win = checkWin(state.grid);
      if(win){
        state.winner = state.turn;
        state.line = win.line;
        highlightWin(win.line);
        statusEl.textContent = `${state.turn} wins`;
        scores[state.turn]++; renderScores();
        celebrate();
        return;
      }
      if(state.moves === 9){
        statusEl.textContent = 'Draw';
        scores.D++; renderScores();
        return;
      }
      state.turn = state.turn === 'X' ? 'O' : 'X';
      updateStatus();
    }
    function updateStatus(){
      statusEl.textContent = `${state.turn} to move`;
      statusEl.style.borderColor = state.turn==='X' ? 'rgba(122,162,247,.25)' : 'rgba(201,210,126,.25)';
      statusEl.style.boxShadow = state.turn==='X'
        ? '0 0 12px rgba(122,162,247,.18) inset'
        : '0 0 12px rgba(201,210,126,.18) inset';
    }
    function highlightWin(line){
      for(const i of line){
        const cell = boardEl.children[i];
        cell.classList.add('win');
      }
    }
    function celebrate(){
      boardEl.classList.add('win');
    }
    function renderScores(){
      document.getElementById('scoreX').textContent = String(scores.X);
      document.getElementById('scoreO').textContent = String(scores.O);
      document.getElementById('scoreD').textContent = String(scores.D);
    }
    function checkWin(g){
      const lines=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
      for(const line of lines){
        const [a,b,c]=line;
        if(g[a] && g[a]===g[b] && g[a]===g[c]) return {player:g[a], line};
      }
      return null;
    }

    resetBtn.addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(700);
      showLoader(false);
      initGame();
    });
    backBtn.addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(600);
      showLoader(false);
      window.location.href = 'index.html';
    });
    refreshBtn.addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(800);
      showLoader(false);
    });

    // Boot
    (async function boot(){
      showLoader(true);
      await animateProgress(900);
      showLoader(false);
      initGame(); renderScores();
    })();
  </script>
</body>
</html>
