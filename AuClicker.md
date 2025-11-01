<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AU Clicker</title>
<style>
  body {
    margin:0;
    font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
    background:#0d1117;
    color:#e6e6e6;
    height:100vh;
    display:grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-template-rows: auto 1fr;
    grid-template-areas:
      "stats soul aus"
      "stats menu aus";
  }
  #stats { grid-area: stats; padding:20px; }
  #soul { grid-area: soul; display:flex; flex-direction:column; align-items:center; justify-content:center; }
  #aus { grid-area: aus; padding:20px; }
  #menu { grid-area: menu; display:flex; justify-content:center; gap:12px; padding:10px; }

  h1 { margin-bottom:12px; }
  .stat { margin:6px 0; }
  button {
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:#e6e6e6;
    padding:10px 16px;
    border-radius:12px;
    cursor:pointer;
    margin:6px;
    transition:transform .08s ease, box-shadow .15s ease;
  }
  button:hover {
    transform:translateY(-1px);
    box-shadow:0 10px 24px rgba(122,162,247,.22);
  }
  #soulBtn {
    width:120px; height:120px;
    border-radius:50%;
    font-size:20px;
  }
</style>
</head>
<body>
  <!-- Left stats -->
  <div id="stats">
    <h1>Stats</h1>
    <div class="stat" id="lvDisplay">LV: 1</div>
    <div class="stat" id="auDisplay">AU: 0</div>
    <div class="stat" id="multiplierDisplay">Multiplier: x1</div>
  </div>

  <!-- Center soul -->
  <div id="soul">
    <button id="soulBtn">SOUL</button>
  </div>

  <!-- Right AU unlocks -->
  <div id="aus">
    <h1>AU Select</h1>
    <button id="buyUpgrade">Buy Upgrade (Cost: 50 AU)</button>
    <div id="upgradeInfo">Current multiplier: x1</div>
  </div>

  <!-- Menu under soul -->
  <div id="menu">
    <button id="resetBtn">Reset</button>
    <button id="saveBtn">Save</button>
    <button id="loadBtn">Load</button>
  </div>

<script>
  let au = 0;
  let multiplier = 1;
  let upgradeCost = 50;
  let lv = 1;

  const auDisplay = document.getElementById('auDisplay');
  const lvDisplay = document.getElementById('lvDisplay');
  const multiplierDisplay = document.getElementById('multiplierDisplay');
  const soulBtn = document.getElementById('soulBtn');
  const buyUpgrade = document.getElementById('buyUpgrade');
  const upgradeInfo = document.getElementById('upgradeInfo');

  function updateDisplay(){
    auDisplay.textContent = 'AU: ' + au;
    lvDisplay.textContent = 'LV: ' + lv;
    multiplierDisplay.textContent = 'Multiplier: x' + multiplier;
    upgradeInfo.textContent = 'Current multiplier: x' + multiplier;
    buyUpgrade.textContent = 'Buy Upgrade (Cost: ' + upgradeCost + ' AU)';
  }

  soulBtn.addEventListener('click', ()=>{
    au += 1 * multiplier;
    if(au >= lv * 100) { lv++; }
    updateDisplay();
  });

  buyUpgrade.addEventListener('click', ()=>{
    if(au >= upgradeCost){
      au -= upgradeCost;
      multiplier++;
      upgradeCost = Math.floor(upgradeCost * 1.8);
      updateDisplay();
    }
  });

  // Save/Load/Reset
  document.getElementById('saveBtn').addEventListener('click', ()=>{
    localStorage.setItem('auClickerSave', JSON.stringify({au, multiplier, upgradeCost, lv}));
  });
  document.getElementById('loadBtn').addEventListener('click', ()=>{
    const save = JSON.parse(localStorage.getItem('auClickerSave'));
    if(save){
      au = save.au; multiplier = save.multiplier; upgradeCost = save.upgradeCost; lv = save.lv;
      updateDisplay();
    }
  });
  document.getElementById('resetBtn').addEventListener('click', ()=>{
    au = 0; multiplier = 1; upgradeCost = 50; lv = 1;
    updateDisplay();
    localStorage.removeItem('auClickerSave');
  });

  updateDisplay();
</script>
</body>
</html>
