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
    justify-content:flex-start;
    min-height:100vh;
    padding:20px;
  }
  h1 { margin-bottom:20px; }
  #auDisplay { font-size:24px; margin:12px 0; }
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
  .upgrade, .characters { margin-top:20px; width:100%; max-width:400px; }
  .char {
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:8px 12px;
    margin:6px 0;
    border:1px solid rgba(122,162,247,.28);
    border-radius:8px;
    background:#161b22;
  }
</style>
</head>
<body>
  <h1>AU Clicker</h1>
  <div id="auDisplay">AU: 0</div>
  <button id="clickBtn">Click for AU</button>

  <div class="upgrade">
    <button id="buyUpgrade">Buy Upgrade (Cost: 50 AU)</button>
    <div id="upgradeInfo">Current multiplier: x1</div>
  </div>

  <div class="characters">
    <h2>Characters</h2>
    <div id="charList"></div>
  </div>

<script>
  let au = 0;
  let multiplier = 1;
  let upgradeCost = 50;

  const auDisplay = document.getElementById('auDisplay');
  const clickBtn = document.getElementById('clickBtn');
  const buyUpgrade = document.getElementById('buyUpgrade');
  const upgradeInfo = document.getElementById('upgradeInfo');
  const charList = document.getElementById('charList');

  // Character definitions
  const characters = [
    { name: "Classic Sans", cost: 100, aps: 1, owned: 0 },
    { name: "Underswap Papyrus", cost: 500, aps: 5, owned: 0 },
    { name: "Dust Sans", cost: 2000, aps: 20, owned: 0 },
    { name: "Error Sans", cost: 10000, aps: 100, owned: 0 }
  ];

  function updateDisplay(){
    auDisplay.textContent = 'AU: ' + au;
    upgradeInfo.textContent = 'Current multiplier: x' + multiplier;
    buyUpgrade.textContent = 'Buy Upgrade (Cost: ' + upgradeCost + ' AU)';
    renderCharacters();
  }

  function renderCharacters(){
    charList.innerHTML = "";
    characters.forEach((c, i)=>{
      const div = document.createElement('div');
      div.className = 'char';
      div.innerHTML = `
        <span>${c.name} (x${c.owned})</span>
        <button onclick="buyChar(${i})">Buy (${c.cost} AU)</button>
      `;
      charList.appendChild(div);
    });
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

  window.buyChar = function(index){
    const c = characters[index];
    if(au >= c.cost){
      au -= c.cost;
      c.owned++;
      c.cost = Math.floor(c.cost * 1.5);
      updateDisplay();
    }
  }

  // Passive AU gain loop
  setInterval(()=>{
    let totalAPS = characters.reduce((sum, c)=> sum + c.aps * c.owned, 0);
    au += totalAPS;
    updateDisplay();
  }, 1000);

  updateDisplay();
</script>
</body>
</html>
