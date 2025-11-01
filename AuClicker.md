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
    flex-direction:row;
    height:100vh;
  }
  /* Left stats panel */
  #statsPanel {
    width:200px;
    background:#161b22;
    border-right:1px solid rgba(122,162,247,.28);
    padding:12px;
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
    width:120px;
    height:120px;
    background:red;
    clip-path: polygon(50% 15%, 61% 0, 75% 0, 100% 25%, 100% 50%, 50% 100%, 0 50%, 0 25%, 25% 0, 39% 0);
    cursor:pointer;
    transition:transform .1s ease;
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
  /* Menu modal */
  #menuModal {
    position:absolute;
    bottom:20px;
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
    width:220px;
    background:#161b22;
    border-left:1px solid rgba(122,162,247,.28);
    padding:12px;
    display:flex;
    flex-direction:column;
    gap:8px;
  }
  #auPanel h2 { margin:0 0 8px 0; font-size:18px; }
  .auBtn {
    padding:8px;
    border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1117;
    color:#e6e6e6;
    cursor:pointer;
  }
  /* Character list modal */
  #charModal {
    position:absolute;
    top:50%;
    right:240px;
    transform:translateY(-50%);
    background:#161b22;
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
    padding:12px;
    display:none;
    flex-direction:column;
    width:250px;
  }
  #charModal h3 { margin:0 0 8px 0; }
  .char {
    display:flex;
    justify-content:space-between;
    margin:4px 0;
  }
  .closeBtn {
    margin-top:10px;
    padding:6px;
    border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1117;
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
    <button class="auBtn" onclick="openAU('Undertale')">Undertale</button>
    <button class="auBtn" onclick="openAU('Underswap')">Underswap</button>
  </div>

  <!-- Character modal -->
  <div id="charModal">
    <h3 id="charTitle">Characters</h3>
    <div id="charList"></div>
    <button class="closeBtn" onclick="closeAU()">X Close</button>
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
  const charModal = document.getElementById('charModal');
  const charTitle = document.getElementById('charTitle');
  const charList = document.getElementById('charList');

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

  function openAU(name){
    charTitle.textContent = name + " Characters";
    charList.innerHTML = "";
    roster[name].forEach((c,i)=>{
      const div = document.createElement('div');
      div.className="char";
      div.innerHTML = `<span>${c.name} (x${c.owned})</span>
        <button onclick="buyChar('${name}',${i})">Buy (${c.cost} LOVE)</button>`;
      charList.appendChild(div);
    });
    charModal.style.display="flex";
  }

  function closeAU(){
    charModal.style.display="none";
  }

  window.buyChar = function(au,index){
    const c = roster[au][index];
    if(love>=c.cost){
      love -= c.cost;
      c.owned++;
      c.cost = Math.floor(c.cost*1.5);
      updateStats();
      openAU(au);
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
