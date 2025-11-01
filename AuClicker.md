<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>AU Clicker</title>
<style>
  :root{
    --bg:#0d1117;
    --panel:#0f1420;
    --panel-2:#161b22;
    --text:#e6e6e6;
    --accent:#7aa2f7;
    --accent-2:#9cdcfe;
    --danger:#ff4d6d;
    --success:#59ffa0;
    --shadow:0 12px 30px rgba(122,162,247,.18);
  }
  *{box-sizing:border-box}
  body{
    margin:0;
    background:radial-gradient(1200px 600px at 50% 50%, #0f1420 20%, #0d1117 68%);
    color:var(--text);
    font-family:ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
    height:100vh;
    display:grid;
    grid-template-columns: 280px 1fr 320px;
    grid-template-rows: 100%;
  }

  /* LEFT: STATS — pinned fully left */
  #statsPanel{
    grid-column:1;
    display:flex;
    flex-direction:column;
    gap:12px;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border-right:1px solid rgba(122,162,247,.28);
  }
  #statsPanel h2{margin:0 0 8px; font-size:18px; letter-spacing:.6px}
  .statRow{
    display:flex; justify-content:space-between; align-items:center;
    padding:10px 12px; border-radius:10px; background:#0c121c;
    border:1px solid rgba(122,162,247,.18);
  }
  .statLabel{opacity:.8}
  .statValue{font-weight:600; color:var(--accent)}

  /* CENTER: SOUL + Menu — SOUL perfectly centered, controls below */
  #stage{
    grid-column:2;
    position:relative;
    display:flex;
    flex-direction:column;
    align-items:center;
  }
  /* Big SOUL centered absolutely with glow and pulse */
  #soulWrap{
    position:absolute;
    top:50%; left:100%;
    transform:translate(-50%,-50%);
    width:220px; height:220px;
    filter:drop-shadow(0 0 20px rgba(255,0,70,.45));
    transition:transform .12s ease;
  }
  #soul{
    width:100%; height:100%;
    background: radial-gradient(circle at 50% 35%, #ff6a85 0%, #ff2f57 40%, #d3133f 70%);
    clip-path: polygon(50% 18%, 63% 3%, 78% 3%, 100% 27%, 100% 55%, 50% 100%, 0 55%, 0 27%, 22% 3%, 37% 3%);
    cursor:pointer;
    animation:pulse 1.8s ease-in-out infinite;
    border:2px solid rgba(255,255,255,.06);
  }
  #soulWrap:active{transform:translate(-50%,-50%) scale(0.96)}
  @keyframes pulse{
    0%{transform:scale(1)}
    50%{transform:scale(1.06)}
    100%{transform:scale(1)}
  }
  /* Click spark */
  .spark{
    position:absolute; left:50%; top:50%;
    width:10px; height:10px; border-radius:50%;
    background:rgba(255,255,255,.85);
    transform:translate(-50%,-50%);
    animation:sparkUp .5s ease forwards;
  }
  @keyframes sparkUp{
    0%{opacity:1; transform:translate(-50%,-50%) scale(1)}
    100%{opacity:0; transform:translate(-50%,-110%) scale(0.2)}
  }

  /* Menu button and tray beneath SOUL */
  #menuBtn{
    position:relative;
    margin-top:260px; /* pushes controls below the centered SOUL */
    padding:10px 20px;
    border-radius:10px;
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:var(--text); cursor:pointer;
    box-shadow:var(--shadow);
    transition:transform .08s ease, box-shadow .2s ease;
  }
  #menuBtn:hover{transform:translateY(-1px); box-shadow:0 14px 36px rgba(122,162,247,.28)}
  #menuTray{
    margin-top:12px;
    display:none;
    gap:8px; padding:12px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
  }
  .trayBtn{
    padding:8px 12px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.22);
    background:#0d1118; color:var(--text);
    transition:transform .08s ease, box-shadow .15s ease;
  }
  .trayBtn:hover{transform:translateY(-1px); box-shadow:0 10px 24px rgba(122,162,247,.22)}

  /* RESET overlay (confirm) */
  #resetOverlay{
    position:absolute; inset:0; display:none;
    align-items:center; justify-content:center;
    background:rgba(0,0,0,.35);
    backdrop-filter: blur(2px);
  }
  #resetCard{
    width:380px; max-width:95vw;
    padding:16px; border-radius:12px;
    background:linear-gradient(180deg,#121826,#0e1322);
    border:1px solid rgba(122,162,247,.28);
    box-shadow:var(--shadow);
  }
  #resetCard h3{margin:0 0 10px}
  #resetCard .desc{opacity:.85; margin-bottom:12px}
  .row{
    display:flex; gap:8px; justify-content:flex-end;
  }
  .btn{
    padding:10px 14px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1118; color:var(--text);
  }
  .btn.primary{background:#12203a}
  .btn.danger{background:#2a0f18; border-color:#ff4d6d}
  .btn.danger:hover{background:#3a1320}

  /* RIGHT: AU SELECT — pinned fully right, self-contained */
  #auPanel{
    grid-column:3;
    display:flex; flex-direction:column;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border-left:1px solid rgba(122,162,247,.28);
  }
  #auPanel h2{margin:0 0 8px; font-size:18px; letter-spacing:.6px}
  #auHeader{
    display:flex; justify-content:space-between; align-items:center;
    margin-bottom:10px;
  }
  #auHeader .subtitle{font-size:13px; color:var(--accent-2); opacity:.85}
  #auContent{flex:1; display:flex; flex-direction:column; gap:8px}
  .auBtn, .auBack{
    padding:10px 12px; border-radius:10px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:var(--text);
    transition:transform .08s ease, box-shadow .15s ease;
  }
  .auBtn:hover, .auBack:hover{transform:translateY(-1px); box-shadow:0 10px 24px rgba(122,162,247,.22)}
  .lock{
    font-size:12px; color:#ffcc66; opacity:.9; margin-left:6px;
  }
  /* Characters list replaces AU buttons in same space */
  .charRow{
    display:flex; justify-content:space-between; align-items:center;
    padding:8px 10px; border-radius:10px;
    background:#0c121c; border:1px solid rgba(122,162,247,.18);
  }
  .buyBtn{
    padding:8px 10px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1118; color:var(--text);
  }
  .buyBtn:hover{transform:translateY(-1px); box-shadow:0 8px 18px rgba(122,162,247,.18)}
  .badge{
    font-size:12px; padding:2px 6px; border-radius:6px;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1118; margin-left:8px; color:var(--accent-2);
  }
</style>
</head>
<body>

  <!-- LEFT: STATS -->
  <aside id="statsPanel">
    <h2>STATS</h2>
    <div class="statRow"><span class="statLabel">LOVE</span><span class="statValue" id="loveStat">0</span></div>
    <div class="statRow"><span class="statLabel">LPC</span><span class="statValue" id="lpcStat">1</span></div>
    <div class="statRow"><span class="statLabel">Resets</span><span class="statValue" id="resetStat">0</span></div>
  </aside>

  <!-- CENTER: STAGE -->
  <main id="stage">
    <div id="soulWrap">
      <div id="soul" aria-label="SOUL"></div>
    </div>
    <button id="menuBtn">Menu</button>
    <div id="menuTray">
      <button class="trayBtn" data-panel="upgrades">Upgrades</button>
      <button class="trayBtn" data-panel="stats">Stats</button>
      <button class="trayBtn" data-panel="reset">Reset</button>
      <button class="trayBtn" data-panel="leaderboard">Leaderboard</button>
      <button class="trayBtn" data-panel="settings">Settings</button>
    </div>

    <!-- Reset confirm overlay -->
    <div id="resetOverlay">
      <div id="resetCard">
        <h3>Confirm Reset (Rebirth)</h3>
        <div class="desc" id="resetDesc">Spend LOVE to reset your timeline and unlock new AUs. Cost: <span id="resetCostText"></span> LOVE.</div>
        <div class="desc">Effects:
          <ul style="margin:8px 0 12px 16px; padding:0">
            <li>Resets increase global LOVE gain by a small bonus.</li>
            <li>Underswap unlocks on first reset.</li>
          </ul>
        </div>
        <div class="row">
          <button class="btn" id="cancelReset">Cancel</button>
          <button class="btn danger" id="confirmReset">Reset</button>
        </div>
      </div>
    </div>
  </main>

  <!-- RIGHT: AU SELECT -->
  <aside id="auPanel">
    <div id="auHeader">
      <h2>AU Select</h2>
      <div class="subtitle" id="auSubtitle">Choose a timeline</div>
    </div>
    <div id="auContent"><!-- populated by JS --></div>
  </aside>

<script>
  // Core state
  let love = 0;
  let lpc = 1;
  let resets = 0;

  // Passive gain bonus per reset (simple global multiplier)
  function resetBonusMultiplier(){ return 1 + resets * 0.1; }

  // Reset cost progression
  let resetCost = 5000;

  // DOM refs
  const loveStat = document.getElementById('loveStat');
  const lpcStat = document.getElementById('lpcStat');
  const resetStat = document.getElementById('resetStat');
  const soul = document.getElementById('soul');
  const soulWrap = document.getElementById('soulWrap');
  const menuBtn = document.getElementById('menuBtn');
  const menuTray = document.getElementById('menuTray');
  const resetOverlay = document.getElementById('resetOverlay');
  const resetCostText = document.getElementById('resetCostText');
  const cancelReset = document.getElementById('cancelReset');
  const confirmReset = document.getElementById('confirmReset');

  const auContent = document.getElementById('auContent');
  const auSubtitle = document.getElementById('auSubtitle');

  // Roster per AU
  const roster = {
    Undertale: [
      { name:"Frisk", cost:100, lps:2, owned:0 },
      { name:"Sans", cost:400, lps:8, owned:0 },
      { name:"Papyrus", cost:1200, lps:24, owned:0 }
    ],
    Underswap: [
      { name:"Swap Sans", cost:800, lps:18, owned:0 },
      { name:"Swap Papyrus", cost:1500, lps:36, owned:0 },
      { name:"Swap Toriel", cost:4000, lps:90, owned:0 }
    ]
    // Add more AUs here when needed
  };

  // UI helpers
  function updateStats(){
    loveStat.textContent = formatNum(love);
    lpcStat.textContent = formatNum(lpc);
    resetStat.textContent = resets;
  }

  function formatNum(n){
    if(n < 1000) return Math.floor(n);
    const units = ["K","M","B","T","Qa","Qi"];
    let u = -1;
    while(n >= 1000 && u < units.length-1){ n/=1000; u++; }
    return n.toFixed(2) + " " + units[u];
  }

  function spawnSpark(){
    const s = document.createElement('div');
    s.className = 'spark';
    soulWrap.appendChild(s);
    setTimeout(()=> s.remove(), 500);
  }

  // Click SOUL -> gain LOVE per click with resets bonus
  soul.addEventListener('click', ()=>{
    const gain = lpc * resetBonusMultiplier();
    love += gain;
    updateStats();
    spawnSpark();
  });

  // Menu toggling
  menuBtn.addEventListener('click', ()=>{
    const visible = menuTray.style.display === 'flex';
    menuTray.style.display = visible ? 'none' : 'flex';
  });
  menuTray.addEventListener('click', (e)=>{
    const btn = e.target.closest('.trayBtn');
    if(!btn) return;
    const panel = btn.getAttribute('data-panel');
    if(panel === 'reset'){
      openResetOverlay();
    } else {
      // Placeholder feedback; wire additional panels as needed
      auSubtitle.textContent = panel.charAt(0).toUpperCase()+panel.slice(1)+" panel (WIP)";
      setTimeout(()=> auSubtitle.textContent = "Choose a timeline", 1500);
    }
  });

  // Reset overlay
  function openResetOverlay(){
    resetCostText.textContent = formatNum(resetCost);
    resetOverlay.style.display = 'flex';
  }
  cancelReset.addEventListener('click', ()=> resetOverlay.style.display = 'none');
  confirmReset.addEventListener('click', ()=>{
    if(love >= resetCost){
      // spend and apply reset
      love -= resetCost;
      resets += 1;
      // increase cost
      resetCost = Math.floor(resetCost * 2.4);
      // soft prestige effects
      lpc = 1; // base per click resets
      // clear ownership back to base (keep base costs as currently scaled for next run)
      Object.keys(roster).forEach(au=>{
        roster[au].forEach(char=>{
          char.owned = 0;
        });
      });
      updateStats();
      // refresh panel states and feedback
      renderAUList();
      resetOverlay.style.display = 'none';
      pulseStage();
    } else {
      shake(resetOverlay);
    }
  });

  // Visual feedback helpers
  function shake(el){
    el.animate([
      {transform:'translateX(0)'},
      {transform:'translateX(-6px)'},
      {transform:'translateX(6px)'},
      {transform:'translateX(0)'}
    ], {duration:260});
  }
  function pulseStage(){
    document.body.animate([
      {filter:'brightness(1)'},
      {filter:'brightness(1.18)'},
      {filter:'brightness(1)'}
    ], {duration:700});
  }

  // RIGHT PANEL BEHAVIOR
  // Modes: 'list' (AU buttons) or 'characters' for selectedAU
  let auMode = 'list';
  let selectedAU = null;

  function renderAUList(){
    auMode = 'list';
    selectedAU = null;
    auSubtitle.textContent = "Choose a timeline";
    auContent.innerHTML = "";

    // Undertale is always available
    const btnUT = document.createElement('button');
    btnUT.className = 'auBtn';
    btnUT.textContent = 'Undertale';
    btnUT.addEventListener('click', ()=> openAU('Undertale'));
    auContent.appendChild(btnUT);

    // Underswap locks until first reset
    const btnUS = document.createElement('button');
    btnUS.className = 'auBtn';
    if(resets >= 1){
      btnUS.textContent = 'Underswap';
      btnUS.addEventListener('click', ()=> openAU('Underswap'));
    } else {
      btnUS.innerHTML = 'Underswap <span class="lock">Locked (Requires 1 reset)</span>';
      btnUS.disabled = true;
      btnUS.style.opacity = .7;
      btnUS.style.cursor = 'not-allowed';
    }
    auContent.appendChild(btnUS);

    // Add more AU entries here, gating by resets if needed
  }

  function openAU(name){
    auMode = 'characters';
    selectedAU = name;
    auSubtitle.textContent = name + " roster";
    auContent.innerHTML = "";

    // Back button replacing AU buttons space
    const back = document.createElement('button');
    back.className = 'auBack';
    back.textContent = '← Back to AU select';
    back.addEventListener('click', renderAUList);
    auContent.appendChild(back);

    // Characters list in same panel space
    roster[name].forEach((c, i)=>{
      const row = document.createElement('div');
      row.className = 'charRow';
      const left = document.createElement('div');
      left.innerHTML = `<strong>${c.name}</strong> <span class="badge">LPS: ${c.lps}</span> <span class="badge">x${c.owned}</span>`;
      const buy = document.createElement('button');
      buy.className = 'buyBtn';
      buy.textContent = `Buy (${formatNum(c.cost)} LOVE)`;
      buy.addEventListener('click', ()=> buyChar(name, i));
      row.appendChild(left);
      row.appendChild(buy);
      auContent.appendChild(row);
    });
  }

  function buyChar(au, index){
    const c = roster[au][index];
    if(love >= c.cost){
      love -= c.cost;
      c.owned++;
      // scale cost
      c.cost = Math.floor(c.cost * 1.55);
      updateStats();
      // refresh the same AU view
      openAU(au);
      gentlePop(auContent);
    } else {
      shake(auContent);
    }
  }

  function gentlePop(el){
    el.animate([
      {transform:'scale(1)'},
      {transform:'scale(1.015)'},
      {transform:'scale(1)'}
    ], {duration:180});
  }

  // Passive LOVE per second across all owned characters
  setInterval(()=>{
    let totalLps = 0;
    Object.values(roster).forEach(list=>{
      list.forEach(c=> totalLps += c.lps * c.owned);
    });
    totalLps *= resetBonusMultiplier();
    love += totalLps;
    updateStats();
  }, 1000);

  // INIT
  updateStats();
  renderAUList();
</script>
</body>
</html>
