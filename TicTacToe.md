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
    --win:#73d13d; --danger:#f7768e; --gold:#ffce63; --lime:#32cd32;
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
  .noselect{ user-select:none; -webkit-user-select:none; -ms-user-select:none }
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
  .glyph{ position:relative; display:inline-block; animation: glyphIn .24s ease-out }
  @keyframes glyphIn{ 0%{ transform:translateY(6px) scale(.94); opacity:.0 } 100%{ transform:translateY(0) scale(1); opacity:1 } }
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

  .beam{
    position:absolute; height:4px; background: linear-gradient(90deg, rgba(115,209,61,0), var(--win), rgba(115,209,61,0));
    box-shadow: 0 0 14px rgba(115,209,61,.8);
    transform-origin:left center;
    animation: beamDraw 1.1s ease-out forwards, beamFade 1.8s ease-in 1.1s forwards;
    pointer-events:none; z-index:3;
  }
  @keyframes beamDraw{ from{ width:0; opacity:.0 } to{ width:100%; opacity:1 } }
  @keyframes beamFade{ from{ opacity:1 } to{ opacity:.0 } }

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

  .cardsTray{
    position:fixed; left:50%; transform:translateX(-50%);
    bottom:16px; display:none; gap:10px; padding:10px 12px;
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

  .effectOverlay{
    position:fixed; inset:0; display:none; place-items:center; z-index:1002; pointer-events:none;
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
  .effectDesc{ font-size:12px; color:var(--muted); text-align:center; max-width:360px }

  .turnOverlay{
    position:fixed; inset:0; display:grid; place-items:center; z-index:1003;
    pointer-events:none; opacity:0; transition: opacity .18s ease;
    background: radial-gradient(900px 500px at 50% 40%, rgba(10,12,18,.25), rgba(6,8,12,.45));
  }
  .turnOverlay.active{ opacity:1 }
  .turnBubble{
    display:grid; gap:6px; justify-items:center;
    padding:12px 16px; border-radius:12px; color:#fff;
    border:1px solid rgba(255,255,255,.22);
    background: linear-gradient(180deg, rgba(16,20,32,.86), rgba(14,18,30,.92));
    box-shadow: 0 10px 34px rgba(0,0,0,.55);
    animation: effectPop .18s ease-out;
  }
  .turnTitle{ font-weight:800; letter-spacing:.2px }
  .turnDesc{ font-size:12px; color:var(--muted) }

  .fogMask{
    position:absolute; inset:0; backdrop-filter: blur(10px) brightness(0.6);
    background: radial-gradient(800px 400px at 50% 30%, rgba(10,12,18,.65), rgba(6,8,12,.85));
    pointer-events:none; z-index:2; opacity:0; transition: opacity .25s ease;
  }
  .fogMask.active{ opacity:1 }

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
    -webkit-mask: radial-gradient(closest-side, transparent 66%, black 67%); mask: radial-gradient(closest-side, transparent 66%); 
    animation: spin .9s linear infinite;
  }
  @keyframes spin{ to{ transform: rotate(360deg) } }
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

  .piece{
    position:absolute; inset:auto auto 6px 6px; z-index:3;
    font-size:12px; padding:2px 6px; border-radius:8px;
    border:1px solid rgba(255,255,255,.18);
    background: rgba(16,20,32,.88); color:#fff;
    box-shadow: 0 6px 18px rgba(0,0,0,.4);
    animation: pieceIn .2s ease-out;
  }
  @keyframes pieceIn{ from{ transform:translateY(6px); opacity:.0 } to{ transform:translateY(0); opacity:1 } }
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
      <div class="fogMask" id="fogMask"></div>
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

  <div class="cardsTray" id="cardsTray" style="display:none"></div>

  <div class="effectOverlay" id="effectOverlay" style="display:none">
    <div class="effectBubble" id="effectBubble">
      <div class="effectTitle" id="effectTitle"></div>
      <div class="effectDesc" id="effectDesc"></div>
    </div>
  </div>

  <div class="turnOverlay" id="turnOverlay">
    <div class="turnBubble">
      <div class="turnTitle" id="turnTitle"></div>
      <div class="turnDesc" id="turnDesc"></div>
    </div>
  </div>

  <div id="loading" class="loading" aria-live="polite" aria-busy="true">
    <div class="loader">
      <div class="ring" aria-hidden="true"></div>
      <div class="progress"><div class="bar" id="bar"></div></div>
      <div class="loader-label noselect" id="loaderLabel">Spinning up board…</div>
    </div>
  </div>

  <div id="modal" class="modal" aria-modal="true" role="dialog">
    <div class="sheet">
      <h3 class="noselect">Game setup</h3>

      <div class="group">
        <div class="pill noselect">Opponent</div>
        <select class="select" id="opponent">
          <option value="player">Local players</option>
          <option value="bot">Bots</option>
        </select>
      </div>

      <div class="group" id="botRow" style="display:none">
        <div class="pill noselect">Difficulty: <span id="difficultyLabel">2.5</span></div>
        <div class="pill noselect" id="difficultyNote">Baseline Extreme</div>
        <input class="range" id="difficulty" type="range" min="1" max="5" step="0.1" value="2.5"/>
      </div>

      <div class="group">
        <div class="pill noselect">Gamemode</div>
        <select class="select" id="mode">
          <option value="casual">Casual (normal tic‑tac‑toe)</option>
          <option value="three">Triad Clash (three players)</option>
          <option value="four">Quad Arena (four players)</option>
          <option value="blitz">Blitz (turn timer)</option>
          <option value="randomizer">Chaos Deck (cards & random events)</option>
          <option value="mine">Minefield (hidden mines)</option>
        </select>
      </div>

      <div class="group">
        <div class="pill noselect">Players</div>
        <div class="row-flex">
          <div id="playersRow" class="row-flex">
            <select class="select" id="playerCount">
              <option value="2">2 players</option>
              <option value="3">3 players</option>
              <option value="4">4 players</option>
            </select>
          </div>
          <div id="blitzRow" class="row-flex" style="display:none">
            <div class="pill noselect">Blitz base (5–20s)</div>
            <input class="range" id="blitzSec" type="range" min="5" max="20" step="1" value="10" />
            <div class="pill noselect"><span id="blitzLabel">10s</span></div>
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
const difficultyNote = document.getElementById('difficultyNote');

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

const fogMask = document.getElementById('fogMask');

const turnOverlay = document.getElementById('turnOverlay');
const turnTitle = document.getElementById('turnTitle');
const turnDesc = document.getElementById('turnDesc');

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
let eliminated = new Set();
let perPlayerTimer = new Map();
let fogOwner = null;
let fogRoundsLeft = 0;
let revealedMinesRounds = 0;
let revealedMinesOwner = null;

// Mafia night state
let mafiaOwner = null;
let mafiaActive = false;

// Piece control (owner-only immediate move)
let pieceControl = { active:false, owner:null, piece:null, moves:0 };

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

// Modal behavior
function openSetup(){ modal.classList.add('active'); updateSetupLabels(); applyFieldVisibility(); }
function closeSetup(){ modal.classList.remove('active'); }
function updateSetupLabels(){
  difficultyLabel.textContent = difficultyRange.value;
  blitzLabel.textContent = blitzRange.value + 's';
  difficultyNote.textContent = etoHLabel(parseFloat(difficultyRange.value));
}
function etoHLabel(v){
  // illustrative mapping (EToH style labels)
  if (v < 1.5) return 'Bottom-Low Hard';
  if (v < 2.5) return 'Baseline Extreme';
  if (v < 3.5) return 'High-Peak Easy';
  if (v < 4.5) return 'Upper Crest';
  return 'Apex Mastery';
}
function applyFieldVisibility(){
  botRow.style.display = opponentSel.value==='bot' ? 'grid' : 'none';
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
  return config.players === 4 ? 6 : config.players === 3 ? 4 : 3;
}

// Turn prompt
function showTurnPrompt(player){
  const isHuman = !(config.opponent==='bot' && player!==state.order[0]);
  const title = isHuman ? 'Your Turn' : 'Bot Turn';
  const desc = isHuman ? `${player} — place your mark or use a card`
                       : `Bot is thinking…`;
  turnTitle.textContent = title;
  turnDesc.textContent = desc;
  turnOverlay.classList.add('active');
  setTimeout(()=>turnOverlay.classList.remove('active'), 900);
}

function initGame(fromSetup=false){
  const size = getBoardSize();
  eliminated.clear();
  fogOwner = null; fogRoundsLeft = 0;
  revealedMinesRounds = 0; revealedMinesOwner = null;
  perPlayerTimer.clear();
  mafiaOwner = null; mafiaActive = false;
  pieceControl = { active:false, owner:null, piece:null, moves:0 };

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
    hands: new Map(),
    skip: {},
    shields: new Set(),
    extraTurns: 0,
    playBlockedOnce: false,
    pieces: []
  };

  for(let m=0;m<mineCount();m++){
    let idx;
    do{ idx = randomInt(0, size*size-1) } while(state.mines.has(idx));
    state.mines.add(idx);
  }

  buildDeck();
  dealStartingHands();

  boardEl.classList.remove('win');
  boardEl.innerHTML = '<div class="ribbon" aria-hidden="true"></div><div class="fogMask" id="fogMask"></div>';
  boardEl.style.gridTemplateColumns = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(540/size))}px))`;
  boardEl.style.gridTemplateRows = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(540/size))}px))`;

  for(let i=0;i<size*size;i++){
    const cell=document.createElement('button');
    cell.className='cell';
    cell.setAttribute('aria-label', `Cell ${i+1}`);
    cell.dataset.idx=i;
    cell.addEventListener('click', onCellClick);
    boardEl.appendChild(cell);
  }

  updateStatus();
  updateCardsTray();

  if (config.mode==='blitz'){
    baseTimer = config.blitzSec;
    for(const p of state.order){ perPlayerTimer.set(p, baseTimer); }
    startTurnTimer(perPlayerTimer.get(currentPlayer()));
  } else {
    stopTurnTimer(); timerBox.style.display='none';
  }

  if (!fromSetup) renderScores();
  showTurnPrompt(currentPlayer());
  maybeBotFirstMove();
}

function getTurnOrder(players){
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

// Cards
const Rarity = { COMMON:'common', RARE:'rare', EPIC:'epic', LEGEND:'legend', MYTH:'myth' };
function card(name, rarity, desc, apply){ return { name, rarity, desc, apply }; }

function buildDeck(){
  const cards = [
    // COMMON
    card('+1 Skip', Rarity.COMMON, 'Target skips next turn', (ctx)=>skipTarget(ctx.target,1)),
    card('Block', Rarity.COMMON, 'Block a chosen empty cell', (ctx)=>blockChosenCell(ctx)),
    card('Clear', Rarity.COMMON, 'Clear a chosen mark', (ctx)=>clearChosenMark(ctx)),
    card('Swap Order', Rarity.COMMON, 'Reverse turn order', ()=>reverseTurnOrder()),
    card('Peek Mine', Rarity.COMMON, 'Reveal if a chosen cell is a mine', (ctx)=>peekMine(ctx)),
    card('Fog', Rarity.COMMON, 'Global fog for 2 rounds (only you see)', (ctx)=>applyFog(currentPlayer(),2)),
    card('Unfog', Rarity.COMMON, 'Remove fog from all cells', ()=>unfogBoard(true)),
    card('+Timer', Rarity.COMMON, '+2s to your timer (Blitz)', ()=>boostTimerFor(currentPlayer(),+2)),
    card('-Timer', Rarity.COMMON, '-2s to next player timer (Blitz)', ()=>drainNextTimer(2)),
    card('Reveal Mine Hint', Rarity.COMMON, 'Pulse a mine area', ()=>pulseMineTease()),

    // RARE
    card('+2 Skip', Rarity.RARE, 'Target skips next 2 turns', (ctx)=>skipTarget(ctx.target,2)),
    card('Double Block', Rarity.RARE, 'Block two chosen empty cells', (ctx)=>blockTwo(ctx)),
    card('Undo', Rarity.RARE, 'Undo last move', ()=>undoLastMove()),
    card('Disarm', Rarity.RARE, 'Remove one random mine', ()=>removeRandomMine()),
    card('Plant', Rarity.RARE, 'Add one random mine', ()=>addRandomMine()),
    card('Clone', Rarity.RARE, 'Play two marks this turn (adjacent)', ()=>clonePlay()),
    card('Boost Draw', Rarity.RARE, 'Draw 1 extra card now', ()=>drawFor(currentPlayer(),1)),
    card('Reveal Mines', Rarity.RARE, 'Show all mines for 2 rounds (you only)', ()=>revealMines(currentPlayer(),2)),
    card('Swap Mine', Rarity.RARE, 'Move a mine to a chosen empty cell', (ctx)=>swapMineToEmpty()),

    // EPIC
    card('Teleport', Rarity.EPIC, 'Move one of your marks to another cell', (ctx)=>teleportMark(ctx)),
    card('Shield', Rarity.EPIC, 'Protect one of your marks from clear/block', (ctx)=>shieldMark(ctx)),
    card('Accelerate', Rarity.EPIC, 'You play again after this turn', ()=>extraTurn()),
    card('Bridge', Rarity.EPIC, 'Connect two of your marks as a wildcard (visual)', (ctx)=>bridgeMarks(ctx)),
    card('Expand', Rarity.EPIC, 'Increase board size by 1 (adds layer)', ()=>expandBoard()),
    card('Cover Zone', Rarity.EPIC, 'Cover a 2x2 zone temporarily', ()=>coverZone(2, randomInt(1,4))),
    card('Knight Strike', Rarity.EPIC, 'Place a knight; removes marks like chess L for 2 rounds', ()=>spawnPiece('Knight', 2)),
    card('Bishop Sweep', Rarity.EPIC, 'Place a bishop; clears diagonals for 3 rounds', ()=>spawnPiece('Bishop', 3)),
    card('Swap Hands', Rarity.EPIC, 'Swap your hand with another player', (ctx)=>swapHands(ctx)),

    // LEGEND
    card('Storm', Rarity.LEGEND, 'Clear 3 random opponent marks', ()=>clearRandomOpponentMarks(3)),
    card('Quake', Rarity.LEGEND, 'Block 4 random empty cells', ()=>blockRandomCells(4)),
    card('Time Warp', Rarity.LEGEND, 'Reset all timers baseline (Blitz)', ()=>resetTimers()),
    card('Specter', Rarity.LEGEND, 'Play on a blocked cell (once)', (ctx)=>playOnBlocked(ctx)),
    card('Rook Wall', Rarity.LEGEND, 'Place a rook; clears ranks/files for 2 rounds', ()=>spawnPiece('Rook', 2)),
    card('Checkers Sweep', Rarity.LEGEND, 'Place checkers piece; clears adjacent diagonals for 2 rounds', ()=>spawnPiece('Checker', 2)),
    card('Zone Freeze', Rarity.LEGEND, 'Freeze a 3x3 area (no plays) 2 rounds', ()=>coverZone(3,2)),
    card('Expand+', Rarity.LEGEND, 'Increase board size by 2', ()=>expandBoard(2)),
    card('Morph', Rarity.LEGEND, 'Change one of your marks into another symbol', (ctx)=>morphMark(ctx)),
    card('Blitz Surge', Rarity.LEGEND, 'Enable Blitz with random timers 12–20s', ()=>blitzSurge()),

    // MYTH
    card('M87', Rarity.MYTH, 'Black hole resets the board and restarts the round', ()=>m87Reset()),
    card('Singularity', Rarity.MYTH, 'Absorb all cards; everyone discards hand', ()=>absorbCards()),
    card('Nova', Rarity.MYTH, 'Add glow; next win highlights across board', ()=>novaGlow()),
    card('Anomaly', Rarity.MYTH, 'Random powerful effect', ()=>randomPower()),
    card('Mafia Night', Rarity.MYTH, 'Secret role phase to eliminate a player/mark', ()=>startMafiaNight()),
    card('UNO Reverse', Rarity.MYTH, 'Reverse order and force next to draw 2 cards', ()=>unoReverse()),
    card('Chess Rain', Rarity.MYTH, 'Spawn random chess pieces for 1–4 rounds', ()=>chessRain()),
    card('Swap Identity', Rarity.MYTH, 'Swap your symbol with another player', (ctx)=>swapIdentity(ctx))
  ];

  // variants/intensities + extras
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
    card('Reveal Mines+', Rarity.EPIC, 'Show mines 3 rounds', ()=>revealMines(currentPlayer(),3)),
    card('Swap Mine+', Rarity.LEGEND, 'Swap two mines into empty cells', ()=>swapTwoMines()),
    card('Expand++', Rarity.MYTH, 'Increase board size by 3', ()=>expandBoard(3)),
    card('Fog+', Rarity.RARE, 'Global fog 3 rounds (you only)', ()=>applyFog(currentPlayer(),3)),
    card('Cover Line', Rarity.EPIC, 'Cover full row/column 2 rounds', ()=>coverLine(2)),
    card('Knight+', Rarity.LEGEND, 'Knight lasts 4 rounds', ()=>spawnPiece('Knight',4)),
    card('Bishop+', Rarity.LEGEND, 'Bishop lasts 4 rounds', ()=>spawnPiece('Bishop',4)),
    card('Rook+', Rarity.LEGEND, 'Rook lasts 4 rounds', ()=>spawnPiece('Rook',4)),
    card('Checker+', Rarity.LEGEND, 'Checker lasts 3 rounds', ()=>spawnPiece('Checker',3)),
    card('Time Slice', Rarity.EPIC, 'Set your timer to next higher integer', ()=>ceilYourTimer()),
    card('Drain Slice', Rarity.EPIC, 'Set next timer to lower integer', ()=>floorNextTimer()),
    card('Wild Draw', Rarity.RARE, 'Draw 2 cards', ()=>drawFor(currentPlayer(),2)),
    card('Mass Shield', Rarity.LEGEND, 'Shield 4 chosen marks', ()=>shieldMultiple(4)),
    card('Purge', Rarity.LEGEND, 'Clear all unshielded marks in a 3x3', ()=>purgeArea(3)),
    card('Pulse Mines', Rarity.RARE, 'Pulse outlines of mine cells for 2s', ()=>pulseAllMines()),
    card('Meteor Strike', Rarity.LEGEND, 'Random 5 cells cleared', ()=>meteorStrike(5)),
    card('Time Freeze', Rarity.EPIC, 'Pause timers for 3s (Blitz)', ()=>timeFreeze(3)),
    card('Mine Shuffle', Rarity.RARE, 'Randomly relocate all mines', ()=>shuffleMines()),
    card('Fog Storm', Rarity.EPIC, 'Fog + reveal mines for owner 2 rounds', ()=>{applyFog(currentPlayer(),2); revealMines(currentPlayer(),2);})
  ]);
  shuffle(state.deck);
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
  if(hand.length>10){
    eliminatePlayer(p,"Too many cards (>10)");
    return;
  }
  updateCardsTray();
}
function shuffle(arr){
  for(let i=arr.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]];
  }
}

// Cards tray
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
      if (isBotTurn()) return;
      const target = await chooseTargetIfNeeded(c.name);
      playCardEffect(c, target);
      hand.splice(idx,1);
      state.hands.set(p, hand);
      updateCardsTray();
      if (!state.extraTurns || state.extraTurns<=0) advanceTurn();
      else state.extraTurns--;
    });
    cardsTray.appendChild(el);
  });
}

async function chooseTargetIfNeeded(cardName){
  const targetingCards = ['+1 Skip','+2 Skip','Storm','Storm+','Specter','Mafia Night','UNO Reverse','Swap Hands','Swap Identity'];
  if (!targetingCards.includes(cardName)) return null;
  const others = state.order.filter(x=>x!==currentPlayer() && !eliminated.has(x));
  if (!others.length) return null;
  return others[0];
}

function playCardEffect(card, target){
  showEffect(card.name, `Rarity: ${card.rarity.toUpperCase()} — ${card.desc}`);
  try{
    card.apply({ target });
    animateBoardPulse();
  }catch(e){}
}

function showEffect(title, desc){
  effectTitle.textContent = title;
  effectDesc.textContent = desc;
  effectOverlay.style.display='grid';
  setTimeout(()=>effectOverlay.style.display='none', 1300);
}
function animateBoardPulse(){
  boardEl.classList.add('win');
  setTimeout(()=>boardEl.classList.remove('win'), 700);
}

// Cell interactions
async function onCellClick(e){
  if (state.winner) return;

  if (pieceControl.active && currentPlayer()===pieceControl.owner){
    const idx = Number(e.currentTarget.dataset.idx);
    attemptPieceMove(idx);
    return;
  }

  const i = Number(e.currentTarget.dataset.idx);
  const player = currentPlayer();

  if (eliminated.has(player)) return;
  if (isBotTurn()) return;

  if (state.blocked.has(i) && !state.playBlockedOnce) return;
  if (state.grid[i]) return;

  if (state.mines.has(i) && config.mode==='mine'){
    placeGlyph(e.currentTarget, player);
    eliminatePlayer(player, `Mine triggered by ${player}`);
    return;
  }

  applyMove(i, player, e.currentTarget);

  if (config.mode==='randomizer') processChaosEvent(true);

  drawFor(player, 1);

  await maybeBotReply();
}

function eliminatePlayer(player, reason){
  eliminated.add(player);
  for(let idx=0; idx<state.grid.length; idx++){
    if (state.grid[idx]===player){
      state.grid[idx]=null;
      const el = boardEl.children[2 + idx];
      el.innerHTML='';
      el.classList.remove('played','win');
    }
  }
  showEffect('Eliminated', `${player} removed: ${reason}`);
  const activeLeft = state.order.filter(p=>!eliminated.has(p));
  if (activeLeft.length===1){
    endGame(activeLeft[0], [], false);
    return;
  }
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

  if (state.moves + state.blocked.size === state.grid.length){
    statusEl.textContent = 'Draw';
    scores.D++; renderScores(); stopTurnTimer(); return;
  }

  state.playBlockedOnce = false;
  advanceTurn();
}

function placeGlyph(el, player){
  el.innerHTML = `<span class="glyph ${symbolClass(player)}">${player}</span>`;
  el.classList.add('played');
}

function advanceTurn(){
  do {
    state.turnIndex = (state.turnIndex + 1) % state.order.length;
  } while (eliminated.has(currentPlayer()));

  const p = currentPlayer();
  if (state.skip[p] && state.skip[p]>0){
    state.skip[p]--;
    return advanceTurn();
  }

  tickDurations();

  updateStatus();
  updateCardsTray();
  showTurnPrompt(p);

  if (config.mode==='blitz'){
    let t = perPlayerTimer.get(p) ?? baseTimer;
    t = Math.min(60, Math.max(1, Math.floor(t)+1));
    perPlayerTimer.set(p, t);
    startTurnTimer(t);
  }

  maybeBotReply();
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
    const cell = boardEl.children[2 + idx];
    cell && cell.classList.add('win');
  }
  drawWinBeam(state.line);
  statusEl.textContent = byMine ? `Mine! ${winner} wins` : `${winner} wins`;
  boardEl.classList.add('win');
  stopTurnTimer();
  if (winner==='X') scores.X++;
  else if (winner==='O') scores.O++;
  else if (winner==='■') scores.P3++;
  else if (winner==='▲') scores.P4++;
  renderScores();
}

function drawWinBeam(line){
  if (!line || line.length===0) return;
  const first = line[0];
  const last = line[line.length-1];
  const a = boardEl.children[2 + first].getBoundingClientRect();
  const b = boardEl.children[2 + last].getBoundingClientRect();
  const br = boardEl.getBoundingClientRect();
  const x1 = (a.left+a.right)/2 - br.left;
  const y1 = (a.top+a.bottom)/2 - br.top;
  const x2 = (b.left+b.right)/2 - br.left;
  const y2 = (b.top+b.bottom)/2 - br.top;
  const dx = x2-x1, dy = y2-y1;
  const len = Math.sqrt(dx*dx+dy*dy);
  const angle = Math.atan2(dy,dx)*180/Math.PI;

  const beam = document.createElement('div');
  beam.className='beam';
  beam.style.left = x1+'px';
  beam.style.top = y1+'px';
  beam.style.width = len+'px';
  beam.style.transform = `translateY(-2px) rotate(${angle}deg)`;
  boardEl.appendChild(beam);
  setTimeout(()=>beam.remove(), 3000);
}

function renderScores(){
  document.getElementById('scoreX').textContent = String(scores.X);
  document.getElementById('scoreO').textContent = String(scores.O);
  document.getElementById('scoreP3').textContent = String(scores.P3);
  document.getElementById('scoreP4').textContent = String(scores.P4);
  document.getElementById('scoreD').textContent = String(scores.D);
}

function currentPlayer(){ return state.order[state.turnIndex]; }

// Win detection NxN
function checkWin(g, size, winLen){
  for(let r=0;r<size;r++){
    for(let c=0;c<=size-winLen;c++){
      const start=r*size+c; const first=g[start]; if(!first) continue;
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

/* Bot logic */
function isBot(p){ return config.opponent==='bot' && !eliminated.has(p) && p!==state.order[0]; }
function isBotTurn(){ return isBot(currentPlayer()); }
async function maybeBotFirstMove(){ if (isBotTurn()) await botTurn(); }
async function maybeBotReply(){
  if (isBotTurn()){
    await sleep(380);
    if (state.winner) return;
    if (!isBotTurn()) return;
    await botTurn();
  }
}

async function botTurn(){
  try{
    const d = config.difficulty;
    const highLevel = d >= 4.0;
    const baseThink = config.mode==='blitz' ? 120 : 220;
    await sleep(baseThink + Math.random()*160 + d*60);

    if (state.winner) return;
    if (!isBotTurn()) return;

    // High-level bots evaluate cards and may hold powerful ones
    if (config.mode==='randomizer'){
      const hand = (state.hands.get(currentPlayer()) || []);
      if (hand.length>0){
        const useChance = highLevel ? 0.65 : Math.min(0.2 + d*0.12, 0.45);
        let playIdx = -1;
        if (highLevel){
          // Prefer impactful cards unless M87 is reserved near win
          const nearWin = isAnyPlayerNearWin();
          playIdx = hand.findIndex(c=>{
            if (c.name==='M87') return nearWin; // hold until someone near win
            return ['Storm+','Quake+','Mass Shield','Purge','Teleport+','UNO Reverse','Swap Identity','Swap Hands','Blitz Surge'].includes(c.name);
          });
          if (playIdx<0 && Math.random()<useChance) playIdx = 0;
        } else if (Math.random()<useChance){
          playIdx = 0;
        }
        if (playIdx>=0){
          const card = hand[playIdx];
          playCardEffect(card, null);
          hand.splice(playIdx,1);
          state.hands.set(currentPlayer(), hand);
          updateCardsTray();
          if (state.extraTurns && state.extraTurns>0) state.extraTurns--;
          else { advanceTurn(); return; }
        }
      }
    }

    const i = chooseBotMove(highLevel);
    if (i==null){
      const empties = emptyIndices();
      if (!empties.length) { advanceTurn(); return; }
      const pick = empties[Math.floor(Math.random()*empties.length)];
      const cellEl = boardEl.children[2 + pick];
      applyMove(pick, currentPlayer(), cellEl);
      return;
    }
    const cellEl = boardEl.children[2 + i];
    applyMove(i, currentPlayer(), cellEl);
  }catch(e){
    advanceTurn();
  }
}
function rarityWeight(r){ return r===Rarity.COMMON?1: r===Rarity.RARE?2: r===Rarity.EPIC?3: r===Rarity.LEGEND?4:5; }
function chooseBotMove(highLevel=false){
  const size = state.size, winLen = getWinLength();
  const grid = state.grid.slice();
  const me = currentPlayer();
  const opp = state.order.filter(p=>p!==me);

  const winMove = findLineCompletion(grid, size, winLen, me); if (winMove!=null) return winMove;
  for (const o of opp){
    const blockMove = findLineCompletion(grid, size, winLen, o); if (blockMove!=null) return blockMove;
  }

  const empties = grid.map((v,idx)=> (v||state.blocked.has(idx))?null:idx).filter(v=>v!=null);
  if (!empties.length) return null;

  // Mine awareness: low-level ignore mines; high-level avoid likely mine cells heuristically
  let candidates = empties;
  if (config.mode==='mine' && highLevel){
    const safe = empties.filter(i=> !likelyMine(i));
    if (safe.length) candidates = safe;
  }

  const centers = getCenterIndices(size);
  const centerPick = centers.find(i=>candidates.includes(i));
  if (centerPick!=null) return centerPick;

  // Prefer higher line potential
  const scored = candidates.map(i=>({i,score: linePotentialScore(i, grid, size, winLen, me)})).sort((a,b)=>b.score-a.score);
  return (scored[0] ? scored[0].i : candidates[Math.floor(Math.random()*candidates.length)]);
}
function likelyMine(i){
  // Heuristic: avoid cells adjacent to many unknowns when mines exist
  const s = state.size, r=Math.floor(i/s), c=i%s;
  let suspicion=0;
  const neighbors = [[1,0],[-1,0],[0,1],[0,-1],[1,1],[1,-1],[-1,1],[-1,-1]];
  for(const [dr,dc] of neighbors){
    const rr=r+dr, cc=c+dc; if(rr<0||cc<0||rr>=s||cc>=s) continue;
    const idx=rr*s+cc;
    if (!state.grid[idx] && !state.blocked.has(idx)) suspicion++;
  }
  return suspicion>=5; // rough guess
}
function isAnyPlayerNearWin(){
  const size=state.size, winLen=getWinLength();
  for(const p of state.order){
    if (eliminated.has(p)) continue;
    const move=findLineCompletion(state.grid.slice(), size, winLen, p);
    if (move!=null) return true;
  }
  return false;
}
function findLineCompletion(grid,size,winLen,player){
  for(let i=0;i<grid.length;i++){ if(grid[i] || state.blocked.has(i)) continue; grid[i]=player; const w=checkWin(grid,size,winLen); grid[i]=null; if(w&&w.player===player) return i; }
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
function linePotentialScore(i, grid, size, winLen, player){
  let score = 0;
  const dirs = [[1,0],[0,1],[1,1],[1,-1]];
  for(const [dr,dc] of dirs){
    let chain=1;
    for(let k=1;k<winLen;k++){
      const rr = Math.floor(i/size)+dr*k, cc=i%size+dc*k;
      const idx = rr*size+cc;
      if (rr<0||cc<0||rr>=size||cc>=size) break;
      if (!grid[idx] && !state.blocked.has(idx)) chain++;
    }
    score += chain;
  }
  const oPos = grid.map((v,ii)=>v===player?ii:null).filter(v=>v!=null);
  score += adjacencyScore(i, oPos, size);
  return score;
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
    perPlayerTimer.set(currentPlayer(), t);
    if (t<=0){
      const loser = currentPlayer();
      let winnerSym = nextActive(loser);
      endGame(winnerSym, [], false);
      stopTurnTimer();
    }
  }, 1000);
}
function stopTurnTimer(){ if (timer){ clearInterval(timer); timer=null; } }
function nextActive(from){
  let idx = state.order.indexOf(from);
  do { idx = (idx + 1) % state.order.length; } while (eliminated.has(state.order[idx]));
  return state.order[idx];
}

// Chaos events
function processChaosEvent(often=false){
  state.chaosTick++;
  const interval = often ? 2 : 3;
  if (state.chaosTick % interval !== 0) return;
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
    {name:'+Timer', desc:'+2s to current timer (Blitz)', apply:()=>boostTimerFor(currentPlayer(),+2)},
    {name:'-Timer', desc:'-2s to next timer (Blitz)', apply:()=>drainNextTimer(2)},
    {name:'Add mine', desc:'A mine appears randomly', apply:()=>addRandomMine()},
    {name:'Remove mine', desc:'A mine disappears', apply:()=>removeRandomMine()},
    {name:'Reveal mine hint', desc:'A mine pulses somewhere…', apply:()=>pulseMineTease()},
    {name:'Fog burst', desc:'Brief global fog', apply:()=>applyFog(currentPlayer(),1)},
    {name:'Zone chill', desc:'Block a 2x2 zone briefly', apply:()=>coverZone(2,1)},
    {name:'Meteor spark', desc:'Clear 2 random cells', apply:()=>meteorStrike(2)}
  ];
  return events[Math.floor(Math.random()*events.length)];
}

// Card effects helpers
function skipTarget(target, n){ state.skip = state.skip || {}; state.skip[target] = (state.skip[target] || 0) + n; }
function reverseTurnOrder(){ const cp = currentPlayer(); state.order.reverse(); state.turnIndex = state.order.indexOf(cp); updateStatus(); }
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
  const el = boardEl.children[2 + i];
  el.setAttribute('disabled','true');
  el.style.opacity = .45; el.style.filter = 'grayscale(0.3) brightness(0.8)';
  el.title = 'Blocked';
  el.animate([{transform:'scale(1)'},{transform:'scale(.95)'},{transform:'scale(1)'}], {duration:180, easing:'ease-out'});
  if (state.moves + state.blocked.size === state.grid.length && !state.winner){
    statusEl.textContent = 'Draw'; scores.D++; renderScores(); stopTurnTimer();
  }
}
function clearChosenMark(ctx){ pickCells(1, (idxs)=> idxs.forEach(i=>clearMark(i)), true); }
function clearChosenMarks(ctx, n){ pickCells(n, (idxs)=> idxs.forEach(i=>clearMark(i)), true); }
function clearMark(i){
  if (!state.grid[i]) return;
  if (state.shields && state.shields.has(i)) return;
  state.grid[i] = null;
  const el = boardEl.children[2 + i];
  el.innerHTML=''; el.classList.remove('played','win');
  el.animate([{filter:'brightness(1)'},{filter:'brightness(.7)'},{filter:'brightness(1)'}], {duration:200, easing:'ease-out'});
}
function undoLastMove(){
  for(let i=state.grid.length-1; i>=0; i--){
    if (boardEl.children[2 + i].classList.contains('played')){
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
  const empties = emptyIndices();
  if (!empties.length) return;
  const i = empties[Math.floor(Math.random()*empties.length)];
  state.mines.add(i);
}
function swapMineToEmpty(){
  const mines = Array.from(state.mines);
  const empties = emptyIndices();
  if (!mines.length || !empties.length) return;
  const m = mines[Math.floor(Math.random()*mines.length)];
  const dest = empties[Math.floor(Math.random()*empties.length)];
  state.mines.delete(m);
  state.mines.add(dest);
  pulseCell(dest, 'var(--danger)');
}
function swapTwoMines(){ swapMineToEmpty(); swapMineToEmpty(); }
function clonePlay(free=false){ state.extraTurns = (state.extraTurns||0) + (free?2:1); }
function teleportMark(ctx, two=false){
  pickCells(two?2:1, (idxs)=>{
    idxs.forEach(i=>{
      if (!state.grid[i]) return;
      const p = state.grid[i];
      const empties = emptyIndices();
      if (!empties.length) return;
      const dest = empties[Math.floor(Math.random()*empties.length)];
      state.grid[i]=null;
      const srcEl = boardEl.children[2 + i];
      srcEl.innerHTML=''; srcEl.classList.remove('played');
      state.grid[dest]=p;
      const dstEl = boardEl.children[2 + dest];
      placeGlyph(dstEl, p);
      pulseCell(dest, 'var(--accent)');
    });
  }, true);
}
function shieldMark(ctx, two=false){
  state.shields = state.shields || new Set();
  pickCells(two?2:1, (idxs)=> idxs.forEach(i=> state.shields.add(i)), true);
}
function shieldMultiple(n){ pickCells(n, (idxs)=> idxs.forEach(i=> state.shields.add(i)), true); }
function extraTurn(times=1){ state.extraTurns = (state.extraTurns||0) + times; }
function bridgeMarks(ctx, loose=false){ /* visual only */ }
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
      const el = boardEl.children[2 + i];
      el.style.outline = mine ? '2px solid var(--danger)' : '2px solid var(--win)';
      setTimeout(()=> el.style.outline='', 1200);
    });
  });
}
function peekMultiple(ctx, n){ pickCells(n, (idxs)=> idxs.forEach(i=>peekMine({target:null, i})) ); }
function applyFog(owner, rounds){
  fogOwner = owner; fogRoundsLeft = rounds;
  fogMask.classList.add('active');
  showEffect('Fog', `Only ${owner} sees clearly for ${rounds} rounds`);
}
function unfogBoard(force=false){
  fogRoundsLeft = 0; fogOwner = null;
  fogMask.classList.remove('active');
  if (force) showEffect('Fog cleared', 'Visibility restored');
}
function revealMines(owner, rounds){
  revealedMinesOwner = owner;
  revealedMinesRounds = rounds;
  pulseAllMines();
}
function moveMineTo(ctx){ swapMineToEmpty(); }
function playOnBlocked(ctx){ state.playBlockedOnce = true; }
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
  const powers = [m87Reset, novaGlow, ()=>blockRandomCells(4), ()=>clearRandomOpponentMarks(3), ()=>extraTurn(2), ()=>expandBoard()];
  powers[Math.floor(Math.random()*powers.length)]();
}
function boostTimerFor(player, delta){
  if (config.mode!=='blitz') return;
  const t = Math.max(1, (perPlayerTimer.get(player) ?? baseTimer) + delta);
  perPlayerTimer.set(player, t);
  if (currentPlayer()===player) startTurnTimer(t);
}
function drainNextTimer(delta){
  if (config.mode!=='blitz') return;
  const next = nextActive(currentPlayer());
  const t = Math.max(1, (perPlayerTimer.get(next) ?? baseTimer) - delta);
  perPlayerTimer.set(next, t);
}
function resetTimers(){
  if (config.mode!=='blitz') return;
  for(const p of state.order){ perPlayerTimer.set(p, baseTimer); }
  startTurnTimer(perPlayerTimer.get(currentPlayer()));
}
function ceilYourTimer(){
  if (config.mode!=='blitz') return;
  const p = currentPlayer();
  const t = Math.ceil(perPlayerTimer.get(p) ?? baseTimer);
  perPlayerTimer.set(p, t);
  startTurnTimer(t);
}
function floorNextTimer(){
  if (config.mode!=='blitz') return;
  const next = nextActive(currentPlayer());
  const t = Math.max(1, Math.floor(perPlayerTimer.get(next) ?? baseTimer));
  perPlayerTimer.set(next, t);
}
function pulseMineTease(){
  if (!state.mines.size) return;
  const i = Array.from(state.mines)[Math.floor(Math.random()*state.mines.size)];
  pulseCell(i, 'var(--danger)');
}
function pulseAllMines(){ Array.from(state.mines).forEach(i=>pulseCell(i, 'var(--danger)')); }
function purgeArea(size){
  pickCells(1, (idxs)=>{
    const center = idxs[0];
    const s = state.size;
    const cr = Math.floor(center/s), cc=center%s;
    const half = Math.floor(size/2);
    for(let r=cr-half;r<=cr+half;r++){
      for(let c=cc-half;c<=cc+half;c++){
        if (r<0||c<0||r>=s||c>=s) continue;
        const i=r*s+c; clearMark(i);
      }
    }
  });
}

// Bonus effects
function meteorStrike(n){
  const filled = state.grid.map((v,i)=>v?i:null).filter(v=>v!=null);
  shuffle(filled);
  for(let k=0;k<n && filled.length;k++){ clearMark(filled.pop()); }
}
function timeFreeze(seconds){
  if (config.mode!=='blitz') return;
  stopTurnTimer();
  setTimeout(()=>{ startTurnTimer(perPlayerTimer.get(currentPlayer()) ?? baseTimer); }, Math.max(0, seconds*1000));
}
function shuffleMines(){
  const count = state.mines.size;
  state.mines.clear();
  const empties = emptyIndices();
  shuffle(empties);
  for(let k=0;k<count && empties.length;k++){ state.mines.add(empties.pop()); }
  pulseAllMines();
}

// Mafia Night (eliminate one player if >2 remain; owner-only selection visuals)
function startMafiaNight(){
  const activeCount = state.order.filter(p=>!eliminated.has(p)).length;
  if (activeCount<=2){ showEffect('Mafia Night', 'Not enough players to eliminate'); return; }
  mafiaOwner = currentPlayer();
  mafiaActive = true;
  showEffect('Mafia Night', `Secret choice under way…`);
  if (!isBot(mafiaOwner)){
    pickOpponentMarks(1, (idxs)=>{
      if (idxs.length){
        const victimMark = state.grid[idxs[0]];
        if (victimMark) eliminatePlayer(victimMark, 'Mafia decision');
      }
      mafiaActive=false; mafiaOwner=null;
    }, 'limegreen');
  } else {
    // Bot mafia chooses victim by majority marks
    const opp = state.order.filter(p=>p!==mafiaOwner && !eliminated.has(p));
    const counts = new Map(opp.map(o=>[o,0]));
    for(let i=0;i<state.grid.length;i++){ const m=state.grid[i]; if(counts.has(m)) counts.set(m, counts.get(m)+1); }
    const sorted = Array.from(counts.entries()).sort((a,b)=>b[1]-a[1]);
    const victim = sorted[0]?.[0];
    if (victim) eliminatePlayer(victim, 'Mafia decision');
    mafiaActive=false; mafiaOwner=null;
  }
}

// Cover & expand, pieces
function coverZone(size, rounds){
  pickCells(1, (idxs)=>{
    const center = idxs[0];
    const s = state.size;
    const cr = Math.floor(center/s), cc=center%s;
    const half = Math.floor(size/2);
    for(let r=cr-half;r<=cr+half;r++){
      for(let c=cc-half;c<=cc+half;c++){
        if (r<0||c<0||r>=s||c>=s) continue;
        const i=r*s+c; blockIndex(i);
      }
    }
    setTimeout(()=>unblockAll(), rounds*900);
  });
}
function coverLine(rounds){
  pickCells(1, (idxs)=>{
    const i = idxs[0], s=state.size, r=Math.floor(i/s);
    for(let c=0;c<s;c++){ blockIndex(r*s+c); }
    setTimeout(()=>unblockAll(), rounds*900);
  });
}
function unblockAll(){
  state.blocked = new Set();
  Array.from(boardEl.children).slice(2).forEach(el=>{
    el.removeAttribute('disabled');
    el.style.opacity = '';
    el.style.filter = '';
    el.title = '';
  });
}
function expandBoard(layers=1){
  const targetSize = state.size + layers;
  const oldGrid = state.grid.slice();
  const oldSize = state.size;
  state.size = targetSize;
  state.grid = Array(targetSize*targetSize).fill(null);
  const startOffset = Math.floor((targetSize-oldSize)/2);
  for(let r=0;r<oldSize;r++){
    for(let c=0;c<oldSize;c++){
      const oldIdx=r*oldSize+c;
      const newIdx=(r+startOffset)*targetSize+(c+startOffset);
      state.grid[newIdx]=oldGrid[oldIdx];
    }
  }
  boardEl.innerHTML = '<div class="ribbon" aria-hidden="true"></div><div class="fogMask" id="fogMask"></div>';
  boardEl.style.gridTemplateColumns = `repeat(${targetSize}, min(16vw, ${Math.max(84, Math.floor(540/targetSize))}px))`;
  boardEl.style.gridTemplateRows = `repeat(${targetSize}, min(16vw, ${Math.max(84, Math.floor(540/targetSize))}px))`;
  for(let i=0;i<state.grid.length;i++){
    const cell=document.createElement('button');
    cell.className='cell';
    cell.dataset.idx=i;
    cell.addEventListener('click', onCellClick);
    if (state.grid[i]) placeGlyph(cell, state.grid[i]);
    boardEl.appendChild(cell);
  }
  while(state.mines.size<mineCount()){
    const i = randomInt(0, state.grid.length-1);
    state.mines.add(i);
  }
  showEffect('Expand', `Board size is now ${state.size}x${state.size}`);
}

// Piece spawn and immediate interactive move
function spawnPiece(type, rounds){
  const empties = emptyIndices();
  if (!empties.length) return;
  const i = empties[Math.floor(Math.random()*empties.length)];
  const owner = currentPlayer();
  const piece = {type, idx:i, rounds, owner};
  state.pieces.push(piece);

  const el = boardEl.children[2 + i];
  const badge = document.createElement('div');
  badge.className='piece';
  badge.textContent = type;
  el.appendChild(badge);

  pieceControl = { active:true, owner, piece, moves:1 };
  showEffect(`${type} placed`, `Move it once now`);
  highlightPieceMoves(piece);
}

function chessRain(){
  const count = randomInt(2,4);
  const types = ['Knight','Bishop','Rook','Checker'];
  for(let k=0;k<count;k++){
    spawnPiece(types[Math.floor(Math.random()*types.length)], randomInt(1,4));
  }
}

function mafiaMini(){
  const opp = state.order.filter(p=>p!==currentPlayer());
  const marks = [];
  for(let i=0;i<state.grid.length;i++){
    if (state.grid[i] && opp.includes(state.grid[i])) marks.push(i);
  }
  if (!marks.length) return;
  clearMark(marks[Math.floor(Math.random()*marks.length)]);
}
function unoReverse(){
  reverseTurnOrder();
  const next = nextActive(currentPlayer());
  drawFor(next,2);
}

function tickDurations(){
  if (fogRoundsLeft>0){ fogRoundsLeft--; if (fogRoundsLeft<=0) unfogBoard(); }
  if (revealedMinesRounds>0){ revealedMinesRounds--; if (revealedMinesRounds<=0) { revealedMinesOwner=null; } }

  for (const piece of state.pieces){ actPiece(piece); piece.rounds--; }
  state.pieces = state.pieces.filter(p=>p.rounds>0);
  Array.from(boardEl.children).slice(2).forEach(el=>{
    const badge = el.querySelector('.piece');
    if (badge){
      const idx = Number(el.dataset.idx);
      const alive = state.pieces.find(p=>p.idx===idx);
      if (!alive) badge.remove();
    }
  });
}

function actPiece(piece){
  const s = state.size;
  const origin = piece.idx;
  const r=Math.floor(origin/s), c=origin%s;
  const hit = (ri,ci)=>{
    if (ri<0||ci<0||ri>=s||ci>=s) return;
    const i=ri*s+ci;
    if (state.blocked.has(i)) return;
    if (state.grid[i] && state.grid[i]===piece.owner) return;
    clearMark(i);
  };
  if (piece.type==='Knight'){
    const deltas=[[2,1],[2,-1],[-2,1],[-2,-1],[1,2],[1,-2],[-1,2],[-1,-2]];
    deltas.forEach(([dr,dc])=>hit(r+dr,c+dc));
  } else if (piece.type==='Bishop'){
    for (let k=1;k<s;k++){ hit(r+k,c+k); hit(r-k,c-k); hit(r+k,c-k); hit(r-k,c+k); }
  } else if (piece.type==='Rook'){
    for (let k=1;k<s;k++){ hit(r+k,c); hit(r-k,c); hit(r,c+k); hit(r,c-k); }
  } else if (piece.type==='Checker'){
    [[1,1],[1,-1],[-1,1],[-1,-1]].forEach(([dr,dc])=>hit(r+dr,c+dc));
  }
}

// Interactive piece move helpers (green outlines owner-only)
function pieceMoves(piece){
  const s = state.size;
  const origin = piece.idx;
  const r=Math.floor(origin/s), c=origin%s;
  const moves = [];
  const add = (ri,ci)=>{
    if (ri<0||ci<0||ri>=s||ci>=s) return;
    const i=ri*s+ci;
    if (state.blocked.has(i)) return;
    moves.push(i);
  };
  if (piece.type==='Knight'){
    [[2,1],[2,-1],[-2,1],[-2,-1],[1,2],[1,-2],[-1,2],[-1,-2]].forEach(([dr,dc])=>add(r+dr,c+dc));
  } else if (piece.type==='Bishop'){
    for (let k=1;k<s;k++){ add(r+k,c+k); add(r-k,c-k); add(r+k,c-k); add(r-k,c+k); }
  } else if (piece.type==='Rook'){
    for (let k=1;k<s;k++){ add(r+k,c); add(r-k,c); add(r,c+k); add(r,c-k); }
  } else if (piece.type==='Checker'){
    [[1,1],[1,-1],[-1,1],[-1,-1]].forEach(([dr,dc])=>add(r+dr,c+dc));
  }
  return Array.from(new Set(moves));
}
function highlightPieceMoves(piece){
  const allowed = new Set(pieceMoves(piece));
  const cells = Array.from(boardEl.children).slice(2);
  cells.forEach((el, idx)=>{
    if (allowed.has(idx) && currentPlayer()===piece.owner){
      el.style.outline='2px solid var(--lime)';
      el.style.boxShadow='0 0 14px rgba(50,205,50,.45) inset';
    }
  });
}
function clearPieceHighlights(){
  Array.from(boardEl.children).slice(2).forEach(el=>{
    el.style.outline='';
    el.style.boxShadow='';
  });
}
function attemptPieceMove(destIdx){
  const piece = pieceControl.piece;
  if (!piece) return;
  const allowed = new Set(pieceMoves(piece));
  if (!allowed.has(destIdx)) return;
  if (state.grid[destIdx] && state.grid[destIdx]===piece.owner) return;

  const fromEl = boardEl.children[2 + piece.idx];
  const toEl = boardEl.children[2 + destIdx];
  const badge = fromEl.querySelector('.piece');
  if (!badge) return;

  if (state.grid[destIdx] && (!state.shields || !state.shields.has(destIdx))) clearMark(destIdx);

  piece.idx = destIdx;
  toEl.appendChild(badge);
  clearPieceHighlights();

  pieceControl.moves--;
  if (pieceControl.moves<=0) pieceControl = { active:false, owner:null, piece:null, moves:0 };
  else highlightPieceMoves(piece);
}

// Target/cell picker
function pickCells(n, done, requireHasMark=false){
  const picked = [];
  const cells = Array.from(boardEl.children).slice(2);
  const handlers = new Map();

  cells.forEach((el, idx)=>{
    const hasMark = !!state.grid[idx];
    const ok = requireHasMark ? hasMark : !state.blocked.has(idx);
    if (!ok) return;

    el.style.outline='2px solid var(--gold)';
    const h = ()=>{
      picked.push(idx);
      el.style.outline='';
      el.removeEventListener('click', h);
      handlers.delete(el);
      if (picked.length===n){
        for (const [cell, handler] of handlers){
          cell.style.outline='';
          cell.removeEventListener('click', handler);
        }
        done(picked);
      }
    };
    handlers.set(el, h);
    el.addEventListener('click', h, {once:true});
  });
}
function pickOpponentMarks(n, done, color='var(--danger)'){
  const picked = [];
  const cells = Array.from(boardEl.children).slice(2);
  const handlers = new Map();
  const me = currentPlayer();
  const opponents = state.order.filter(p=>p!==me);

  cells.forEach((el, idx)=>{
    const mark = state.grid[idx];
    const ok = !!mark && opponents.includes(mark);
    if (!ok) return;
    el.style.outline=`2px solid ${color}`;
    const h = ()=>{
      picked.push(idx);
      el.style.outline='';
      el.removeEventListener('click', h);
      handlers.delete(el);
      if (picked.length===n){
        for (const [cell, handler] of handlers){
          cell.style.outline='';
          cell.removeEventListener('click', handler);
        }
        done(picked);
      }
    };
    handlers.set(el, h);
    el.addEventListener('click', h, {once:true});
  });
}

// Identity/hand/morph/blitz cards
function swapIdentity(ctx){
  const others = state.order.filter(p=>p!==currentPlayer() && !eliminated.has(p));
  if(!others.length) return;
  const target=ctx.target || others[0];
  const me=currentPlayer();
  for(let i=0;i<state.grid.length;i++){
    if(state.grid[i]===me) state.grid[i]=target;
    else if(state.grid[i]===target) state.grid[i]=me;
    const cell = boardEl.children[2 + i];
    if (state.grid[i]) placeGlyph(cell, state.grid[i]);
  }
  showEffect('Swap Identity', `${me} <-> ${target}`);
}
function swapHands(ctx){
  const others = state.order.filter(p=>p!==currentPlayer() && !eliminated.has(p));
  if(!others.length) return;
  const target=ctx.target || others[0];
  const myHand=state.hands.get(currentPlayer())||[];
  const theirHand=state.hands.get(target)||[];
  state.hands.set(currentPlayer(),theirHand);
  state.hands.set(target,myHand);
  updateCardsTray();
  showEffect('Swap Hands', `Hands swapped with ${target}`);
}
function morphMark(ctx){
  pickCells(1,(idxs)=>{
    const i=idxs[0]; if(!state.grid[i])return;
    const me = state.grid[i];
    const others=state.order.filter(p=>p!==me && !eliminated.has(p));
    if (!others.length) return;
    state.grid[i]=others[0];
    const cell = boardEl.children[2 + i];
    placeGlyph(cell, state.grid[i]);
  },true);
}
function blitzSurge(){
  config.mode='blitz';
  baseTimer=randomInt(12,20);
  for(const p of state.order) perPlayerTimer.set(p,baseTimer);
  startTurnTimer(baseTimer);
}

// Utils
function emptyIndices(){ return state.grid.map((v,i)=> v||state.blocked.has(i)?null:i).filter(v=>v!=null); }
function pulseCell(i, color){
  const el = boardEl.children[2 + i];
  el.style.outline=`2px solid ${color}`;
  setTimeout(()=> el.style.outline='', 800);
}
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
