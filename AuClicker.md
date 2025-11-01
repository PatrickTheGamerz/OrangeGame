<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Games Hub</title>
<style>
  :root{
    --bg:#080b12; --text:#e6e6e6; --muted:#a7b0c0; --panel:rgba(21,24,34,.75);
    --border:rgba(122,162,247,.28); --accent:#7aa2f7; --accent2:#c28fff;
    --lime:#73d13d; --gold:#ffce63; --shadow:0 14px 42px rgba(0,0,0,.5);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0; color:var(--text);
    font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
    background: radial-gradient(1100px 520px at 75% -20%, #16223d 0%, var(--bg) 58%) fixed,
                radial-gradient(800px 400px at 15% 120%, #1a1f35 0%, transparent 65%) fixed;
    overflow-x:hidden;
  }
  .ambient{
    position:fixed; inset:-30vmax; z-index:-1; pointer-events:none;
    filter: blur(70px) saturate(130%); opacity:.35; animation: float 18s ease-in-out infinite;
    background:
      radial-gradient(closest-side at 40% 45%, #183061, transparent 60%),
      radial-gradient(closest-side at 70% 60%, #4b2a8b, transparent 60%),
      radial-gradient(closest-side at 30% 70%, #0f6aa8, transparent 60%);
  }
  @keyframes float{
    0%{transform:translate3d(0,0,0) scale(1)}
    50%{transform:translate3d(-2%,-1%,0) scale(1.05)}
    100%{transform:translate3d(0,0,0) scale(1)}
  }
  .topbar{
    display:flex; align-items:center; gap:12px; padding:14px 18px; position:sticky; top:0; z-index:3;
    backdrop-filter: blur(10px);
    background: linear-gradient(0deg, rgba(16,18,28,.55), rgba(16,18,28,.78));
    border-bottom: 1px solid rgba(122,162,247,.18); box-shadow: var(--shadow);
  }
  .brand{
    font-weight:800; letter-spacing:.3px;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    -webkit-background-clip:text; background-clip:text; color:transparent; user-select:none;
  }
  .spacer{flex:1}
  .btn{
    border: 1px solid var(--border); background: linear-gradient(180deg, #111423, #0d1120); color: var(--text);
    padding: 10px 14px; border-radius: 12px; cursor:pointer;
    transition: transform .08s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
    box-shadow: 0 6px 16px rgba(122,162,247,.1);
  }
  .btn:hover{
    border-color: rgba(122,162,247,.35); background: linear-gradient(180deg, #14182a, #0f1325);
    box-shadow: 0 10px 24px rgba(122,162,247,.22); transform: translateY(-1px);
  }
  h2.section{ margin:16px 20px 8px; font-size:18px; color:var(--muted); font-weight:600; user-select:none }

  /* Grid for other games (e.g., TicTacToe) */
  .grid{ display:grid; grid-template-columns:repeat(auto-fit, minmax(280px, 1fr)); gap:20px; padding:20px; }

  .card{
    position:relative; border: 1px solid var(--border); border-radius: 18px; padding: 18px 18px 56px;
    background: var(--panel); backdrop-filter: blur(10px); box-shadow: var(--shadow); overflow:hidden;
    transition: transform .14s ease, box-shadow .2s ease, border-color .2s ease;
  }
  .card::before{
    content:""; position:absolute; inset:-1px; border-radius: 18px;
    background: conic-gradient(from 0deg,
      rgba(122,162,247,.22), rgba(194,143,255,.22), rgba(106,227,189,.22), rgba(122,162,247,.22));
    opacity:.0; pointer-events:none; transition: opacity .28s ease; mix-blend-mode: screen;
  }
  .card:hover{ transform: translateY(-3px); box-shadow: 0 18px 42px rgba(0,0,0,.5); border-color: rgba(122,162,247,.42) }
  .card:hover::before{ opacity:.6 }
  .card h3{ margin:0; font-size:16px; user-select:none }
  .card p{ margin:8px 0 0; color:var(--muted); font-size:13px; line-height:1.5; user-select:none }
  .actions{ display:flex; gap:10px; margin-top:14px }
  .player-count{
    position:absolute; right:12px; bottom:12px; display:inline-flex; align-items:center; gap:8px;
    padding:6px 10px; border-radius:999px; border:1px solid rgba(255,255,255,.12); background: rgba(255,255,255,.06);
    color: var(--muted); font-size:12px; box-shadow: 0 10px 24px rgba(0,0,0,.35); backdrop-filter: blur(8px);
  }
  .player-dot{
    width:10px; height:10px; border-radius:50%;
    background: linear-gradient(180deg, var(--accent), var(--accent2));
    box-shadow: 0 0 12px rgba(122,162,247,.6);
  }

  /* Fullscreen loader */
  .loading{
    position:fixed; inset:0; display:grid; place-items:center;
    background: radial-gradient(800px 400px at 50% 30%, rgba(20,24,34,.88), rgba(10,12,18,.95));
    backdrop-filter: blur(6px); z-index:1000; opacity:0; pointer-events:none; transition: opacity .28s ease;
  }
  .loading.active{ opacity:1; pointer-events:auto }
  .loader{
    display:grid; gap:12px; justify-items:center; padding: 26px 28px;
    border: 1px solid rgba(122,162,247,.28); border-radius: 16px;
    background: linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88)); box-shadow: var(--shadow);
  }
  .ring{
    width:64px; height:64px; border-radius:50%;
    background: conic-gradient(from 0deg, var(--accent) 0% 30%, var(--accent2) 30% 60%, #6ae3bd 60% 85%, transparent 85% 100%);
    -webkit-mask: radial-gradient(closest-side, transparent 66%, black 67%);
    mask: radial-gradient(closest-side, transparent 66%, black 67%); animation: spin .9s linear infinite;
  }
  @keyframes spin{ to{ transform: rotate(360deg) } }
  .progress{
    width:240px; height:8px; border-radius:999px; border:1px solid rgba(255,255,255,.1);
    background: rgba(255,255,255,.06); overflow:hidden
  }
  .bar{ height:100%; background: linear-gradient(90deg, var(--accent), var(--accent2)); width:0%; transition: width .28s ease }
  .loader-label{ font-size:12px; color:var(--muted) }

  .footer{ padding: 8px 20px 20px; color: var(--muted); font-size: 12px; user-select:none }

  /* Centered AU Clicker section */
  .center-wrapper{
    display:flex;
    justify-content:center;
    align-items:center;
    min-height: calc(100vh - 160px); /* viewport height minus topbar + footer */
    padding:20px;
  }
  .center-wrapper .card{
    max-width: 440px;
    width: 100%;
  }
</style>
</head>
<body>
  <div class="ambient"></div>

  <header class="topbar">
    <div class="brand">Games Hub</div>
    <div class="spacer"></div>
    <button class="btn" id="refresh">Refresh</button>
  </header>

  <h2 class="section">Choose a game</h2>
  <main class="grid" aria-label="Game list">
    <article class="card" aria-labelledby="ttt-title">
      <h3 id="ttt-title">X and O (Tic‑Tac‑Toe)</h3>
      <p>Classic 3×3. Two players: X starts, O follows. First to align three wins.</p>
      <div class="actions">
        <button class="btn" id="play-ttt">Play</button>
      </div>
      <div class="player-count" aria-label="Players online for Tic‑Tac‑Toe">
        <span class="player-dot" aria-hidden="true"></span>
        <span id="tttCount">Players online: 0</span>
      </div>
    </article>
  </main>

  <h2 class="section">AU Clicker</h2>
  <section class="center-wrapper" aria-label="AU Clicker centered">
    <article class="card" aria-labelledby="au-title">
      <h3 id="au-title">AU Clicker</h3>
      <p>Incremental clicker game. Click to gain AU, unlock upgrades, and progress.</p>
      <div class="actions">
        <button class="btn" id="play-au">Play</button>
      </div>
      <div class="player-count" aria-label="Players online for AU Clicker">
        <span class="player-dot" aria-hidden="true"></span>
        <span id="auCount">Players online: 0</span>
      </div>
    </article>
  </section>

  <div class="footer">Tip: Load preps the game with that sweet glow. Play jumps right in.</div>

  <div id="loading" class="loading" aria-live="polite" aria-busy="true">
    <div class="loader">
      <div class="ring" aria-hidden="true"></div>
      <div class="progress"><div class="bar" id="bar"></div></div>
      <div class="loader-label" id="loaderLabel">Preparing assets…</div>
    </div>
  </div>

  <script>
    const loading = document.getElementById('loading');
    const bar = document.getElementById('bar');
    const label = document.getElementById('loaderLabel');

    function showLoader(flag){
      loading.classList.toggle('active', !!flag);
    }

    function animateProgress(ms=1200){
      bar.style.width='0%';
      label.textContent='Preparing assets…';
      let p=0;
      const step=()=>{
        p=Math.min(100, p + Math.random()*24 + 6);
        bar.style.width = p.toFixed(0)+'%';
        if(p<35) label.textContent='Warming up shaders…';
        else if(p<65) label.textContent='Linking modules…';
        else if(p<90) label.textContent='Polishing UI…';
        else label.textContent='Almost there…';
        if(p<100) requestAnimationFrame(step);
      };
      requestAnimationFrame(step);
      return new Promise(res=>setTimeout(res, ms));
    }

    // Real player count: replace URL with your backend endpoint returning {count:number}
    async function updateCounts(){
      try{
        const res = await fetch('/api/playercount/tictactoe', {cache:'no-store'});
        if(res.ok){
          const data = await res.json();
          document.getElementById('tttCount').textContent = 'Players online: ' + (data.count ?? 0);
        }
      }catch(e){
        // Silent fail if backend not available
      }

      try{
        const res2 = await fetch('/api/playercount/auclicker', {cache:'no-store'});
        if(res2.ok){
          const data2 = await res2.json();
          document.getElementById('auCount').textContent = 'Players online: ' + (data2.count ?? 0);
        }
      }catch(e){
        // Silent fail
      }
    }

    setInterval(updateCounts, 3000);
    updateCounts();

    document.getElementById('refresh').addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(1000);
      showLoader(false);
    });

    document.getElementById('play-ttt').addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(900);
      showLoader(false);
      window.location.href = 'TicTacToe.html';
    });

    document.getElementById('play-au').addEventListener('click', async ()=>{
      showLoader(true);
      await animateProgress(900);
      showLoader(false);
      window.location.href = 'AuClicker.html';
    });
  </script>
</body>
</html>
