<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>X and O — Chaos Deck Mafia</title>
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
    transition: transform .1s ease, box-shadow .15s ease, border-color .15s ease, background .2s ease;
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
    transition: transform .14s ease, border-color .15s ease, box-shadow .18s ease, background .22s ease, color .2s ease, filter .2s ease;
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

  /* Win-line beam */
  .beam{
    position:absolute; height:4px; border-radius:999px;
    background: linear-gradient(90deg, rgba(115,209,61,0), var(--win), rgba(115,209,61,0));
    box-shadow: 0 0 16px rgba(115,209,61,.9);
    transform-origin:left center;
    animation: beamDraw 1s ease-out forwards, beamHold .6s linear 1s forwards, beamFade .8s ease-in 1.6s forwards;
    pointer-events:none; z-index:3;
  }
  @keyframes beamDraw{ from{ width:0; opacity:.0 } to{ width:100%; opacity:1 } }
  @keyframes beamHold{ from{ opacity:1 } to{ opacity:1 } }
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

  /* Overlays */
  .effectOverlay{
    position:fixed; inset:0; display:grid; place-items:center; z-index:1002; pointer-events:none;
  }
  .effectBubble{
    display:grid; gap:8px; justify-items:center;
    padding:16px 20px; border-radius:12px; color:#fff;
    border:1px solid rgba(255,255,255,.2);
    background: radial-gradient(280px 140px at 50% 40%, rgba(20,24,34,.96), rgba(16,20,32,.92));
    box-shadow: 0 14px 40px rgba(0,0,0,.6);
    animation: effectPop .22s ease-out;
  }
  @keyframes effectPop{ 0%{ transform:scale(.92); opacity:.0 } 100%{ transform:scale(1); opacity:1 } }

  .turnOverlay{
    position:fixed; inset:0; display:grid; place-items:center; z-index:1001; pointer-events:none;
    opacity:0; transition: opacity .22s ease;
  }
  .turnOverlay.active{ opacity:1 }
  .turnBubble{
    padding:14px 18px; border-radius:12px; color:#fff;
    border:1px solid rgba(255,255,255,.2);
    background: radial-gradient(280px 140px at 50% 40%, rgba(20,24,34,.92), rgba(16,20,32,.88));
    box-shadow: 0 10px 28px rgba(0,0,0,.5);
    animation: effectPop .2s ease-out;
    font-weight:700;
  }

  /* Fog mask */
  .fogMask{
    position:absolute; inset:0; backdrop-filter: blur(10px) brightness(0.6);
    background: radial-gradient(800px 400px at 50% 30%, rgba(10,12,18,.65), rgba(6,8,12,.85));
    pointer-events:none; z-index:2; opacity:0; transition: opacity .25s ease;
  }
  .fogMask.active{ opacity:1 }

  /* Chess/checker piece marker + move hint */
  .piece{
    position:absolute; inset:auto auto 6px 6px; z-index:3;
    font-size:12px; padding:2px 6px; border-radius:8px;
    border:1px solid rgba(255,255,255,.18);
    background: rgba(16,20,32,.88); color:#fff;
    box-shadow: 0 6px 18px rgba(0,0,0,.4);
    animation: pieceIn .2s ease-out;
  }
  @keyframes pieceIn{ from{ transform:translateY(6px); opacity:.0 } to{ transform:translateY(0); opacity:1 } }
  .moveHint{
    position:absolute; inset:0; outline:2px solid rgba(115,209,61,.85); border-radius:14px; pointer-events:none; opacity:.9;
    animation: hintPulse 1s ease-in-out infinite;
  }
  @keyframes hintPulse{ 0%{opacity:.35} 50%{opacity:1} 100%{opacity:.35} }
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

  <!-- Private cards tray (only current player's cards are shown) -->
  <div class="cardsTray" id="cardsTray" style="display:none"></div>

  <!-- Effect overlay -->
  <div class="effectOverlay" id="effectOverlay" style="display:none">
    <div class="effectBubble" id="effectBubble">
      <div class="effectTitle" id="effectTitle"></div>
      <div class="effectDesc" id="effectDesc"></div>
    </div>
  </div>
  <!-- Turn overlay -->
  <div class="turnOverlay" id="turnOverlay"><div class="turnBubble" id="turnBubble">Your Turn</div></div>

  <!-- Loading overlay -->
  <div id="loading" class="loading" aria-live="polite" aria-busy="true" style="display:none"></div>

  <!-- Setup modal -->
  <div id="modal" class="modal" aria-modal="true" role="dialog" style="display:none">
    <div class="sheet" style="width:min(580px,92vw);display:grid;gap:14px;padding:18px;border:1px solid var(--border);border-radius:14px;background:linear-gradient(180deg, rgba(16,20,32,.78), rgba(14,18,30,.88));box-shadow:var(--shadow)">
      <h3 style="margin:0;font-size:18px">Game setup</h3>

      <div class="group">
        <div class="pill">Opponent</div>
        <div class="row-flex" style="display:flex;gap:12px;align-items:center">
          <select class="select" id="opponent" style="flex:1;background:#101423;border:1px solid rgba(255,255,255,.12);color:var(--text);border-radius:10px;padding:8px 10px">
            <option value="player">Local players</option>
            <option value="bot">Bot</option>
          </select>
        </div>
      </div>

      <div class="group">
        <div class="pill">Gamemode</div>
        <select class="select" id="mode" style="background:#101423;border:1px solid rgba(255,255,255,.12);color:var(--text);border-radius:10px;padding:8px 10px">
          <option value="casual">Casual (normal tic‑tac‑toe)</option>
          <option value="randomizer">Chaos Deck (cards & random events)</option>
          <option value="mine">Minefield (hidden mines + eliminations)</option>
          <option value="blitz">Blitz (turn timer with carryover +1s)</option>
        </select>
      </div>

      <div class="group">
        <div class="pill">Players</div>
        <select class="select" id="playerCount" style="background:#101423;border:1px solid rgba(255,255,255,.12);color:var(--text);border-radius:10px;padding:8px 10px">
          <option value="2">2 players</option>
          <option value="3">3 players</option>
          <option value="4">4 players</option>
        </select>
      </div>

      <div class="row-flex" style="justify-content:flex-end;display:flex;gap:12px">
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
const modal = document.getElementById('modal');
const opponentSel = document.getElementById('opponent');
const modeSel = document.getElementById('mode');
const playerCountSel = document.getElementById('playerCount');

const timerBox = document.getElementById('timerBox');
const timeLeftEl = document.getElementById('timeLeft');

const cardsTray = document.getElementById('cardsTray');

const effectOverlay = document.getElementById('effectOverlay');
const effectTitle = document.getElementById('effectTitle');
const effectDesc = document.getElementById('effectDesc');

const fogMask = document.getElementById('fogMask');

const turnOverlay = document.getElementById('turnOverlay');
const turnBubble = document.getElementById('turnBubble');

const cancelSetup = document.getElementById('cancelSetup');
const applySetup = document.getElementById('applySetup');

// State
let config = {
  opponent: 'player', // 'player' or 'bot'
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

// Round tracking (for Mafia cadence)
let roundCount = 0; // increments when turn cycles back to first player

// Mafia
let mafiaCardUsed = false; // one-time obtain/use rule
let mafiaRole = null;      // symbol of mafia (hidden)
let mafiaPhase = false;    // dark phase active
let sheriffRevealed = null; // last sheriff check target
let votes = new Map();     // player -> votes count

// Controllable chess/checker piece
let activePieceControl = null; // { owner, type, idx, roundsLeft, capturedThisTurn }

// Loader (minimal)
function showModal(flag){ modal.style.display = flag ? 'grid' : 'none'; }

setupBtn.addEventListener('click', ()=> showModal(true));
cancelSetup.addEventListener('click', ()=> showModal(false));
applySetup.addEventListener('click', ()=>{
  config.opponent = opponentSel.value;
  config.mode = modeSel.value;
  config.players = parseInt(playerCountSel.value, 10);
  showModal(false);
  initGame(true);
});

// Boot
initGame(false);

// Helpers
function getTurnOrder(players){
  if (players===4) return ['X','O','■','▲'];
  if (players===3) return ['X','O','■'];
  return ['X','O'];
}
function getBoardSize(){
  if (config.mode==='randomizer') return 5;
  if (config.players===3) return 4;
  if (config.players===4) return 5;
  return 3;
}
function getWinLength(){ const s=getBoardSize(); return s>=4 ? 4 : 3; }
function symbolClass(sym){ return sym==='X'?'x' : sym==='O'?'o' : sym==='■'?'p3':'p4'; }
function currentPlayer(){ return state.order[state.turnIndex]; }
function isBot(p){ return config.opponent==='bot' && p!=='X'; } // only non-first players are bots
function isBotTurn(){ return isBot(currentPlayer()); }
function nextActive(from){
  let idx = state.order.indexOf(from);
  do { idx = (idx + 1) % state.order.length; } while (eliminated.has(state.order[idx]));
  return state.order[idx];
}

// Init
function initGame(fromSetup){
  eliminated.clear();
  fogOwner = null; fogRoundsLeft = 0;
  perPlayerTimer.clear();
  activePieceControl = null;
  mafiaRole = null; mafiaPhase = false; sheriffRevealed=null; votes.clear();

  const size = getBoardSize();
  state = {
    size,
    grid:Array(size*size).fill(null),
    blocked:new Set(),
    order:getTurnOrder(config.players),
    turnIndex:0,
    winner:null,
    line:[],
    moves:0,
    mines:new Set(),
    deck:[],
    hands:new Map(), // per player cards
    skip:{},
    shields:new Set(),
    extraTurns:0,
    playBlockedOnce:false,
    pieces:[], // passive piece badges
  };
  buildDeck();
  dealStartingHands();

  boardEl.classList.remove('win');
  boardEl.innerHTML = '<div class="ribbon" aria-hidden="true"></div><div class="fogMask" id="fogMask"></div>';
  boardEl.style.gridTemplateColumns = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(540/size))}px))`;
  boardEl.style.gridTemplateRows = `repeat(${size}, min(16vw, ${Math.max(84, Math.floor(540/size))}px))`;
  for(let i=0;i<size*size;i++){
    const cell=document.createElement('button');
    cell.className='cell';
    cell.dataset.idx=i;
    cell.addEventListener('click', onCellClick);
    boardEl.appendChild(cell);
  }

  updateStatus();
  renderScores();
  updateCardsTray();

  if (config.mode==='blitz'){
    baseTimer = config.blitzSec;
    for(const p of state.order){ perPlayerTimer.set(p, baseTimer); }
    startTurnTimer(baseTimer);
  } else {
    stopTurnTimer(); timerBox.style.display='none';
  }

  // bots only move if it is bot's turn
  if (isBotTurn()) botTurn();
}

// Turn overlays
function showTurnOverlay(text){
  turnBubble.textContent = text;
  turnOverlay.classList.add('active');
  setTimeout(()=>turnOverlay.classList.remove('active'), 900);
}

// Status
function updateStatus(){
  const player = currentPlayer();
  statusEl.textContent = `${player} to move`;
  const color = player==='X' ? 'rgba(122,162,247,.25)'
              : player==='O' ? 'rgba(201,210,126,.25)'
              : player==='■' ? 'rgba(255,159,111,.25)'
              : 'rgba(106,227,189,.25)';
  statusEl.style.borderColor = color;
  statusEl.style.boxShadow = `0 0 12px ${color} inset`;

  const isHumanTurn = !isBot(player);
  const label = isHumanTurn ? (player==='X' ? 'Your Turn' : 'First Player Turn') : `${player} (Bot)`;
  showTurnOverlay(label);
}

// Move
async function onCellClick(e){
  if (state.winner) return;
  const i = Number(e.currentTarget.dataset.idx);
  const player = currentPlayer();

  // Mafia: if phase active and player != mafia, they cannot act
  if (mafiaPhase && player !== mafiaRole){
    showEffect('Night', 'Darkness falls. Wait…');
    return;
  }

  // Controllable piece move first (may capture one piece; checker can chain captures by rules)
  if (activePieceControl && activePieceControl.owner === player){
    const can = pieceCanMove(activePieceControl, i);
    showMoveTargets(activePieceControl); // green highlight
    if (!can){ showEffect('Piece blocked', 'Invalid target'); return; }
    moveControlledPiece(activePieceControl, i);
    activePieceControl.roundsLeft--;
    if (activePieceControl.roundsLeft <= 0) activePieceControl = null;
    // Moving doesn't consume the mark; player may still place mark in same turn
    return;
  }

  if (isBotTurn()) return; // block only when it's actually bot’s turn
  if (eliminated.has(player)) return;
  if (state.blocked.has(i) && !state.playBlockedOnce) return;
  if (state.grid[i]) return;

  // Place mark
  state.grid[i] = player;
  state.moves++;
  placeGlyph(e.currentTarget, player);

  // Card overflow elimination rule
  const hand = state.hands.get(player) || [];
  if (hand.length > 10){
    eliminatePlayer(player, 'Too many cards (>10)');
  }

  const win = checkWin(state.grid, state.size, getWinLength());
  if (win){ endGame(player, win.line, false); return; }

  if (state.moves + state.blocked.size === state.grid.length){
    statusEl.textContent = 'Draw';
    scores.D++; renderScores(); stopTurnTimer(); return;
  }

  // Draw a card after placing mark (randomizer mode)
  if (config.mode==='randomizer') drawFor(player, 1);

  state.playBlockedOnce = false;
  advanceTurn();
}

// Advance turn (handles roundCount and mafia cadence)
function advanceTurn(){
  const prev = currentPlayer();
  // Standard advance
  do {
    state.turnIndex = (state.turnIndex + 1) % state.order.length;
  } while (eliminated.has(currentPlayer()));

  const now = currentPlayer();

  // If we wrapped around to first non-eliminated, count a round
  if (now === state.order[0]) {
    roundCount++;
    // Every 3–5 rounds: trigger Mafia night cadence
    if (roundCount >= 3 && roundCount <= 5 && !mafiaPhase){
      triggerMafiaCadence();
      // Reset window after cadence
      roundCount = 0;
    }
  }

  tickDurations();
  updateStatus();
  updateCardsTray();

  // Blitz
  if (config.mode==='blitz'){
    let t = perPlayerTimer.get(now) ?? baseTimer;
    t = Math.min(60, Math.max(1, Math.floor(t)+1)); // carryover +1s
    perPlayerTimer.set(now, t);
    startTurnTimer(t);
  }

  // If it's bot’s turn now, let bot act
  if (isBotTurn()) botTurn();
}

// Bot
async function botTurn(){
  await sleep(220 + Math.random()*160);

  // Bot piece control first
  if (activePieceControl && activePieceControl.owner === currentPlayer()){
    const moveTo = chooseControlledPieceMove(activePieceControl);
    if (moveTo!=null){
      moveControlledPiece(activePieceControl, moveTo);
      activePieceControl.roundsLeft--;
      if (activePieceControl.roundsLeft <= 0) activePieceControl = null;
    }
  }

  // Pick move
  const i = chooseBotMove();
  if (i==null) return;
  const cellEl = boardEl.children[2 + i];
  state.grid[i] = currentPlayer();
  state.moves++;
  placeGlyph(cellEl, currentPlayer());

  // Card overflow elimination rule for bots
  const hand = state.hands.get(currentPlayer()) || [];
  if (hand.length > 10){
    eliminatePlayer(currentPlayer(), 'Too many cards (>10)');
  }

  const win = checkWin(state.grid, state.size, getWinLength());
  if (win){ endGame(currentPlayer(), win.line, false); return; }

  if (state.moves + state.blocked.size === state.grid.length){
    statusEl.textContent = 'Draw';
    scores.D++; renderScores(); stopTurnTimer(); return;
  }

  if (config.mode==='randomizer') drawFor(currentPlayer(), 1);

  advanceTurn();
}

// Choose bot move heuristic
function chooseBotMove(){
  const size = state.size, winLen = getWinLength();
  const grid = state.grid.slice();
  const me = currentPlayer();
  const opp = state.order.filter(p=>p!==me && !eliminated.has(p));

  const winMove = findLineCompletion(grid, size, winLen, me); if (winMove!=null) return winMove;
  for (const o of opp){
    const blockMove = findLineCompletion(grid, size, winLen, o); if (blockMove!=null) return blockMove;
  }
  const empties = grid.map((v,idx)=> (v||state.blocked.has(idx))?null:idx).filter(v=>v!=null);
  if (!empties.length) return null;
  return empties[Math.floor(Math.random()*empties.length)];
}
function findLineCompletion(grid,size,winLen,player){
  for(let i=0;i<grid.length;i++){ if(grid[i] || state.blocked.has(i)) continue; grid[i]=player; const w=checkWin(grid,size,winLen); grid[i]=null; if(w&&w.player===player) return i; }
  return null;
}

// Glyph
function placeGlyph(el, player){
  el.innerHTML = `<span class="glyph ${symbolClass(player)}">${player}</span>`;
  el.classList.add('played');
}

// Win handling
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
  const first = line[0], last = line[line.length-1];
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
  beam.style.transform = `rotate(${angle}deg)`;
  boardEl.appendChild(beam);
  setTimeout(()=>beam.remove(), 2600);
}

// Scores
function renderScores(){
  document.getElementById('scoreX').textContent = String(scores.X);
  document.getElementById('scoreO').textContent = String(scores.O);
  document.getElementById('scoreP3').textContent = String(scores.P3);
  document.getElementById('scoreP4').textContent = String(scores.P4);
  document.getElementById('scoreD').textContent = String(scores.D);
}

// Win detection NxN
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

// Cards
const Rarity = { COMMON:'common', RARE:'rare', EPIC:'epic', LEGEND:'legend', MYTH:'myth' };
function card(name, rarity, desc, apply){ return { name, rarity, desc, apply }; }
function buildDeck(){
  const cards = [
    // COMMON
    card('Block', Rarity.COMMON, 'Block a chosen empty cell', ()=>blockChosenCell()),
    card('Clear', Rarity.COMMON, 'Clear a chosen mark', ()=>clearChosenMark()),
    card('Swap Order', Rarity.COMMON, 'Reverse turn order', ()=>reverseTurnOrder()),
    card('Fog', Rarity.COMMON, 'Global fog for 2 rounds (only you see)', ()=>applyFog(currentPlayer(),2)),
    // RARE
    card('Double Block', Rarity.RARE, 'Block two chosen cells', ()=>blockChosenCells(2)),
    card('Undo', Rarity.RARE, 'Undo last move', ()=>undoLastMove()),
    // EPIC chess/checkers with control
    card('Knight', Rarity.EPIC, 'Spawn a Knight you can move each turn', ()=>spawnPieceWithControl('Knight',3)),
    card('Bishop', Rarity.EPIC, 'Spawn a Bishop you can move each turn', ()=>spawnPieceWithControl('Bishop',3)),
    card('Rook', Rarity.EPIC, 'Spawn a Rook you can move each turn', ()=>spawnPieceWithControl('Rook',3)),
    card('Checker', Rarity.EPIC, 'Spawn a Checker you can move/capture', ()=>spawnPieceWithControl('Checker',3)),
    // LEGEND
    card('Storm', Rarity.LEGEND, 'Clear 3 random opponent marks', ()=>clearRandomOpponentMarks(3)),
    // MYTH (Mafia Night one-time)
    card('Mafia Night', Rarity.MYTH, 'Once per game: start Mafia night mechanics', ()=>mafiaNightCard())
  ];
  state.deck = cards.concat(cards).concat(cards); // simple expansion
  shuffle(state.deck);
}
function dealStartingHands(){
  for(const p of state.order){
    state.hands.set(p, draw(2));
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
function shuffle(arr){ for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; } }

function updateCardsTray(){
  const p = currentPlayer();
  const hand = state.hands.get(p) || [];
  // Only show in randomizer mode
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
      // Mafia Night card one-time rule
      if (c.name==='Mafia Night' && mafiaCardUsed){ showEffect('Mafia Night', 'Already used once'); return; }

      playCardEffect(c);

      // consume
      hand.splice(idx,1);
      state.hands.set(p, hand);
      updateCardsTray();

      // End or continue turn
      if (!state.extraTurns || state.extraTurns<=0) advanceTurn();
      else state.extraTurns--;
    });
    cardsTray.appendChild(el);
  });
}

function playCardEffect(card){
  showEffect(card.name, `Rarity: ${card.rarity.toUpperCase()} — ${card.desc}`);
  try{ card.apply(); }catch(e){}
  animateBoardPulse();
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

// Basic card actions
function reverseTurnOrder(){
  const cp = currentPlayer();
  state.order.reverse();
  state.turnIndex = state.order.indexOf(cp);
  updateStatus();
}
function pickCells(n, done, requireHasMark=false){
  let picked = [];
  const cells = Array.from(boardEl.children).slice(2);
  cells.forEach((el, idx)=>{
    const hasMark = !!state.grid[idx];
    const ok = requireHasMark ? hasMark : true;
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
function blockChosenCell(){ pickCells(1, (indices)=> indices.forEach(i=>blockIndex(i)) ); }
function blockChosenCells(n){ pickCells(n, (idxs)=> idxs.forEach(i=>blockIndex(i)) ); }
function blockIndex(i){
  if (state.blocked.has(i)) return;
  state.blocked.add(i);
  const el = boardEl.children[2 + i];
  el.setAttribute('disabled','true');
  el.style.opacity = .45; el.style.filter = 'grayscale(0.3) brightness(0.8)';
  el.title = 'Blocked';
}
function clearChosenMark(){ pickCells(1, (idxs)=> idxs.forEach(i=>clearMark(i)), true); }
function clearRandomOpponentMarks(n){
  const opponents = state.order.filter(p=>p!==currentPlayer());
  const marks = [];
  for(let i=0;i<state.grid.length;i++){
    if (state.grid[i] && opponents.includes(state.grid[i])) marks.push(i);
  }
  shuffle(marks);
  for(let k=0;k<n && marks.length;k++){ clearMark(marks.pop()); }
}
function clearMark(i){
  if (!state.grid[i]) return;
  if (state.shields && state.shields.has(i)) return;
  state.grid[i] = null;
  const el = boardEl.children[2 + i];
  el.innerHTML=''; el.classList.remove('played','win');
}
function undoLastMove(){
  for(let i=state.grid.length-1; i>=0; i--){
    if (boardEl.children[2 + i].classList.contains('played')){
      clearMark(i); break;
    }
  }
}
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

// Chess/checkers controllable piece
function spawnPieceWithControl(type, rounds){
  const empties = emptyIndices();
  if (!empties.length) return;
  const idx = empties[Math.floor(Math.random()*empties.length)];
  state.pieces.push({type, idx, rounds});
  const el = boardEl.children[2 + idx];
  const badge = document.createElement('div');
  badge.className='piece';
  badge.textContent = type;
  el.appendChild(badge);

  activePieceControl = { owner: currentPlayer(), type, idx, roundsLeft: rounds, capturedThisTurn:false };
  showEffect(`${type} ready`, 'Green tiles show valid moves');
  showMoveTargets(activePieceControl);
}
function showMoveTargets(ctrl){
  removeAllHints();
  const targets = validTargets(ctrl);
  targets.forEach(i=>{
    const el = boardEl.children[2 + i];
    const hint = document.createElement('div');
    hint.className='moveHint';
    el.appendChild(hint);
  });
}
function removeAllHints(){
  Array.from(boardEl.children).slice(2).forEach(el=>{
    const h = el.querySelector('.moveHint'); if (h) h.remove();
  });
}
function pieceCanMove(ctrl, targetIdx){
  return validTargets(ctrl).includes(targetIdx);
}
function validTargets(ctrl){
  const s = state.size;
  const idx = ctrl.idx;
  const r0 = Math.floor(idx/s), c0 = idx % s;
  const targets = [];
  const push=(ri,ci)=>{
    if (ri<0||ci<0||ri>=s||ci>=s) return;
    const ti = ri*s+ci;
    if (state.blocked.has(ti)) return; // walls
    // cannot move onto own mark; can capture one opponent mark (except checker which can chain captures)
    if (state.grid[ti] && state.grid[ti]===ctrl.owner) return;
    targets.push(ti);
  };

  if (ctrl.type==='Knight'){
    const deltas=[[2,1],[2,-1],[-2,1],[-2,-1],[1,2],[1,-2],[-1,2],[-1,-2]];
    deltas.forEach(([dr,dc])=>push(r0+dr,c0+dc));
  } else if (ctrl.type==='Bishop'){
    [[1,1],[1,-1],[-1,1],[-1,-1]].forEach(([dr,dc])=>{
      for(let k=1;k<s;k++){ push(r0+dr*k, c0+dc*k); }
    });
  } else if (ctrl.type==='Rook'){
    [[1,0],[-1,0],[0,1],[0,-1]].forEach(([dr,dc])=>{
      for(let k=1;k<s;k++){ push(r0+dr*k, c0+dc*k); }
    });
  } else if (ctrl.type==='Checker'){
    [[1,1],[1,-1],[-1,1],[-1,-1]].forEach(([dr,dc])=>push(r0+dr, c0+dc));
  }
  return targets;
}
function moveControlledPiece(ctrl, targetIdx){
  // Allow capture only one per turn except checker (can chain by multiple moves, we simplify: one capture per click)
  const hasOpponent = state.grid[targetIdx] && state.grid[targetIdx] !== ctrl.owner;
  if (hasOpponent && ctrl.type!=='Checker' && ctrl.capturedThisTurn) return; // already captured
  if (hasOpponent){ clearMark(targetIdx); ctrl.capturedThisTurn = true; }

  const fromCell = boardEl.children[2 + ctrl.idx];
  const toCell = boardEl.children[2 + targetIdx];
  const badge = fromCell.querySelector('.piece'); if (badge) badge.remove();

  const newBadge = document.createElement('div');
  newBadge.className='piece';
  newBadge.textContent = ctrl.type;
  toCell.appendChild(newBadge);
  ctrl.idx = targetIdx;

  showMoveTargets(ctrl);
  showEffect('Piece moved', `${ctrl.type} repositioned`);
}
function chooseControlledPieceMove(ctrl){
  const candidates = validTargets(ctrl);
  if (!candidates.length) return null;
  const captures = candidates.filter(i=>state.grid[i] && state.grid[i]!==ctrl.owner);
  return (captures[0] ?? candidates[Math.floor(Math.random()*candidates.length)]);
}

// Mafia Night mechanics
function mafiaNightCard(){
  if (mafiaCardUsed){ showEffect('Mafia Night', 'Already used once'); return; }
  mafiaCardUsed = true;
  startMafiaNight();
}
function startMafiaNight(){
  // Assign mafia randomly among active players
  const pool = state.order.filter(p=>!eliminated.has(p));
  mafiaRole = pool[Math.floor(Math.random()*pool.length)];
  mafiaPhase = true;
  showEffect('Mafia Night', mafiaRole===currentPlayer() ? 'You are mafia. Choose 3 marks to eliminate.' : 'Darkness falls…');
}
function triggerMafiaCadence(){
  // Auto night: mafia chooses 3 pieces to eliminate
  startMafiaNight();
  // Mafia elimination: pick up to 3 opponent marks; if someone has <3 marks, mafia may eliminate the player
  const marks = [];
  for(let i=0;i<state.grid.length;i++){
    const v = state.grid[i];
    if (v && v!==mafiaRole) marks.push(i);
  }
  shuffle(marks);
  const killCount = Math.min(3, marks.length);
  for(let k=0;k<killCount;k++){ clearMark(marks[k]); }

  // If any player has <3 marks on board, mafia may eliminate that player
  for (const p of state.order){
    if (p===mafiaRole || eliminated.has(p)) continue;
    const count = state.grid.filter(v=>v===p).length;
    if (count < 3){
      eliminatePlayer(p, 'Mafia elimination (few marks)');
    }
  }

  // Sheriff check: randomly reveal role of one player to sheriff (we show message only to sheriff if it exists; here first player acts as sheriff)
  const sheriff = state.order[0];
  const candidates = state.order.filter(p=>p!==sheriff && !eliminated.has(p));
  if (candidates.length){
    sheriffRevealed = candidates[Math.floor(Math.random()*candidates.length)];
    if (currentPlayer()===sheriff){
      showEffect('Sheriff check', `${sheriffRevealed} is ${sheriffRevealed===mafiaRole?'MAFIA':'NOT mafia'}`);
    } else {
      showEffect('Sheriff check', 'Someone was checked.');
    }
  }

  // Voting: simple majority vote against a random suspect (demo)
  votes.clear();
  const suspects = state.order.filter(p=>!eliminated.has(p));
  if (suspects.length){
    const suspect = suspects[Math.floor(Math.random()*suspects.length)];
    for (const voter of suspects){ if (voter!==suspect) votes.set(suspect, (votes.get(suspect)||0)+1); }
    const majority = votes.get(suspect) >= Math.ceil(suspects.length/2);
    if (majority){
      if (suspect===mafiaRole){
        // Mafia voted out: remaining players win immediately
        for (const p of state.order){ if (!eliminated.has(p) && p!==mafiaRole) { endGame(p, [], false); break; } }
      } else {
        eliminatePlayer(suspect, 'Voted out');
      }
    }
  }

  mafiaPhase = false;
  mafiaRole = null;
}

// Elimination
function eliminatePlayer(player, reason){
  eliminated.add(player);
  // Remove all their marks
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
  }
}

// Tick durations
function tickDurations(){
  if (fogRoundsLeft>0){
    fogRoundsLeft--;
    if (fogRoundsLeft<=0) unfogBoard();
  }

  // Passive piece sweeps per turn (light)
  for (const piece of state.pieces){
    piece.rounds--;
  }
  state.pieces = state.pieces.filter(p=>p.rounds>0);
  Array.from(boardEl.children).slice(2).forEach(el=>{
    const badge = el.querySelector('.piece');
    if (badge){
      const idx = Number(el.dataset.idx);
      const alive = state.pieces.find(p=>p.idx===idx);
      if (!alive) badge.remove();
    }
  });

  // Reset capture flag each turn
  if (activePieceControl) activePieceControl.capturedThisTurn = false;
}

// Utils
function emptyIndices(){ return state.grid.map((v,i)=> v||state.blocked.has(i)?null:i).filter(v=>v!=null); }
function randomInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min }
function sleep(ms){ return new Promise(res=>setTimeout(res, ms)) }

// Controls
resetBtn.addEventListener('click', ()=> initGame(false));
backBtn.addEventListener('click', ()=> window.location.href = 'index.html');
refreshBtn.addEventListener('click', ()=> location.reload());
</script>
</body>
</html>
```
