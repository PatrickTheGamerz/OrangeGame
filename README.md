<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Games — Selector + X and O</title>
  <style>
    :root {
      --bg: #0f1115;
      --panel: #151822;
      --accent: #7aa2f7;
      --text: #e6e6e6;
      --muted: #9aa0aa;
      --border: #232638;
      --win: #73d13d;
      --lose: #f7768e;
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0;
      background: var(--bg);
      color: var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
    }

    /* App shell */
    .app {
      display: grid;
      grid-template-rows: auto 1fr;
      min-height: 100vh;
    }
    .topbar {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 12px 16px;
      border-bottom: 1px solid var(--border);
      background: linear-gradient(0deg, var(--panel), #101320);
    }
    .brand {
      font-weight: 600;
      letter-spacing: 0.2px;
    }
    .spacer { flex: 1; }
    .btn {
      border: 1px solid var(--border);
      background: #111423;
      color: var(--text);
      padding: 8px 12px;
      border-radius: 8px;
      cursor: pointer;
      transition: border-color .15s ease, transform .05s ease;
    }
    .btn:hover { border-color: #394161; }
    .btn:active { transform: translateY(1px); }

    /* Main sections */
    .view {
      display: none;
      padding: 20px;
    }
    .view.active { display: block; }

    /* Game selector */
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 16px;
    }
    .card {
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 16px;
      display: grid;
      grid-template-rows: auto 1fr auto;
      gap: 10px;
    }
    .card h3 {
      margin: 0;
      font-size: 16px;
    }
    .card p {
      margin: 0;
      color: var(--muted);
      font-size: 13px;
      line-height: 1.4;
    }
    .card .actions {
      display: flex;
      gap: 8px;
      margin-top: 10px;
    }

    /* Loading overlay */
    .loading {
      position: fixed;
      inset: 0;
      display: grid;
      place-items: center;
      background: rgba(10, 12, 18, 0.85);
      backdrop-filter: blur(2px);
      z-index: 1000;
      opacity: 0;
      pointer-events: none;
      transition: opacity .2s ease;
    }
    .loading.active {
      opacity: 1;
      pointer-events: all;
    }
    .loader {
      display: grid;
      gap: 12px;
      justify-items: center;
      padding: 24px 28px;
      border: 1px solid var(--border);
      border-radius: 12px;
      background: var(--panel);
    }
    .spinner {
      width: 32px; height: 32px;
      border: 3px solid #2a3045;
      border-top-color: var(--accent);
      border-radius: 50%;
      animation: spin 0.9s linear infinite;
    }
    @keyframes spin { to { transform: rotate(360deg); } }
    .loader-label { color: var(--muted); font-size: 12px; }

    /* Tic-tac-toe board */
    .ttt {
      display: grid;
      justify-items: center;
      gap: 16px;
    }
    .ttt-head {
      display: flex; align-items: center; gap: 10px;
    }
    .status {
      font-size: 14px; color: var(--muted);
    }
    .board {
      display: grid;
      grid-template-columns: repeat(3, 80px);
      grid-template-rows: repeat(3, 80px);
      gap: 8px;
    }
    .cell {
      width: 80px; height: 80px;
      display: grid; place-items: center;
      font-size: 34px; font-weight: 700;
      border: 1px solid var(--border);
      border-radius: 10px;
      background: #121522;
      cursor: pointer;
      transition: background .15s ease, border-color .15s ease, transform .05s ease;
      user-select: none;
    }
    .cell:hover { border-color: #3a4263; background: #151930; }
    .cell:active { transform: translateY(1px); }
    .cell.win { outline: 2px solid var(--win); }
    .legend {
      display: flex; gap: 14px; align-items: center;
      color: var(--muted); font-size: 12px;
    }
    .badge {
      padding: 2px 8px; border-radius: 999px;
      border: 1px solid var(--border); background: #101423;
      color: var(--text); font-size: 12px;
    }
    .bar {
      display: flex; gap: 8px;
    }
    .meta {
      display: flex; gap: 16px; align-items: center; color: var(--muted);
      font-size: 12px;
    }
    .score {
      display: inline-flex; gap: 8px; align-items: center;
    }
    .score .x { color: var(--accent); }
    .score .o { color: #c9d27e; }

    /* Utility */
    .hide { display: none !important; }
  </style>
</head>
<body>
  <div class="app">
    <header class="topbar">
      <div class="brand">Games</div>
      <div class="spacer"></div>
      <button class="btn" data-nav="selector">Select game</button>
    </header>

    <!-- Selector view -->
    <main id="selector" class="view active">
      <h2 style="margin:0 0 12px 0; font-size:18px;">Choose a game</h2>
      <div class="grid">
        <article class="card">
          <h3>X and O (Tic‑Tac‑Toe)</h3>
          <p>Classic 3×3. Two players: X starts, O follows. First to align three wins.</p>
          <div class="actions">
            <button class="btn" data-load="tic-tac-toe">Load</button>
            <button class="btn" data-open="tic-tac-toe">Play</button>
          </div>
        </article>

        <!-- Future game cards can be added here -->
        <article class="card">
          <h3>Coming soon</h3>
          <p>Add new cards here. The loader + navigation already works.</p>
          <div class="actions">
            <button class="btn" disabled>Load</button>
            <button class="btn" disabled>Play</button>
          </div>
        </article>
      </div>
    </main>

    <!-- Tic-tac-toe view -->
    <section id="tic-tac-toe" class="view">
      <div class="ttt">
        <div class="ttt-head">
          <h2 style="margin:0; font-size:18px;">X and O</h2>
          <span class="status" id="status">X to move</span>
        </div>

        <div class="legend">
          <span class="badge">3×3 grid</span>
          <span class="badge">3 in a row wins</span>
          <span class="badge">X starts</span>
        </div>

        <div class="board" id="board" aria-label="Tic-tac-toe board"></div>

        <div class="bar">
          <button class="btn" id="reset">Reset</button>
          <button class="btn" id="back">Back to selector</button>
        </div>

        <div class="meta">
          <div class="score">
            <span class="x">X wins:</span><span id="scoreX">0</span>
            <span class="o" style="margin-left:10px;">O wins:</span><span id="scoreO">0</span>
            <span style="margin-left:10px;">Draws:</span><span id="scoreD">0</span>
          </div>
        </div>
      </div>
    </section>
  </div>

  <!-- Loading overlay -->
  <div id="loading" class="loading" aria-live="polite" aria-busy="true">
    <div class="loader">
      <div class="spinner" aria-hidden="true"></div>
      <div class="loader-label">Loading game…</div>
    </div>
  </div>

  <script>
    // Navigation + loader
    const views = {
      selector: document.getElementById('selector'),
      ttt: document.getElementById('tic-tac-toe'),
    };
    const loading = document.getElementById('loading');

    function showView(key) {
      for (const v of Object.values(views)) v.classList.remove('active');
      (key === 'selector' ? views.selector : views.ttt).classList.add('active');
    }
    function showLoader(flag) {
      loading.classList.toggle('active', !!flag);
    }
    function fakeLoad(ms = 700) {
      return new Promise(res => setTimeout(res, ms));
    }

    // Topbar nav
    document.querySelector('[data-nav="selector"]').addEventListener('click', () => showView('selector'));

    // Card actions
    document.querySelector('[data-load="tic-tac-toe"]').addEventListener('click', async () => {
      showLoader(true);
      await fakeLoad(900);
      showLoader(false);
    });
    document.querySelector('[data-open="tic-tac-toe"]').addEventListener('click', async () => {
      showLoader(true);
      await fakeLoad(600);
      showLoader(false);
      showView('ttt');
    });

    // Tic-tac-toe logic
    const boardEl = document.getElementById('board');
    const statusEl = document.getElementById('status');
    const resetBtn = document.getElementById('reset');
    const backBtn = document.getElementById('back');

    let state, scores = { X: 0, O: 0, D: 0 };

    function initGame() {
      state = {
        grid: Array(9).fill(null),
        turn: 'X',
        winner: null,
        line: [],
        moves: 0,
      };
      boardEl.innerHTML = '';
      for (let i = 0; i < 9; i++) {
        const cell = document.createElement('button');
        cell.className = 'cell';
        cell.setAttribute('aria-label', `Cell ${i + 1}`);
        cell.dataset.idx = i;
        cell.addEventListener('click', onMove);
        boardEl.appendChild(cell);
      }
      updateStatus();
    }

    function onMove(e) {
      if (state.winner) return;
      const i = Number(e.currentTarget.dataset.idx);
      if (state.grid[i]) return;
      state.grid[i] = state.turn;
      state.moves++;
      e.currentTarget.textContent = state.turn;

      const win = checkWin(state.grid);
      if (win) {
        state.winner = state.turn;
        state.line = win.line;
        highlightWin(win.line);
        statusEl.textContent = `${state.turn} wins`;
        scores[state.turn]++; renderScores();
        return;
      }
      if (state.moves === 9) {
        statusEl.textContent = 'Draw';
        scores.D++; renderScores();
        return;
      }
      state.turn = state.turn === 'X' ? 'O' : 'X';
      updateStatus();
    }

    function updateStatus() {
      statusEl.textContent = `${state.turn} to move`;
    }

    function highlightWin(line) {
      for (const i of line) {
        const cell = boardEl.children[i];
        cell.classList.add('win');
      }
    }

    function renderScores() {
      document.getElementById('scoreX').textContent = String(scores.X);
      document.getElementById('scoreO').textContent = String(scores.O);
      document.getElementById('scoreD').textContent = String(scores.D);
    }

    function resetBoard() {
      initGame();
    }

    function checkWin(g) {
      const lines = [
        [0,1,2], [3,4,5], [6,7,8],
        [0,3,6], [1,4,7], [2,5,8],
        [0,4,8], [2,4,6],
      ];
      for (const line of lines) {
        const [a,b,c] = line;
        if (g[a] && g[a] === g[b] && g[a] === g[c]) {
          return { player: g[a], line };
        }
      }
      return null;
    }

    resetBtn.addEventListener('click', resetBoard);
    backBtn.addEventListener('click', () => {
      showView('selector');
    });

    // Boot
    initGame();
    renderScores();
  </script>
</body>
</html>
```
