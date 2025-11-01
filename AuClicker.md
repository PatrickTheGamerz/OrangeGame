<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Games Hub</title>
  <style>
    :root{
      --bg:#080b12; --text:#e6e6e6; --muted:#a7b0c0;
      --panel:rgba(21,24,34,.75); --border:rgba(122,162,247,.28);
      --accent:#7aa2f7; --accent2:#c28fff; --lime:#73d13d; --gold:#ffce63;
      --shadow:0 14px 42px rgba(0,0,0,.5);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; color:var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
      background:
        radial-gradient(1100px 520px at 75% -20%, #16223d 0%, var(--bg) 58%) fixed,
        radial-gradient(800px 400px at 15% 120%, #1a1f35 0%, transparent 65%) fixed;
      overflow-x:hidden;
    }
    .ambient{position:fixed; inset:-30vmax; z-index:-1; pointer-events:none;
      filter: blur(70px) saturate(130%); opacity:.35;
      animation: float 18s ease-in-out infinite;
      background:
        radial-gradient(closest-side at 40% 45%, #183061, transparent 60%),
        radial-gradient(closest-side at 70% 60%, #4b2a8b, transparent 60%),
        radial-gradient(closest-side at 30% 70%, #0f6aa8, transparent 60%);
    }
    @keyframes float{0%{transform:translate3d(0,0,0) scale(1)}50%{transform:translate3d(-2%,-1%,0) scale(1.05)}100%{transform:translate3d(0,0,0) scale(1)}}
    .topbar{display:flex; align-items:center; gap:12px; padding:14px 18px; position:sticky; top:0; z-index:3;
      backdrop-filter: blur(10px);
      background: linear-gradient(0deg, rgba(16,18,28,.55), rgba(16,18,28,.78));
      border-bottom: 1px solid rgba(122,162,247,.18);
      box-shadow: var(--shadow);}
    .brand{font-weight:800; letter-spacing:.3px;
      background: linear-gradient(90deg, var(--accent), var(--accent2));
      -webkit-background-clip:text; background-clip:text; color:transparent; user-select:none;}
    .spacer{flex:1}
    .btn{border: 1px solid var(--border);
      background: linear-gradient(180deg, #111423, #0d1120);
      color: var(--text); padding: 10px 14px; border-radius: 12px; cursor:pointer;
      transition: transform .08s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
      box-shadow: 0 6px 16px rgba(122,162,247,.1);}
    .btn:hover{border-color: rgba(122,162,247,.35);
      background: linear-gradient(180deg, #14182a, #0f1325);
      box-shadow: 0 10px 24px rgba(122,162,247,.22);
      transform: translateY(-1px);}
    h2.section{ margin:16px 20px 8px; font-size:18px; color:var(--muted); font-weight:600; user-select:none }
    .grid{display:grid; grid-template-columns:repeat(auto-fit, minmax(280px, 1fr));
      gap:20px; padding:20px;}
    .card{position:relative; border: 1px solid var(--border); border-radius: 18px;
      padding: 18px 18px 56px; background: var(--panel); backdrop-filter: blur(10px);
      box-shadow: var(--shadow); overflow:hidden; transition: transform .14s ease, box-shadow .2s ease, border-color .2s ease;}
    .card:hover{ transform: translateY(-3px); box-shadow: 0 18px 42px rgba(0,0,0,.5); border-color: rgba(122,162,247,.42) }
    .card h3{ margin:0; font-size:16px; user-select:none }
    .card p{ margin:8px 0 0; color:var(--muted); font-size:13px; line-height:1.5; user-select:none }
    .actions{ display:flex; gap:10px; margin-top:14px }
    .player-count{position:absolute; right:12px; bottom:12px; display:inline-flex; align-items:center; gap:8px;
      padding:6px 10px; border-radius:999px; border:1px solid rgba(255,255,255,.12);
      background: rgba(255,255,255,.06); color: var(--muted); font-size:12px;
      box-shadow: 0 10px 24px rgba(0,0,0,.35); backdrop-filter: blur(8px);}
    .player-dot{width:10px; height:10px; border-radius:50%;
      background: linear-gradient(180deg, var(--accent), var(--accent2));
      box-shadow: 0 0 12px rgba(122,162,247,.6);}
    .footer{ padding: 8px 20px 20px; color: var(--muted); font-size: 12px; user-select:none }
    /* Game panel */
    .game-panel{position:fixed; inset:0; z-index:20; display:none; grid-template-rows:auto 1fr;
      background: radial-gradient(1200px 600px at 75% -20%, rgba(16,20,32,.88), rgba(12,14,22,.95));
      backdrop-filter: blur(6px);}
    .game-panel.active{display:grid}
    .game-top{display:flex; align-items:center; gap:12px; padding:14px 18px; border-bottom:1px solid rgba(122,162,247,.22);
      background: linear-gradient(0deg, rgba(18,22,34,.55), rgba(16,18,28,.75)); box-shadow: var(--shadow);}
    .game-content{padding:16px; overflow:auto;}
    .cols{display:grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap:16px;}
    .panel{border: 1px solid var(--border); border-radius: 14px; padding: 14px; background: var(--panel); box-shadow: var(--shadow);}
    .stat{display:flex; justify-content:space-between; align-items:center; padding:8px 10px; border-radius:10px;
      background: rgba(255,255,255,.06); border:1px solid rgba(255,255,255,.12); margin:6px 0;}
    .big-btn{font-weight:700; border: 1px solid var(--border); background: linear-gradient(180deg, #13172a, #0d1120);
      color: var(--text); padding: 14px 18px; border-radius: 12px; cursor:pointer; width:100%;}
    .char{display:flex; justify-content:space-between; align-items:flex-start; gap:10px; padding:10px; border-radius:12px; margin:8px 0;
      background: rgba(255,255,255,.05); border: 1px solid rgba(255,255,255,.1);}
    .char .meta{ color:var(--muted); font-size:12px }
    .char .buy{min-width:96px; border: 1px solid var(--border); background: linear-gradient(180deg, #111423, #0d1120);
      color: var(--text); padding:8px 10px; border-radius: 10px; cursor:pointer;}
    .char .buy:disabled{opacity:.6; cursor:not-allowed; border-color: rgba(255,255,255,.08);}
    .log{ font-size:12px; color:var(--muted) }
  </style>
</head>
<body>
  <div class="ambient"></div>
  <header class="topbar">
    <div class="brand">
