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
    overflow:hidden;
    position:relative;
  }

  /* LEFT: STATS — percentage positioning */
  #statsPanel{
    position:absolute;
    top:50%; left:5%;
    transform:translateY(-50%);
    width:280px;
    display:flex;
    flex-direction:column;
    gap:12px;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
  }
  #statsPanel h2{margin:0 0 8px; font-size:18px; letter-spacing:.6px}
  .statRow{
    display:flex; justify-content:space-between; align-items:center;
    padding:10px 12px; border-radius:10px; background:#0c121c;
    border:1px solid rgba(122,162,247,.18);
  }
  .statLabel{opacity:.85}
  .statValue{font-weight:600; color:var(--accent)}

  /* CENTER: STAGE — perfectly centered SOUL; Menu overlays and does not move SOUL */
  #stage{
    position:absolute;
    top:50%; left:50%;
    transform:translate(-50%,-50%);
    display:flex;
    flex-direction:column;
    align-items:center;
    gap:12px;
  }

  /* SOUL */
  #soulWrap{
    width:220px; height:220px;
    position:relative;
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
  @keyframes pulse{
    0%{transform:scale(1)}
    50%{transform:scale(1.06)}
    100%{transform:scale(1)}
  }

  /* Shatter state (no second soul behind; we dim the same element) */
  #soulWrap.shattered #soul{
    animation:none;
    opacity:0.18;
    filter:grayscale(0.15) brightness(0.9);
  }
  .shards{
    position:absolute; inset:0; pointer-events:none;
  }
  .shard{
    position:absolute; width:14px; height:14px; border-radius:3px;
    background:linear-gradient(180deg,#ff6a85,#d3133f);
    opacity:0.95;
    animation:fly 1.2s ease-out forwards;
  }
  @keyframes fly{
    0%{transform:translate(0,0) rotate(0) scale(1); opacity:0.95}
    100%{transform:translate(var(--dx), var(--dy)) rotate(var(--rot)) scale(0.7); opacity:0}
  }

  /* Damage text (black fill with red outline) */
  .dmg{
    position:absolute; left:50%; top:50%;
    transform:translate(-50%,-50%);
    font-weight:800; color:#000;
    text-shadow:
      0 1px 0 #ff2f57, 0 -1px 0 #ff2f57,
      1px 0 0 #ff2f57, -1px 0 0 #ff2f57,
      1px 1px 0 #ff2f57, -1px -1px 0 #ff2f57,
      -1px 1px 0 #ff2f57, 1px -1px 0 #ff2f57;
    animation:dmgRise .8s ease-out forwards;
    pointer-events:none;
  }
  @keyframes dmgRise{
    0%{opacity:1; transform:translate(-50%,-50%) translateY(0) scale(1)}
    100%{opacity:0; transform:translate(-50%,-50%) translateY(-30px) scale(1.05)}
  }

  /* "But it refused." typewriter */
  .refused{
    position:absolute; left:50%; top:50%;
    transform:translate(-50%,-50%);
    background:rgba(0,0,0,.6);
    padding:8px 12px; border-radius:8px;
    border:1px solid rgba(122,162,247,.28);
    font-weight:600; color:#ff6a85;
    letter-spacing:.6px;
    box-shadow:var(--shadow);
  }

  /* Menu tray overlays SOUL (absolute, z-index above) */
  #menuTray{
    position:absolute;
    bottom:100%; /* sits above Menu button */
    left:50%;
    transform:translateX(-50%);
    display:none;
    flex-direction:column;
    gap:8px; padding:12px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
    width:220px; /* narrower */
    z-index:10; /* above SOUL */
  }
  .trayBtn{
    padding:8px 12px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.22);
    background:#0d1118; color:var(--text);
    transition:transform .08s ease, box-shadow .15s ease;
    text-align:left;
  }
  .trayBtn:hover{transform:translateY(-1px); box-shadow:0 10px 24px rgba(122,162,247,.22)}

  /* Menu button directly below and always centered; tray overlays without moving SOUL */
  #menuBtn{
    padding:10px 16px;
    border-radius:10px;
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:var(--text); cursor:pointer;
    box-shadow:var(--shadow);
    transition:transform .08s ease, box-shadow .2s ease;
    width:220px;
    z-index:9;
  }
  #menuBtn:hover{transform:translateY(-1px); box-shadow:0 14px 36px rgba(122,162,247,.28)}

  /* RESET overlay */
  #resetOverlay{
    position:absolute; inset:0; display:none;
    align-items:center; justify-content:center;
    background:rgba(0,0,0,.35);
    backdrop-filter: blur(2px);
  }
  #resetCard{
    width:420px; max-width:95vw;
    padding:16px; border-radius:12px;
    background:linear-gradient(180deg,#121826,#0e1322);
    border:1px solid rgba(122,162,247,.28);
    box-shadow:var(--shadow);
  }
  #resetCard h3{margin:0 0 10px}
  #resetCard .desc{opacity:.85; margin-bottom:12px}
  .row{display:flex; gap:8px; justify-content:flex-end;}
  .btn{
    padding:10px 14px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1118; color:var(--text);
  }
  .btn.danger{background:#2a0f18; border-color:#ff4d6d}
  .btn.danger:hover{background:#3a1320}

  /* RIGHT: AU SELECT — percentage positioning */
  #auPanel{
    position:absolute;
    top:50%; right:5%;
    transform:translateY(-50%);
    width:320px;
    display:flex; flex-direction:column;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
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
  .lock{font-size:12px; color:#ffcc66; opacity:.9; margin-left:6px;}
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
    <div class="statRow"><span class="statLabel">LV</span><span class="statValue" id="loveStat">0</span></div>
    <div class="statRow"><span class="statLabel">EXP</span><span class="statValue" id="expStat">0</span></div>
    <div class="statRow"><span class="statLabel">RESET</span><span class="statValue" id="resetStat">0</span></div>
    <div class="statRow"><span class="statLabel">NEXT</span><span class="statValue" id="needStat">10 EXP</span></div>
  </aside>

  <!-- CENTER: STAGE -->
  <main id="stage">
    <!-- Tray overlays above Menu and SOUL, does not move them -->
    <div id="menuTray">
      <button class="trayBtn" data-panel="settings">Settings</button>
      <button class="trayBtn" data-panel="leaderboard">Leaderboard</button>
      <button class="trayBtn" data-panel="reset">Reset</button>
      <button class="trayBtn" data-panel="stats">Stats</button>
      <button class="trayBtn" data-panel="upgrades">Upgrades</button>
    </div>

    <div id="soulWrap">
      <div id="soul" aria-label="SOUL"></div>
    </div>

    <button id="menuBtn">Menu</button>

    <!-- Reset confirm overlay -->
    <div id="resetOverlay">
      <div id="resetCard">
        <h3>Confirm Reset (Rebirth)</h3>
        <div class="desc">
          Requirements to reset:
          <ul style="margin:8px 0 12px 16px; padding:0">
            <li>LV ≥ 20</li>
            <li>EXP ≥ 2,500 (2,500 EXP will be consumed)</li>
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
  let love = 0;     // LV
  let exp = 0;      // EXP
  let resets = 0;

  // EXP needed logic: starts at 10 and increases by +10 per LV gained
  let expNeeded = 10;

  // Soul HP mechanics
  let soulHP = randInt(25,40);
  let soulDisabled = false; // disabled during shatter/refuse cycle

  // Reset requirements
  const RESET_EXP_COST = 2500;
  const RESET_LV_REQ = 20;

  // Passive bonus per reset (multiplier on EXP sources)
  function resetBonusMultiplier(){ return 1 + resets * 0.1; }

  // DOM refs
  const loveStat = document.getElementById('loveStat');
  const expStat = document.getElementById('expStat');
  const needStat = document.getElementById('needStat');
  const resetStat = document.getElementById('resetStat');
  const soul = document.getElementById('soul');
  const soulWrap = document.getElementById('soulWrap');
  const menuBtn = document.getElementById('menuBtn');
  const menuTray = document.getElementById('menuTray');
  const resetOverlay = document.getElementById('resetOverlay');
  const cancelReset = document.getElementById('cancelReset');
  const confirmReset = document.getElementById('confirmReset');
  const auContent = document.getElementById('auContent');
  const auSubtitle = document.getElementById('auSubtitle');

  // Roster per AU (LPS treated as EXP/s)
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
  };

  // UI helpers
  function updateStats(){
    loveStat.textContent = formatNum(love);
    expStat.textContent = formatNum(exp);
    needStat.textContent = formatNum(expNeeded) + " EXP";
    resetStat.textContent = resets;
  }

  function formatNum(n){
    if(n < 1000) return Math.floor(n);
    const units = ["K","M","B","T","Qa","Qi"];
    let u = -1;
    while(n >= 1000 && u < units.length-1){ n/=1000; u++; }
    return n.toFixed(2) + " " + units[u];
  }

  function randInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }

  // EXP conversion to LV — handles multiple level-ups if EXP exceeds need
  function tryConvertExpToLove(){
    let leveled = false;
    while(exp >= expNeeded){
      exp -= expNeeded;
      love += 1;
      expNeeded += 10;
      leveled = true;
    }
    if(leveled){
      gentlePop(statsPanelOnly()); // subtle pop on stats, not global
    }
  }
  function statsPanelOnly(){ return document.getElementById('statsPanel'); }

  // SOUL click -> damage only (no instant reward), floats damage text
  soul.addEventListener('click', ()=>{
    if(soulDisabled) return;
    const dmg = randInt(5,16);
    soulHP -= dmg;

    showDamage(dmg);

    // slight self-nudge on the soul only
    soulWrap.animate([
      {transform:'scale(1)'},
      {transform:'scale(0.985)'},
      {transform:'scale(1)'}
    ], {duration:120});

    if(soulHP <= 0){
      shatterSoul();
    }
    updateStats();
  });

  // Damage text
  function showDamage(n){
    const d = document.createElement('div');
    d.className = 'dmg';
    d.textContent = n;
    soulWrap.appendChild(d);
    setTimeout(()=> d.remove(), 900);
  }

  // Shatter the SOUL, grant random EXP (2–24), rare "But it refused."
  function shatterSoul(){
    soulDisabled = true;
    soulWrap.classList.add('shattered');

    // create shards (no second soul; just pieces)
    const shards = document.createElement('div');
    shards.className = 'shards';
    for(let i=0;i<16;i++){
      const piece = document.createElement('div');
      piece.className = 'shard';
      const dx = randInt(-110,110) + "px";
      const dy = randInt(-110,110) + "px";
      const rot = randInt(-180,180) + "deg";
      piece.style.setProperty('--dx', dx);
      piece.style.setProperty('--dy', dy);
      piece.style.setProperty('--rot', rot);
      piece.style.left = randInt(60,160) + "px";
      piece.style.top = randInt(60,160) + "px";
      shards.appendChild(piece);
    }
    soulWrap.appendChild(shards);

    // reward EXP
    let gained = randInt(2,24);
    gained = Math.floor(gained * resetBonusMultiplier());
    exp += gained;
    tryConvertExpToLove();
    updateStats();

    // 10% chance: "But it refused." — typewriter letters
    const refusedChance = Math.random() < 0.10;
    if(refusedChance){
      typeRefused("But it refused.", soulWrap, ()=> {
        // keep shattered briefly then restore
        setTimeout(respawnSoul, 800);
      });
    } else {
      // normal respawn sooner
      setTimeout(respawnSoul, 900);
    }

    function respawnSoul(){
      shards.remove();
      soulWrap.classList.remove('shattered');
      soulDisabled = false;
      soulHP = randInt(25,40);
    }
  }

  // Typewriter overlay (letter-by-letter)
  function typeRefused(text, container, onDone){
    const el = document.createElement('div');
    el.className = 'refused';
    el.textContent = "";
    container.appendChild(el);
    let i = 0;
    const tick = setInterval(()=>{
      el.textContent = text.slice(0, i+1);
      i++;
      if(i >= text.length){
        clearInterval(tick);
        setTimeout(()=>{
          el.remove();
          onDone && onDone();
        }, 700);
      }
    }, 50); // 0.05s per letter
  }

  // Menu toggling — tray overlays SOUL without moving it
  menuBtn.addEventListener('click', ()=>{
    const isOpen = menuTray.style.display === 'flex';
    menuTray.style.display = isOpen ? 'none' : 'flex';
  });
  menuTray.addEventListener('click', (e)=>{
    const btn = e.target.closest('.trayBtn');
    if(!btn) return;
    const panel = btn.getAttribute('data-panel');
    if(panel === 'reset'){
      openResetOverlay();
    } else {
      auSubtitle.textContent = panel.charAt(0).toUpperCase()+panel.slice(1)+" panel (WIP)";
      setTimeout(()=> auSubtitle.textContent = "Choose a timeline", 1500);
    }
  });

  // Reset overlay with requirements
  function openResetOverlay(){
    resetOverlay.style.display = 'flex';
  }
  cancelReset.addEventListener('click', ()=> resetOverlay.style.display = 'none');

  confirmReset.addEventListener('click', ()=>{
    if(love >= RESET_LV_REQ && exp >= RESET_EXP_COST){
      exp -= RESET_EXP_COST; // consume EXP
      resets += 1;

      // prestige effects
      // keep LV, keep NEXT scaling tied to LV progression
      // clear ownership
      Object.keys(roster).forEach(au=>{
        roster[au].forEach(char=>{ char.owned = 0; });
      });

      // feedback
      updateStats();
      renderAUList();
      resetOverlay.style.display = 'none';
      pulse(statsPanelOnly()); // pulse stats only
    } else {
      shake(resetOverlay); // shake overlay only
    }
  });

  // Visual feedback (scoped)
  function shake(el){
    el.animate([
      {transform:'translateX(0)'},
      {transform:'translateX(-6px)'},
      {transform:'translateX(6px)'},
      {transform:'translateX(0)'}
    ], {duration:260});
  }
  function gentlePop(el){
    el.animate([
      {transform:'scale(1)'},
      {transform:'scale(1.02)'},
      {transform:'scale(1)'}
    ], {duration:200});
  }
  function pulse(el){
    el.animate([
      {filter:'brightness(1)'},
      {filter:'brightness(1.15)'},
      {filter:'brightness(1)'}
    ], {duration:600});
  }

  // RIGHT PANEL BEHAVIOR
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
  }

  function openAU(name){
    auMode = 'characters';
    selectedAU = name;
    auSubtitle.textContent = name + " roster";
    auContent.innerHTML = "";

    const back = document.createElement('button');
    back.className = 'auBack';
    back.textContent = '← Back to AU select';
    back.addEventListener('click', renderAUList);
    auContent.appendChild(back);

    roster[name].forEach((c, i)=>{
      const row = document.createElement('div');
      row.className = 'charRow';
      const left = document.createElement('div');
      left.innerHTML = `<strong>${c.name}</strong> <span class="badge">EXP/s: ${c.lps}</span> <span class="badge">x${c.owned}</span>`;
      const buy = document.createElement('button');
      buy.className = 'buyBtn';
      buy.textContent = `Buy (${formatNum(c.cost)} EXP)`;
      buy.addEventListener('click', ()=> buyChar(name, i));
      row.appendChild(left);
      row.appendChild(buy);
      auContent.appendChild(row);
    });
  }

  function buyChar(au, index){
    const c = roster[au][index];
    if(exp >= c.cost){
      exp -= c.cost;
      c.owned++;
      c.cost = Math.floor(c.cost * 1.55);
      tryConvertExpToLove();
      updateStats();
      openAU(au);
      gentlePop(auContent); // affect panel only
    } else {
      shake(auContent); // affect panel only
    }
  }

  // Passive EXP per second across all owned characters (multiplied by resets)
  setInterval(()=>{
    let totalLps = 0;
    Object.values(roster).forEach(list=>{
      list.forEach(c=> totalLps += c.lps * c.owned);
    });
    totalLps = Math.floor(totalLps * resetBonusMultiplier());
    if(totalLps > 0){
      exp += totalLps;
      tryConvertExpToLove();
      updateStats();
    }
  }, 1000);

  // INIT
  updateStats();
  renderAUList();
</script>
</body>
</html>
