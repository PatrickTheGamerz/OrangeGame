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
    display:flex;
    align-items:center;
    justify-content:center;
    height:100vh;
  }
  .layout {
    display:grid;
    grid-template-columns: 1fr auto 1fr;
    align-items:center;
    width:100%;
    max-width:1000px;
  }
  #auDisplay {
    font-size:24px;
    justify-self:start;
    margin-left:20px;
  }
  .center {
    display:flex;
    flex-direction:column;
    align-items:center;
    text-align:center;
  }
  h1 { margin-bottom:20px; }
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
  .upgrade {
    display:flex;
    flex-direction:column;
    align-items:flex-end;
    margin-right:20px;
  }
</style>
</head>
<body>
  <div class="layout">
    <!-- Left: AU counter -->
    <div id="auDisplay">AU: 0</div>

    <!-- Center: main clicker -->
    <div class="center">
      <h1>AU Clicker</h1>
      <button id="clickBtn">Click for AU</button>
    </div>

    <!-- Right: upgrades -->
    <div class="upgrade">
      <button id="buyUpgrade">Buy Upgrade (Cost: 50 AU)</button>
      <div id="upgradeInfo">Current multiplier: x1</div>
    </div>
  </div>

<script>
  let au = 0;
  let multiplier = 1;
  let upgradeCost = 50;

  const auDisplay = document.getElementById('auDisplay');
  const clickBtn = document.getElementById('clickBtn');
  const buyUpgrade = document.getElementById('buyUpgrade');
  const upgradeInfo = document.getElementById('upgradeInfo');

  function updateDisplay(){
    auDisplay.textContent = 'AU: ' + au;
    upgradeInfo.textContent = 'Current multiplier: x' + multiplier;
    buyUpgrade.textContent = 'Buy Upgrade (Cost: ' + upgradeCost + ' AU)';
  }

  clickBtn.addEventListener('click', ()=>{
    au += 1 * multiplier;
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

  updateDisplay();
</script>
</body>
</html>
