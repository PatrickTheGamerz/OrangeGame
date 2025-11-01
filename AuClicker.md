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
    flex-direction:column;
    align-items:center;
    min-height:100vh;
    padding:20px;
  }
  h1 { margin:10px 0; }
  #goldDisplay { font-size:24px; margin:12px 0; }
  #timelineDisplay { font-size:18px; margin:8px 0; color:#9cdcfe; }

  /* Heart clicker */
  #clickBtn {
    width:80px;
    height:80px;
    background:red;
    clip-path: polygon(50% 15%, 61% 0, 75% 0, 100% 25%, 100% 50%, 50% 100%, 0 50%, 0 25%, 25% 0, 39% 0);
    border:none;
    cursor:pointer;
    margin:20px;
    transition:transform .1s ease;
  }
  #clickBtn:hover { transform:scale(1.1); }

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

  .panel {
    margin-top:20px;
    width:100%;
    max-width:500px;
    background:#161b22;
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
    padding:12px;
  }
  .char {
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:8px 12px;
    margin:6px 0;
    border:1px solid rgba(122,162,247,.28);
    border-radius:8px;
    background:#0d1117;
  }
</style>
</head>
<body>
  <h1>AU Clicker</h1>
  <div id="goldDisplay">Gold: 0 G</div>
  <div id="timelineDisplay">Timeline: Undertale</div>
  <button id="clickBtn"></button>

  <div class="panel">
    <h2>Upgrades</h2>
    <button id="buyUpgrade">Buy Upgrade (Cost: 50 G)</button>
    <div id="upgradeInfo">Current multiplier: x1</div>
  </div>

  <div class="panel">
    <h2>Characters</h2>
    <div id="charList"></div>
  </div>

  <div class="panel">
    <h2>Reset Timeline</h2>
    <button id="resetBtn">Reset (Cost: 10,000 G)</button>
    <div id="resetInfo">Resets unlock new AUs and boost Gold gain</div>
  </div>

<script>
  let gold = 0;
  let multiplier = 1;
  let upgradeCost = 50;
  let resetCost = 10000;
  let timelineIndex = 0;
  const timelines = ["Undertale", "Underswap", "Underfell"];

  const goldDisplay = document.getElementById('goldDisplay');
  const clickBtn = document.getElementById('clickBtn');
  const buyUpgrade = document.getElementById('buyUpgrade');
  const upgradeInfo = document.getElementById('upgradeInfo');
  const charList = document.getElementById('charList');
  const resetBtn = document.getElementById('resetBtn');
  const timelineDisplay = document.getElementById('timelineDisplay');

  // AU character rosters
  const roster = {
    "Undertale": [
      { name: "Frisk", cost: 100, gps: 1, owned: 0 },
      { name: "Sans", cost: 500, gps: 5, owned: 0 },
      { name: "Papyrus", cost: 2000, gps: 20, owned: 0 }
    ],
    "Underswap": [
      { name: "Swap Sans", cost: 200, gps: 2, owned: 0 },
      { name: "Swap Papyrus", cost: 1000, gps: 10, owned: 0 },
      { name: "Swap Toriel", cost: 5000, gps: 50, owned: 0 }
    ],
    "Underfell": [
      { name: "Fell Sans", cost: 500, gps: 5, owned: 0 },
      { name: "Fell Papyrus", cost: 2500, gps: 25, owned: 0 },
      { name: "Fell Undyne", cost: 10000, gps: 100, owned: 0 }
    ]
  };

  function currentRoster(){
    return roster[timelines[timelineIndex]];
  }

  function updateDisplay(){
    goldDisplay.textContent = 'Gold: ' + gold + ' G';
    upgradeInfo.textContent = 'Current multiplier: x' + multiplier;
    buyUpgrade.textContent = 'Buy Upgrade (Cost: ' + upgradeCost + ' G)';
    resetBtn.textContent = 'Reset (Cost: ' + resetCost + ' G)';
    timelineDisplay.textContent = 'Timeline: ' + timelines[timelineIndex];
    renderCharacters();
  }

  function renderCharacters(){
    charList.innerHTML = "";
    currentRoster().forEach((c, i)=>{
      const div = document.createElement('div');
      div.className = 'char';
      div.innerHTML = `
        <span>${c.name} (x${c.owned})</span>
        <button onclick="buyChar(${i})">Buy (${c.cost} G)</button>
      `;
      charList.appendChild(div);
    });
  }

  clickBtn.addEventListener('click', ()=>{
    gold += 1 * multiplier;
    updateDisplay();
  });

  buyUpgrade.addEventListener('click', ()=>{
    if(gold >= upgradeCost){
      gold -= upgradeCost;
      multiplier++;
      upgradeCost = Math.floor(upgradeCost * 1.8);
      updateDisplay();
    }
  });

  window.buyChar = function(index){
    const c = currentRoster()[index];
    if(gold >= c.cost){
      gold -= c.cost;
      c.owned++;
      c.cost = Math.floor(c.cost * 1.5);
      updateDisplay();
    }
  }

  resetBtn.addEventListener('click', ()=>{
    if(gold >= resetCost){
      gold = 0;
      multiplier = 1;
      upgradeCost = 50;
      resetCost = Math.floor(resetCost * 2.5);
      timelineIndex = (timelineIndex + 1) % timelines.length;
      // reset character ownership
      Object.values(roster).forEach(list => list.forEach(c => { c.owned = 0; c.cost = Math.floor(c.cost); }));
      updateDisplay();
    }
  });

  // Passive Gold gain loop
  setInterval(()=>{
    let totalGPS = currentRoster().reduce((sum, c)=> sum + c.gps * c.owned, 0);
    gold += totalGPS;
    updateDisplay();
  }, 1000);

  updateDisplay();
</script>
</body>
</html>
