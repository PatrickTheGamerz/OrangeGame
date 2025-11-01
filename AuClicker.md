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
    height:100vh;
  }

  /* Left stats panel */
  #statsPanel {
    width:240px;
    background:#161b22;
    border-right:1px solid rgba(122,162,247,.28);
    padding:16px;
    display:flex;
    flex-direction:column;
    gap:12px;
  }
  #statsPanel h2 { margin:0 0 8px 0; font-size:18px; }
  .stat { font-size:16px; }

  /* Center main area */
  #mainArea {
    flex:1;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    position:relative;
  }
  #soul {
    width:160px;
    height:160px;
    background:linear-gradient(145deg, #ff4d4d, #b30000);
    clip-path: polygon(50% 15%, 61% 0, 75% 0, 100% 25%, 100% 50%, 50% 100%, 0 50%, 0 25%, 25% 0, 39% 0);
    cursor:pointer;
    transition:transform .15s ease;
    box-shadow:0 0 20px rgba(255,0,0,.6);
  }
  #soul:hover { transform:scale(1.1); }
  #menuBtn {
    margin-top:20px;
    padding:10px 20px;
    border-radius:8px;
    border:1px solid rgba(122,162,247,.28);
    background:#111423;
    color:#e6e6e6;
    cursor:pointer;
  }
  #menuModal {
    margin-top:12px;
    background:#161b22;
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
    padding:12px;
    display:none;
    flex-direction:column;
    gap:8px;
  }
  #menuModal button {
    padding:8px;
    border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1117;
    color:#e6e6e6;
    cursor:pointer;
  }

  /* Right AU select panel */
  #auPanel {
    width:280px;
    background:#161b22;
    border-left:1px solid rgba(122,162,247,.28);
    padding:16px;
    display:flex;
    flex-direction:column;
  }
  #auPanel h2 { margin:0 0 8px 0; font-size:18px; }
  .auBtn {
    padding:8px;
    border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1117;
    color:#e6e6e6;
    cursor:pointer;
    margin-bottom:6px;
  }
  #charContainer {
    margin-top:12px;
    flex:1;
    overflow-y:auto;
  }
  .char {
    display:flex;
    justify-content:space-between;
    margin:4px 0;
    padding:6px;
    border:1px solid rgba(122,162,247,.28);
    border-radius:6px;
    background:#0d1117;
  }
  #closeAU {
    margin-top:12px;
    padding:8px;
    border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#111423;
    color:#e6e6e6;
    cursor:pointer;
  }
</style>
</head>
<body>
  <!-- Left stats -->
  <div id="statsPanel">
    <h2>STATS</h2>
    <div class="stat" id="loveStat">LOVE: 0</div>
    <div class="stat" id="lpcStat">LPC: 1</div>
    <div class="stat" id="resetStat">Resets: 0</div>
  </div>

  <!-- Center main -->
  <div id="mainArea">
    <div id="soul"></div>
    <button id="menuBtn">Menu</button>
    <div id="menuModal">
      <button onclick="openMenu('upgrades')">Upgrades</button>
      <button onclick="openMenu('stats')">Stats</button>
      <button onclick="openMenu('reset')">Reset</button>
      <button onclick="openMenu('leaderboard')">Leaderboard</button>
      <button onclick="openMenu('settings')">Settings</button>
    </div>
  </div>

  <!-- Right AU select -->
  <div id="auPanel">
    <h2>AU Select</h2>
    <div id="auButtons">
      <button class="auBtn" onclick="showAU('Undertale')">Undertale</button>
      <button class="auBtn" onclick="showAU('Underswap')">Underswap</button>
    </div>
    <div id="charContainer"></div>
  </div>

<script>
  let love = 0;
  let lpc = 1;
  let resets = 0;

  const loveStat = document.getElementById('loveStat');
  const lpcStat = document.getElementById('lpcStat');
  const resetStat = document.getElementById('resetStat');
  const soul = document.getElementById('soul');
  const menuBtn = document.getElementById('menuBtn');
  const menuModal = document.getElementById('menuModal');
  const charContainer = document.getElementById('charContainer');
  const auButtons = document.getElementById('auButtons');

  const roster = {
    "Undertale": [
      { name:"Frisk", cost:50, lps:1, owned:0 },
      { name:"Sans", cost:200, lps:5, owned:0 }
    ],
    "Underswap": [
      { name:"Swap Sans", cost:100, lps:2, owned:0 },
      { name:"Swap Papyrus", cost:400, lps:8, owned:0 }
    ]
  };

  function updateStats(){
    loveStat.textContent = "LOVE: " + love;
    lpcStat.textContent = "LPC: " + lpc;
    resetStat.textContent = "Resets: " + resets;
  }

  soul.addEventListener('click', ()=>{
    love += lpc;
    updateStats();
  });

  menuBtn.addEventListener('click', ()=>{
    menuModal.style.display = menuModal.style.display==="flex" ? "none":"flex";
  });

  function openMenu(type){
    alert("Open menu: " + type);
  }

  function showAU(name){
    auButtons.style.display="none";
    charContainer.innerHTML = "<h3>"+name+" Characters</h3>";
    roster[name].forEach((c,i)=>{
      const div = document.createElement('div');
      div.className="char";
      div.innerHTML = `<span>${c.name} (x${c.owned})</span>
        <button onclick="buyChar('${name}',${i})">Buy (${c.cost} LOVE)</button>`;
      charContainer.appendChild(div);
    });
    const closeBtn = document.createElement('button');
    closeBtn.id="closeAU";
    closeBtn.textContent="X Close";
    closeBtn.onclick=()=>{
      charContainer.innerHTML="";
      auButtons.style.display="block";
    };
    charContainer.appendChild(closeBtn);
  }

  window.buyChar = function(au,index){
    const c = roster[au][index];
    if(love>=c.cost){
      love -= c.cost;
      c.owned++;
      c.cost = Math.floor(c.cost*1.5);
      updateStats();
      showAU(au);
    }
  }

  // Passive LOVE gain
  setInterval(()=>{
    let total = 0;
    Object.values(roster).forEach(list=>{
      list.forEach(c=> total += c.lps*c.owned);
    });
    love += total;
    updateStats();
  },1000);

  updateStats();
</script>
</body>
</html>
