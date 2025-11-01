# MULTIVERSAL AU CLICKER

A compact prototype inspired by UNDERTALE: AU CLICKER. Click to gather AU Energy, buy upgrades, unlock AUs, and prestige for permanent boosts.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>MULTIVERSAL AU CLICKER</title>
<style>
  :root{
    --bg:#0b0e14; --panel:#151822; --text:#e6e6e6; --muted:#a7b0c0;
    --accent:#7aa2f7; --accent2:#c28fff; --good:#73d13d; --warn:#ffce63;
    --border:rgba(122,162,247,.28); --shadow:0 12px 36px rgba(0,0,0,.55);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0;
    background:
      radial-gradient(900px 420px at 65% -10%, #14203a, var(--bg) 60%);
    color:var(--text);
    font-family:ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;
    display:grid; place-items:center;
  }
  .wrap{display:grid;gap:16px;justify-items:center;width:100%}
  .panel{
    background:var(--panel);
    border:1px solid var(--border);
    border-radius:14px;
    padding:18px 20px;
    box-shadow:var(--shadow);
    width:min(880px, 94vw);
  }
  .title{
    margin:0; text-align:center; font-weight:800;
    background:linear-gradient(90deg,var(--accent),var(--accent2));
    -webkit-background-clip:text; background-clip:text; color:transparent;
    letter-spacing:.3px;
  }
  .row{display:flex;gap:12px;align-items:center;justify-content:center;flex-wrap:wrap;margin-top:10px}
  .stat{border:1px solid rgba(255,255,255,.12);background:rgba(255,255,255,.06);padding:8px 12px;border-radius:999px;color:var(--muted);font-size:14px}
  .big{font-size:24px;color:#fff}
  .btn{
    border:1px solid var(--border);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:#fff; padding:12px 16px; border-radius:12px; cursor:pointer; transition:.15s;
  }
  .btn:hover{transform:translateY(-2px);background:linear-gradient(180deg,#14182a,#0f1325)}
  .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:12px;margin-top:16px}
  .card{border:1px solid var(--border);border-radius:12px;background:rgba(255,255,255,.06);padding:12px}
  .card h3{margin:0 0 6px}
  .card p{margin:0;color:var(--muted);font-size:13px}
  .card .buy{margin-top:10px}
  .disabled{opacity:.45;cursor:not-allowed}
  .au-list{display:grid;gap:8px;margin-top:16px}
  .au{
    display:flex;justify-content:space-between;align-items:center;
    padding:8px;border-radius:10px;background:rgba(255,255,255,.06);
    border:1px solid rgba(255,255,255,.12)
  }
  .note{color:var(--muted); font-size:12px; text-align:center; margin-top:6px}
</style>
</head>
<body>
  <div class="wrap">
    <div class="panel">
      <h1 class="title">MULTIVERSAL AU CLICKER</h1>

      <div class="row">
        <div class="stat big" id="energyStat">0 AU Energy</div>
        <div class="stat">Per Click: <span id="perClick">1</span></div>
        <div class="stat">Auto: <span id="autoRate">0/s</span></div>
        <div class="stat">Prestige: <span id="prestige">0</span></div>
      </div>

      <div class="row">
        <button class="btn" id="clickBtn">Click!</button>
        <button class="btn" id="saveBtn">Save</button>
        <button class="btn" id="loadBtn">Load</button>
        <button class="btn" id="resetBtn">Reset</button>
      </div>

      <div class="grid">
        <div class="card">
          <h3>Auto‑Clicker</h3>
          <p>Adds passive AU Energy per second.</p>
          <div class="buy">
            <button class="btn disabled" id="buyAuto">Buy (50 AU)</button>
          </div>
        </div>
        <div class="card">
          <h3>Multiplier</h3>
          <p>Increases energy per click (x5).</p>
          <div class="buy">
            <button class="btn disabled" id="buyMult">Buy (200 AU)</button>
          </div>
        </div>
        <div class="card">
          <h3>Prestige</h3>
          <p>Reset progress for a permanent +25% boost to all gains.</p>
          <div class="buy">
            <button class="btn disabled" id="buyPrestige">Prestige (10,000 AU)</button>
          </div>
        </div>
      </div>

      <h3 style="margin:16px 0 8px">Unlocked AUs</h3>
      <div class="au-list" id="auList"></div>
      <div class="note">Tip: Unlocked AUs are cosmetic here; expand with skins, particles, or idle bonuses.</div>
    </div>
  </div>

<script>
const energyStat = document.getElementById('energyStat');
const perClickEl = document.getElementById('perClick');
const autoRateEl = document.getElementById('autoRate');
const prestigeEl = document.getElementById('prestige');

const clickBtn = document.getElementById('clickBtn');
const saveBtn = document.getElementById('saveBtn');
const loadBtn = document.getElementById('loadBtn');
const resetBtn = document.getElementById('resetBtn');

const buyAuto = document.getElementById('buyAuto');
const buyMult = document.getElementById('buyMult');
const buyPrestige = document.getElementById('buyPrestige');

const auList = document.getElementById('auList');

let state = {
  energy: 0,
  perClick: 1,
  autoRate: 0,
  prestige: 0,
  unlocked: []
};

const AUs = [
  {name:'Classic Sans', cost: 100},
  {name:'Underfell', cost: 500},
  {name:'Swap Sans', cost: 2000},
  {name:'Dust', cost: 10000},
  {name:'Error', cost: 25000}
];

function render(){
  energyStat.textContent = format(state.energy) + ' AU Energy';
  perClickEl.textContent = state.perClick;
  autoRateEl.textContent = state.autoRate + '/s';
  prestigeEl.textContent = state.prestige;

  toggleBtn(buyAuto, state.energy >= 50 && state.autoRate === 0);
  toggleBtn(buyMult, state.energy >= 200 && state.perClick === 1);
  toggleBtn(buyPrestige, state.energy >= 10000);

  auList.innerHTML = '';
  AUs.forEach(au=>{
    const unlocked = state.unlocked.includes(au.name);
    const canUnlock = state.energy >= au.cost && !unlocked;
    const el = document.createElement('div');
    el.className = 'au';
    el.innerHTML = `<span>${au.name}</span><span>${unlocked ? 'Unlocked' : format(au.cost)+' AU'}</span>`;
    if (canUnlock){
      el.style.borderColor = 'rgba(115,209,61,.35)';
      el.style.boxShadow = '0 0 18px rgba(115,209,61,.18) inset';
      el.style.cursor = 'pointer';
      el.onclick = ()=>{
        state.energy -= au.cost;
        state.unlocked.push(au.name);
        render();
      };
    }
    auList.appendChild(el);
  });
}

function toggleBtn(btn, enabled){
  btn.classList.toggle('disabled', !enabled);
}

function gainMultiplier(){
  return 1 + state.prestige * 0.25;
}

clickBtn.onclick = ()=>{
  const gain = Math.max(1, Math.floor(state.perClick * gainMultiplier()));
  state.energy += gain;
  render();
};

let autoTimer = null;
buyAuto.onclick = ()=>{
  if (state.energy >= 50 && state.autoRate === 0){
    state.energy -= 50;
    state.autoRate = 1;
    if (!autoTimer){
      autoTimer = setInterval(()=>{
        state.energy += state.autoRate * gainMultiplier();
        render();
      }, 1000);
    }
    render();
  }
};

buyMult.onclick = ()=>{
  if (state.energy >= 200 && state.perClick === 1){
    state.energy -= 200;
    state.perClick = 5;
    render();
  }
};

buyPrestige.onclick = ()=>{
  if (state.energy >= 10000){
    // soft reset with prestige increment
    state.energy = 0;
    state.perClick = 1;
    state.autoRate = 0;
    state.unlocked = [];
    state.prestige += 1;
    if (autoTimer){ clearInterval(autoTimer); autoTimer = null; }
    render();
  }
};

saveBtn.onclick = ()=>{
  localStorage.setItem('auclicker_save', JSON.stringify(state));
};

loadBtn.onclick = ()=>{
  const raw = localStorage.getItem('auclicker_save');
  if (raw){
    try{
      const parsed = JSON.parse(raw);
      state = Object.assign({energy:0, perClick:1, autoRate:0, prestige:0, unlocked:[]}, parsed);
    }catch(e){}
    if (state.autoRate > 0 && !autoTimer){
      autoTimer = setInterval(()=>{
        state.energy += state.autoRate * gainMultiplier();
        render();
      }, 1000);
    }
    render();
  }
};

resetBtn.onclick = ()=>{
  if (confirm('Reset progress?')){
    state = {energy:0, perClick:1, autoRate:0, prestige:0, unlocked:[]};
    if (autoTimer){ clearInterval(autoTimer); autoTimer = null; }
    render();
  }
};

function format(n){
  if (n < 1000) return String(Math.floor(n));
  const units = ['K','M','B','T','Q'];
  let idx = -1;
  let val = n;
  while (val >= 1000 && idx < units.length-1){
    val /= 1000; idx++;
  }
  return val.toFixed(val>=100?0: val>=10?1:2) + ' ' + units[idx];
}

render();
</script>
</body>
</html>
