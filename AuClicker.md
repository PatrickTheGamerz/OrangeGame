<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>AU Clicker</title>
<style>
  :root{
    --bg:#000;
    --panel:#0f1420;
    --panel-2:#161b22;
    --text:#e6e6e6;
    --accent:#7aa2f7;
    --danger:#ff4d6d;
    --shadow:0 12px 30px rgba(122,162,247,.18);
  }
  body{
    margin:0;background:var(--bg);color:var(--text);
    font-family:ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,Arial;
    height:100vh;overflow:hidden;
  }

  /* LEFT: STATS (fixed, isolated from soul transforms) */
  #statsPanel{
    position:absolute;top:50%;left:5%;transform:translateY(-50%);
    width:280px;display:flex;flex-direction:column;gap:12px;padding:16px;
    background:linear-gradient(180deg,var(--panel),var(--panel-2));
    border:1px solid rgba(122,162,247,.28);border-radius:12px;
    contain:layout paint;will-change:opacity,transform;
  }
  .statRow{
    display:flex;justify-content:space-between;align-items:center;
    padding:10px 12px;border-radius:10px;background:#0c121c;border:1px solid rgba(122,162,247,.18);
  }
  .statLabel{opacity:.85}
  .statValue{font-weight:600;color:var(--accent)}

  /* RIGHT: AU SELECT */
  #auPanel{
    position:absolute;top:50%;right:5%;transform:translateY(-50%);
    width:320px;display:flex;flex-direction:column;padding:16px;
    background:linear-gradient(180deg,var(--panel),var(--panel-2));
    border:1px solid rgba(122,162,247,.28);border-radius:12px;max-height:80vh;
  }
  #auHeader{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px;}
  #auHeader .subtitle{font-size:13px;color:#9cdcfe;opacity:.85}
  #auContent{flex:1;display:flex;flex-direction:column;gap:8px;overflow-y:auto;}
  .auBtn,.auBack{
    padding:10px 12px;border-radius:10px;cursor:pointer;
    border:1px solid rgba(122,162,247,.28);background:linear-gradient(180deg,#111423,#0d1120);color:var(--text);
  }
  .charRow{
    display:flex;justify-content:space-between;align-items:center;
    padding:10px;border-radius:10px;background:#0c121c;border:1px solid rgba(122,162,247,.18);
  }
  .charName{font-weight:700;letter-spacing:.3px}
  .buyBtn{
    padding:8px 12px;border-radius:8px;cursor:pointer;border:1px solid rgba(122,162,247,.28);
    background:#0d1118;color:var(--text);
  }
  .buyBtn:disabled{opacity:.6;cursor:not-allowed}

  /* CENTER: STAGE */
  #stage{
    position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);
    display:flex;flex-direction:column;align-items:center;gap:12px;
  }
  #soulWrap{
    width:220px;height:220px;position:relative;
    filter:drop-shadow(0 0 20px rgba(255,0,70,.45));
    isolation:isolate; /* clean stacking context for z-index control */
  }
  #soul{
    width:100%;height:100%;background:#ff2f57;
    clip-path:polygon(50% 15%,61% 0,75% 0,100% 25%,100% 55%,50% 100%,0 55%,0 25%,25% 0,39% 0);
    cursor:pointer;animation:pulse 1.8s ease-in-out infinite;transition:filter .18s ease;
    will-change:transform,filter;
  }
  #soul.dim{filter:brightness(0.55);}
  @keyframes pulse{0%{transform:scale(1)}50%{transform:scale(1.06)}100%{transform:scale(1)}}
  #soulWrap.shattered #soul{opacity:0;animation:none;}

  /* Heart halves */
  .half{
    position:absolute;top:0;width:50%;height:100%;background:#ff2f57;pointer-events:none;
  }
  .half.left{
    left:0;
    clip-path:polygon(
      50% 15%, 39% 0, 25% 0, 0 25%, 0 55%,
      20% 68%, 35% 82%, 50% 100%,
      50% 70%, 50% 15%
    );
  }
  .half.right{
    right:0;
    clip-path:polygon(
      50% 15%, 61% 0, 75% 0, 100% 25%, 100% 55%,
      80% 68%, 65% 82%, 50% 100%,
      50% 70%, 50% 15%
    );
  }
  .halfAnim.left{animation:halfLeft .6s cubic-bezier(.22,.61,.36,1) forwards;}
  .halfAnim.right{animation:halfRight .6s cubic-bezier(.22,.61,.36,1) forwards;}
  @keyframes halfLeft{to{transform:translateX(-52px) rotate(-18deg);opacity:0}}
  @keyframes halfRight{to{transform:translateX(52px) rotate(18deg);opacity:0}}

  /* Shards */
  .shards{position:absolute;inset:0;pointer-events:none;}
  .shard{
    position:absolute;width:12px;height:12px;background:#ff2f57;border-radius:2px;
    box-shadow:0 0 10px rgba(255,47,87,.55);
    animation:fly 900ms cubic-bezier(.22,.61,.36,1) forwards;
  }
  @keyframes fly{
    0%{opacity:1;transform:translate(0,0) scale(1)}
    100%{opacity:0;transform:translate(var(--dx),var(--dy)) rotate(var(--rot)) scale(0.8)}
  }

  /* Damage text */
  .dmg{
    position:absolute;left:50%;top:0%;transform:translate(-50%,-120%);
    font-size:42px;font-weight:900;color:#000;
    text-shadow:2px 2px 0 #ff0000,-2px -2px 0 #ff0000,2px -2px 0 #ff0000,-2px 2px 0 #ff0000;
    animation:dmgRise .8s ease-out forwards;pointer-events:none;z-index:11;
  }
  @keyframes dmgRise{
    0%{opacity:1;transform:translate(-50%,-120%) translateY(0)}
    100%{opacity:0;transform:translate(-50%,-120%) translateY(-40px)}
  }

  /* Refused text */
  .refused{
    position:absolute;left:50%;top:0%;transform:translate(-50%,-150%);
    font-size:32px;font-weight:800;color:#fff;z-index:11;white-space:nowrap;
    text-shadow:0 0 12px rgba(255,255,255,.35);
  }

  /* Undertale-style slash (SVG path drawn, behind menu tray) */
  .slashSVG{
    position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);
    width:200px;height:240px;pointer-events:none;z-index:5; /* layer 1 */
  }
  .slashPath{
    fill:none;stroke:url(#grad);stroke-width:14;stroke-linecap:round;stroke-linejoin:round;
    filter:drop-shadow(0 0 14px rgba(255,47,87,.85)) drop-shadow(0 0 24px rgba(255,255,255,.45));
    stroke-dasharray:620;stroke-dashoffset:620;
    animation:drawSlash .26s cubic-bezier(.22,.61,.36,1) forwards,
              lingerSlash .12s ease-out .26s forwards,
              fadeSlash .22s ease-out .38s forwards;
  }
  @keyframes drawSlash{to{stroke-dashoffset:0}}
  @keyframes lingerSlash{to{opacity:0.95}}
  @keyframes fadeSlash{to{opacity:0}}

  .impactFlash{
    position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);
    width:100px;height:100px;border-radius:50%;
    background:radial-gradient(circle,rgba(255,255,255,0.95),rgba(255,47,87,0.25));
    box-shadow:0 0 24px rgba(255,255,255,.7),0 0 36px rgba(255,47,87,.6);
    z-index:6;pointer-events:none;animation:flashPop .28s ease-out forwards;
  }
  @keyframes flashPop{
    0%{opacity:1;transform:translate(-50%,-50%) scale(0.7)}
    100%{opacity:0;transform:translate(-50%,-50%) scale(1.25)}
  }

  /* Menu overlays (above slash) */
  #menuTray{
    position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);
    display:none;flex-direction:column;gap:8px;padding:12px;
    background:linear-gradient(180deg,var(--panel),var(--panel-2));border:1px solid rgba(122,162,247,.28);
    border-radius:12px;width:220px;z-index:10; /* layer 2 */
  }
  .trayBtn{padding:8px 12px;border-radius:8px;cursor:pointer;border:1px solid rgba(122,162,247,.22);background:#0d1118;color:var(--text);text-align:left;}
  #menuBtn{
    padding:10px 16px;border-radius:10px;border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);color:var(--text);cursor:pointer;width:220px;z-index:9;
  }

  /* Reset overlay */
  #resetOverlay{position:absolute;inset:0;display:none;align-items:center;justify-content:center;background:rgba(0,0,0,.55);z-index:20;}
  #resetCard{
    width:420px;max-width:95vw;padding:16px;border-radius:12px;
    background:linear-gradient(180deg,#121826,#0e1322);border:1px solid rgba(122,162,247,.28);box-shadow:var(--shadow);
  }
  #resetCard h3{margin:0 0 10px}
  #resetCard .desc{opacity:.9;margin-bottom:12px}
  .row{display:flex;gap:8px;justify-content:flex-end;}
  .btn{padding:10px 14px;border-radius:8px;cursor:pointer;border:1px solid rgba(122,162,247,.28);background:#0d1118;color:var(--text);}
  .btn.danger{background:#2a0f18;border-color:#ff4d6d}
  .lock{margin-left:6px;color:#9aa0a6;font-size:12px}
</style>
</head>
<body>
  <!-- LEFT: STATS -->
  <aside id="statsPanel">
    <h2>STATS</h2>
    <div class="statRow"><span class="statLabel">LV</span><span class="statValue" id="loveStat">0</span></div>
    <div class="statRow"><span class="statLabel">EXP</span><span class="statValue" id="expStat">0</span></div>
    <div class="statRow"><span class="statLabel">GOLD</span><span class="statValue" id="goldStat">0</span></div>
    <div class="statRow"><span class="statLabel">RESET</span><span class="statValue" id="resetStat">0</span></div>
    <div class="statRow"><span class="statLabel">NEXT</span><span class="statValue" id="needStat">10 EXP</span></div>
  </aside>

  <!-- CENTER: STAGE -->
  <main id="stage">
    <div id="soulWrap">
      <div id="soul" aria-label="SOUL"></div>
      <div id="menuTray">
        <button class="trayBtn" data-panel="settings">Settings</button>
        <button class="trayBtn" data-panel="leaderboard">Leaderboard</button>
        <button class="trayBtn" data-panel="reset">Reset</button>
        <button class="trayBtn" data-panel="stats">Stats</button>
        <button class="trayBtn" data-panel="upgrades">Upgrades</button>
      </div>
    </div>
    <button id="menuBtn">Menu</button>

    <!-- Reset overlay -->
    <div id="resetOverlay">
      <div id="resetCard">
        <h3>RESET</h3>
        <div class="desc">
          Requirements:
          <ul style="margin:8px 0 12px 16px;padding:0">
            <li>LV: 20</li>
            <li>EXP: 2,500</li>
          </ul>
          <div>All your progress will be reset</div>
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
    <div id="auContent"></div>
  </aside>

<script>
  /* ===== Core state ===== */
  let love = 0;     // LV
  let exp = 0;      // EXP
  let gold = 0;     // GOLD
  let resets = 0;
  let expNeeded = 10;

  // Soul HP mechanics
  let soulHP = randInt(25,40);
  let soulDisabled = false;

  // Reset requirements
  const RESET_EXP_REQ = 2500;
  const RESET_LV_REQ = 20;

  function resetBonusMultiplier(){ return 1 + resets * 0.1; }

  // DOM refs
  const loveStat = document.getElementById('loveStat');
  const expStat  = document.getElementById('expStat');
  const goldStat = document.getElementById('goldStat');
  const needStat = document.getElementById('needStat');
  const resetStat= document.getElementById('resetStat');

  const soul     = document.getElementById('soul');
  const soulWrap = document.getElementById('soulWrap');
  const menuBtn  = document.getElementById('menuBtn');
  const menuTray = document.getElementById('menuTray');

  const resetOverlay = document.getElementById('resetOverlay');
  const cancelReset  = document.getElementById('cancelReset');
  const confirmReset = document.getElementById('confirmReset');

  const auContent  = document.getElementById('auContent');
  const auSubtitle = document.getElementById('auSubtitle');

  /* ===== AU roster ===== */
  const roster = {
    Undertale: [
      { name:"FRISK: 50 G",   label:"FRISK: 50 G",   costGold:50,   dps:0,  owned:0, type:'frisk' },
      { name:"TORIEL: 120 G", label:"TORIEL: 120 G", costGold:120,  dps:5,  owned:0, type:'dps' },
      { name:"PAPYRUS: 220 G",label:"PAPYRUS: 220 G",costGold:220,  dps:7,  owned:0, type:'dps' },
      { name:"UNDYNE: 400 G", label:"UNDYNE: 400 G", costGold:400,  dps:10, owned:0, type:'dps' },
      { name:"METTATON: 650 G",label:"METTATON: 650 G",costGold:650,dps:14, owned:0, type:'dps' },
      { name:"SANS: 1200 G",  label:"SANS: 1200 G",  costGold:1200, dps:18, owned:0, type:'dps' },
      { name:"ASGORE: 1800 G",label:"ASGORE: 1800 G",costGold:1800, dps:24, owned:0, type:'dps' }
    ],
    Underswap: [
      { name:"Swap Sans",     label:"Swap Sans",     costGold:900,  dps:20, owned:0, type:'dps' },
      { name:"Swap Papyrus",  label:"Swap Papyrus",  costGold:1300, dps:26, owned:0, type:'dps' },
      { name:"Swap Toriel",   label:"Swap Toriel",   costGold:2200, dps:34, owned:0, type:'dps' }
    ]
  };

  /* ===== Persistence ===== */
  const SAVE_KEY = 'au_clicker_save_v7';
  function save(){
    const data = { love, exp, gold, resets, expNeeded, soulHP, roster };
    localStorage.setItem(SAVE_KEY, JSON.stringify(data));
  }
  function load(){
    const raw = localStorage.getItem(SAVE_KEY);
    if(!raw) return;
    try{
      const data = JSON.parse(raw);
      love = data.love ?? love;
      exp = data.exp ?? exp;
      gold = data.gold ?? gold;
      resets = data.resets ?? resets;
      expNeeded = data.expNeeded ?? expNeeded;
      soulHP = data.soulHP ?? soulHP;
      if(data.roster){
        Object.keys(roster).forEach(au=>{
          if(data.roster[au]){
            roster[au].forEach((c,i)=>{
              const s = data.roster[au][i];
              if(s && s.name === c.name){
                c.costGold = s.costGold ?? c.costGold;
                c.dps      = s.dps ?? c.dps;
                c.owned    = s.owned ?? c.owned;
                c.type     = s.type ?? c.type;
              }
            });
          }
        });
      }
    }catch(e){ console.warn('Load failed', e); }
  }

  /* ===== UI helpers ===== */
  function updateStats(){
    loveStat.textContent = formatNum(love);
    expStat.textContent  = formatNum(exp);
    goldStat.textContent = formatNum(gold);
    resetStat.textContent= resets;
    needStat.textContent = formatNum(expNeeded) + " EXP";
    save();
  }
  function formatNum(n){
    if(n < 1000) return Math.floor(n);
    const units = ["K","M","B","T","Qa","Qi"]; let u=-1;
    while(n>=1000 && u<units.length-1){ n/=1000; u++; }
    return n.toFixed(2)+" "+units[u];
  }
  function randInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }

  function tryConvertExpToLove(){
    let leveled=false;
    while(exp>=expNeeded){
      exp-=expNeeded; love+=1; expNeeded+=10; leveled=true;
    }
    if(leveled){ gentlePop(document.getElementById('statsPanel')); }
  }

  /* ===== SOUL interactions ===== */
  soul.addEventListener('click', ()=>{
    if(soulDisabled) return;
    const dmg = randInt(5,16);
    applyDamage(dmg, true);
  });

  function applyDamage(dmg, dim=true){
    soulHP -= dmg;
    showDamage(dmg);
    if(dim){
      soul.classList.add('dim');
      setTimeout(()=> soul.classList.remove('dim'),150);
    }
    /* Jitter fix: animate only the soul (not the wrapper/panels) */
    soul.animate(
      [{transform:'scale(1)'},{transform:'scale(0.985)'},{transform:'scale(1)'}],
      {duration:120, easing:'ease-out'}
    );
    if(soulHP <= 0){ shatterSoul(); }
    updateStats();
  }

  function showDamage(n){
    const d=document.createElement('div'); d.className='dmg'; d.textContent=n;
    soulWrap.appendChild(d); setTimeout(()=> d.remove(),900);
  }

  /* ===== FRISK slash emitter (drawn SVG path, behind menu) ===== */
  let friskSlashTimer = null;
  function startFriskSlashLoop(){
    stopFriskSlashLoop();
    const schedule = ()=>{
      const delay = randInt(8000,12000);
      friskSlashTimer = setTimeout(()=>{
        if(!soulDisabled){
          showFriskSlashSVG();
          const slashDmg = randInt(12,15);
          applyDamage(slashDmg, true);
        }
        schedule();
      }, delay);
    };
    schedule();
  }
  function stopFriskSlashLoop(){
    if(friskSlashTimer){ clearTimeout(friskSlashTimer); friskSlashTimer=null; }
  }

  function showFriskSlashSVG(){
    const svgNS = "http://www.w3.org/2000/svg";
    const svg = document.createElementNS(svgNS, "svg");
    svg.setAttribute("viewBox", "0 0 200 240");
    svg.classList.add("slashSVG");

    const defs = document.createElementNS(svgNS, "defs");
    const grad = document.createElementNS(svgNS, "linearGradient");
    grad.setAttribute("id", "grad");
    grad.setAttribute("x1", "0"); grad.setAttribute("y1", "0");
    grad.setAttribute("x2", "0"); grad.setAttribute("y2", "1");
    const stop1 = document.createElementNS(svgNS, "stop");
    stop1.setAttribute("offset", "0%"); stop1.setAttribute("stop-color", "#ffffff"); stop1.setAttribute("stop-opacity", "1");
    const stop2 = document.createElementNS(svgNS, "stop");
    stop2.setAttribute("offset", "55%"); stop2.setAttribute("stop-color", "#ff2f57"); stop2.setAttribute("stop-opacity", "0.95");
    const stop3 = document.createElementNS(svgNS, "stop");
    stop3.setAttribute("offset", "100%"); stop3.setAttribute("stop-color", "#ff2f57"); stop3.setAttribute("stop-opacity", "0.8");
    grad.appendChild(stop1); grad.appendChild(stop2); grad.appendChild(stop3);
    defs.appendChild(grad);
    svg.appendChild(defs);

    /* Curved tapered path approximating Undertale slash silhouette */
    const path = document.createElementNS(svgNS, "path");
    path.setAttribute("class", "slashPath");
    path.setAttribute("d",
      "M100,6 " +
      "C96,20 92,34 94,50 " +
      "C98,70 108,92 118,112 " +
      "C126,128 132,146 130,162 " +
      "C128,178 120,196 110,214"
    );

    /* Impact flash slightly after draw start */
    const flash = document.createElement("div");
    flash.className = "impactFlash";

    svg.appendChild(path);
    soulWrap.appendChild(svg);
    soulWrap.appendChild(flash);

    setTimeout(()=> svg.remove(), 700);
    setTimeout(()=> flash.remove(), 320);
  }

  /* ===== Shatter sequence ===== */
  function shatterSoul(){
    soulDisabled = true;
    soulWrap.classList.add('shattered');

    const leftHalf = document.createElement('div'); leftHalf.className='half left';
    const rightHalf= document.createElement('div'); rightHalf.className='half right';
    soulWrap.appendChild(leftHalf); soulWrap.appendChild(rightHalf);

    let gainedExp = Math.floor(randInt(2,24) * resetBonusMultiplier());
    let gainedGold= randInt(4,9);

    const refused = Math.random() < 0.10;
    if(refused){ gainedExp*=2; gainedGold*=2; }

    exp += gainedExp; gold += gainedGold; tryConvertExpToLove(); updateStats();

    if(refused){
      typeRefusedInline("But it refused.", soulWrap, ()=>{
        setTimeout(()=>{
          leftHalf.remove(); rightHalf.remove();
          soulWrap.classList.remove('shattered'); soulDisabled=false; soulHP=randInt(25,40);
        }, 2000);
      });
    } else {
      setTimeout(()=>{
        leftHalf.classList.add('halfAnim','left');
        rightHalf.classList.add('halfAnim','right');

        setTimeout(()=>{
          const shards=document.createElement('div'); shards.className='shards';
          for(let i=0;i<18;i++){
            const p=document.createElement('div'); p.className='shard';
            p.style.setProperty('--dx', randInt(-110,110)+'px');
            p.style.setProperty('--dy', randInt(-110,110)+'px');
            p.style.setProperty('--rot', randInt(-180,180)+'deg');
            p.style.left = randInt(60,160)+'px'; p.style.top = randInt(60,160)+'px';
            shards.appendChild(p);
          }
          soulWrap.appendChild(shards);

          setTimeout(()=>{
            shards.remove(); leftHalf.remove(); rightHalf.remove();
            soulWrap.classList.remove('shattered'); soulDisabled=false; soulHP=randInt(25,40);
          }, 900);
        }, 200);
      }, 1000);
    }
  }

  function typeRefusedInline(text, container, onDone){
    const el=document.createElement('div'); el.className='refused'; el.textContent="";
    container.appendChild(el); let i=0;
    const tick=setInterval(()=>{
      el.textContent=text.slice(0, i+1); i++;
      if(i>=text.length){ clearInterval(tick); setTimeout(()=>{ el.remove(); onDone && onDone(); }, 400); }
    }, 50);
  }

  /* ===== Menu ===== */
  menuBtn.addEventListener('click', ()=>{
    const open = menuTray.style.display==='flex';
    menuTray.style.display = open ? 'none':'flex';
  });
  menuTray.addEventListener('click',(e)=>{
    const btn=e.target.closest('.trayBtn'); if(!btn) return;
    const panel=btn.getAttribute('data-panel');
    if(panel==='reset'){ openResetOverlay(); }
    else {
      auSubtitle.textContent=panel.charAt(0).toUpperCase()+panel.slice(1)+" panel (WIP)";
      setTimeout(()=> auSubtitle.textContent="Choose a timeline",1500);
    }
  });

  /* ===== Reset overlay ===== */
  function openResetOverlay(){ resetOverlay.style.display='flex'; }
  cancelReset.addEventListener('click',()=> resetOverlay.style.display='none');
  confirmReset.addEventListener('click',()=>{
    if(love>=RESET_LV_REQ && exp>=RESET_EXP_REQ){
      exp -= RESET_EXP_REQ;
      love=0; exp=0; gold=0; expNeeded=10; resets+=1;
      Object.keys(roster).forEach(au=> roster[au].forEach(c=> c.owned=0 ));
      stopFriskSlashLoop();
      updateStats(); renderAUList(); resetOverlay.style.display='none';
      pulse(document.getElementById('statsPanel'));
    } else { shake(resetOverlay); }
  });

  /* ===== Feedback ===== */
  function shake(el){
    el.animate(
      [{transform:'translateX(0)'},{transform:'translateX(-6px)'},{transform:'translateX(6px)'},{transform:'translateX(0)'}],
      {duration:260}
    );
  }
  function gentlePop(el){
    el.animate(
      [{transform:'scale(1)'},{transform:'scale(1.02)'},{transform:'scale(1)'}],
      {duration:200}
    );
  }
  function pulse(el){
    el.animate(
      [{filter:'brightness(1)'},{filter:'brightness(1.15)'},{filter:'brightness(1)'}],
      {duration:600}
    );
  }

  /* ===== AU panel behavior ===== */
  let auMode='list'; let selectedAU=null;

  function renderAUList(){
    auMode='list'; selectedAU=null; auSubtitle.textContent="Choose a timeline"; auContent.innerHTML="";
    const btnUT=document.createElement('button'); btnUT.className='auBtn'; btnUT.textContent='Undertale'; btnUT.addEventListener('click',()=> openAU('Undertale')); auContent.appendChild(btnUT);
    const btnUS=document.createElement('button'); btnUS.className='auBtn';
    if(resets>=1){ btnUS.textContent='Underswap'; btnUS.addEventListener('click',()=> openAU('Underswap')); }
    else { btnUS.innerHTML='Underswap <span class="lock">Locked (Requires 1 reset)</span>'; btnUS.disabled=true; btnUS.style.opacity=.7; btnUS.style.cursor='not-allowed'; }
    auContent.appendChild(btnUS);
  }

  function openAU(name){
    auMode='characters'; selectedAU=name; auSubtitle.textContent=name+" roster"; auContent.innerHTML="";
    const back=document.createElement('button'); back.className='auBack'; back.textContent='← Back to AU select'; back.addEventListener('click',renderAUList); auContent.appendChild(back);

    roster[name].forEach((c,i)=>{
      const row=document.createElement('div'); row.className='charRow';
      const left=document.createElement('div'); left.className='charName'; left.textContent=c.label;
      const buy=document.createElement('button'); buy.className='buyBtn';
      buy.textContent = c.owned ? 'Owned' : 'Buy';
      buy.disabled = !!c.owned;
      buy.addEventListener('click',()=> buyChar(name,i));
      row.appendChild(left); row.appendChild(buy); auContent.appendChild(row);
    });
  }

  function buyChar(au,index){
    const c=roster[au][index];
    if(c.owned) return;
    if(gold>=c.costGold){
      gold -= c.costGold; c.owned = 1;
      if(c.type==='frisk'){ startFriskSlashLoop(); }
      tryConvertExpToLove(); updateStats(); openAU(au); gentlePop(auContent);
    } else { shake(auContent); }
  }

  /* ===== Passive DPS (FRISK excluded) ===== */
  setInterval(()=>{
    if(soulDisabled) return;
    let totalDps=0;
    Object.values(roster).forEach(list=>{
      list.forEach(c=>{
        if(c.owned && c.type==='dps'){ totalDps += c.dps; }
      });
    });
    if(totalDps>0){ applyDamage(totalDps, true); }
  }, 1000);

  /* ===== INIT ===== */
  load(); updateStats(); renderAUList();
  if(roster.Undertale.find(x=> x.type==='frisk' && x.owned)) startFriskSlashLoop();
</script>
</body>
</html>
