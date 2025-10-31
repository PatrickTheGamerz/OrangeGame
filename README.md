<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>AU Clicker — HTML Only</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root{
      --bg:#0a0a0a;
      --panel:#131313;
      --panel-2:#1a1a1a;
      --accent:#ffcc00;
      --accent-2:#7cf3ff;
      --accent-3:#ff7c9a;
      --text:#f5f5f5;
      --muted:#bdbdbd;
      --danger:#ff4d4f;
      --good:#51c15e;
      --shadow:0 8px 24px rgba(0,0,0,.35);
      --mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono","Courier New", monospace;
      --sans: Inter, system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji", "Segoe UI Symbol";
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; background:radial-gradient(1200px 800px at 70% 10%, #111 0%, var(--bg) 38%, #000 100%);
      color:var(--text); font-family:var(--sans);
    }
    .app{
      display:grid; grid-template-columns: 320px 1fr 380px; gap:16px;
      padding:16px; height:100%;
    }
    header{
      grid-column:1 / -1; display:flex; align-items:center; justify-content:space-between;
      padding:12px 16px; background:var(--panel-2); border:1px solid #222; border-radius:12px; box-shadow:var(--shadow);
    }
    header .title{
      display:flex; align-items:center; gap:12px; letter-spacing:0.5px;
    }
    .soul{
      width:18px; height:18px; border-radius:50%; background:var(--accent-3);
      box-shadow:0 0 12px var(--accent-3), inset 0 -3px 8px rgba(255,255,255,.2);
    }
    .subtitle{color:var(--muted); font-size:14px}
    .pill{
      padding:8px 12px; border-radius:999px; background:#0f0f0f; border:1px solid #222; color:var(--muted);
      display:flex; align-items:center; gap:8px; font-family:var(--mono);
    }
    .panel{
      background:var(--panel); border:1px solid #1e1e1e; border-radius:12px; box-shadow:var(--shadow); overflow:hidden;
    }
    .panel h3{
      margin:0; padding:12px 14px; border-bottom:1px solid #1e1e1e; background:linear-gradient(180deg, #171717, #121212);
      font-size:15px; letter-spacing:.4px; color:#ddd;
    }
    .panel .content{padding:12px}
    .stats{
      display:grid; grid-template-columns:1fr 1fr; gap:10px;
    }
    .stat{
      background:#0f0f0f; border:1px solid #202020; border-radius:8px; padding:10px; display:flex; justify-content:space-between; align-items:center;
    }
    .stat .label{color:var(--muted); font-size:12px}
    .stat .value{font-family:var(--mono)}
    .big-button{
      display:flex; flex-direction:column; align-items:center; justify-content:center; gap:8px;
      width:100%; height:260px; border-radius:12px; border:1px solid #202020; background:radial-gradient(240px 140px at 50% 40%, #1b1b1b 0%, #131313 60%, #0f0f0f 100%);
      box-shadow:0 10px 30px rgba(0,0,0,.5), inset 0 0 24px rgba(255,255,255,.035);
      cursor:pointer; user-select:none; transition:transform .06s ease, box-shadow .06s ease;
    }
    .big-button:hover{transform:translateY(-2px)}
    .big-button:active{transform:translateY(0)}
    .click-value{font-size:44px; font-family:var(--mono); color:var(--accent); text-shadow:0 0 12px rgba(255,204,0,.35)}
    .click-label{color:var(--muted); font-size:13px}
    .row{display:flex; gap:12px}
    .col{display:flex; flex-direction:column; gap:12px}
    .store-list{display:flex; flex-direction:column; gap:8px; max-height:calc(100vh - 280px); overflow:auto; padding-right:6px}
    .item{
      display:grid; grid-template-columns:1fr auto; gap:10px; align-items:center;
      padding:10px; border-radius:10px; background:#0f0f0f; border:1px solid #202020;
    }
    .item.locked{opacity:.6; filter:saturate(.8)}
    .item .name{font-weight:600}
    .item .desc{color:var(--muted); font-size:12px}
    .item .cost{font-family:var(--mono); color:#eee}
    .item .buy{
      padding:8px 10px; border-radius:8px; background:linear-gradient(180deg, #212121 0%, #151515 100%);
      border:1px solid #2a2a2a; color:#eee; cursor:pointer; transition:.12s ease; font-family:var(--mono);
    }
    .item .buy:disabled{opacity:.5; cursor:not-allowed}
    .badge{font-size:11px; color:var(--accent-2); font-family:var(--mono)}
    .footer-row{display:flex; align-items:center; justify-content:space-between; gap:12px; margin-top:8px; color:var(--muted)}
    .progress{
      height:8px; background:#0f0f0f; border:1px solid #202020; border-radius:999px; overflow:hidden;
    }
    .progress > div{
      height:100%; width:0%; background:linear-gradient(90deg, var(--accent) 0%, #ffe88a 100%);
      box-shadow:0 0 12px rgba(255,204,0,.4); transition:width .2s ease;
    }
    .tabs{display:flex; gap:8px; margin-bottom:8px}
    .tab{
      padding:8px 12px; border-radius:8px; border:1px solid #242424; background:#121212; color:#ddd; cursor:pointer; font-family:var(--mono);
    }
    .tab.active{background:#1a1a1a; color:#fff; border-color:#2c2c2c}
    .log{
      font-family:var(--mono); font-size:12px; background:#0d0d0d; border:1px solid #202020; border-radius:8px; padding:10px; max-height:180px; overflow:auto;
    }
    .log .entry{padding:4px 0; border-bottom:1px dashed #1f1f1f}
    .log .entry:last-child{border-bottom:none}
    .pill-button{
      padding:6px 10px; border-radius:999px; border:1px solid #242424; background:#121212; color:#ddd; cursor:pointer; font-family:var(--mono);
    }
    .pill-button.red{color:#fff; border-color:#3a1313; background:#1c0f10}
    .pill-button.green{color:#fff; border-color:#163a19; background:#0f1c10}
    .secret{
      display:none; padding:10px; border-radius:10px; border:1px dashed #2a2a2a; background:#0f0f0f; color:#eee
    }
    .secret.show{display:block}
    .toast{
      position:fixed; left:50%; transform:translateX(-50%); bottom:20px; background:#141414; border:1px solid #262626;
      border-radius:10px; padding:10px 12px; color:#eee; box-shadow:var(--shadow); font-family:var(--mono);
      opacity:0; pointer-events:none; transition:opacity .15s ease, transform .15s ease;
    }
    .toast.show{opacity:1; transform:translateX(-50%) translateY(-6px)}
    @media (max-width:1100px){
      .app{grid-template-columns:1fr}
      .store-list{max-height:none}
    }
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="title">
        <div class="soul" aria-hidden="true"></div>
        <div>
          <div style="font-weight:700">AU Clicker</div>
          <div class="subtitle">Undertale-inspired, HTML-only</div>
        </div>
      </div>
      <div style="display:flex; gap:8px; align-items:center">
        <div class="pill">
          <span>LV</span>
          <strong id="lv" style="color:#fff">0</strong>
        </div>
        <div class="pill">
          <span>Flow</span>
          <strong id="dps" style="color:#fff">0/s</strong>
        </div>
        <button class="pill-button" id="saveBtn" title="Save to browser">Save</button>
        <button class="pill-button" id="exportBtn" title="Copy save to clipboard">Export</button>
        <button class="pill-button red" id="resetBtn" title="Hard reset">Reset</button>
      </div>
    </header>

    <!-- Left: click core -->
    <div class="panel">
      <h3>Click core</h3>
      <div class="content">
        <div class="stats">
          <div class="stat">
            <div>
              <div class="label">Total clicks</div>
              <div class="value" id="totalClicks">0</div>
            </div>
            <div>
              <div class="label">Per click</div>
              <div class="value" id="perClick">1</div>
            </div>
          </div>
          <div class="stat">
            <div>
              <div class="label">Unlocked AUs</div>
              <div class="value" id="unlockedCount">0</div>
            </div>
            <div>
              <div class="label">Milestone</div>
              <div class="value" id="milestone">None</div>
            </div>
          </div>
        </div>

        <div style="margin-top:12px" class="row">
          <div class="col" style="flex:1">
            <button id="clickBtn" class="big-button" aria-label="Gain LV">
              <div class="click-value" id="clickValuePreview">+1 LV</div>
              <div class="click-label">Click to gain LV and progress milestones</div>
            </button>

            <div class="footer-row">
              <div style="display:flex; align-items:center; gap:8px">
                <span>Next milestone</span>
                <span class="badge" id="nextMilestoneLabel">LV 100</span>
              </div>
              <div class="progress" style="flex:1">
                <div id="milestoneProgress"></div>
              </div>
            </div>
          </div>

          <div class="col" style="width:280px">
            <div class="panel">
              <h3>Upgrades</h3>
              <div class="content">
                <div class="store-list" id="upgradeList"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Middle: AUs store -->
    <div class="panel">
      <h3>AU roster</h3>
      <div class="content">
        <div class="store-list" id="auList"></div>
      </div>
    </div>

    <!-- Right: Events & log -->
    <div class="panel">
      <h3>Events and secrets</h3>
      <div class="content">
        <div class="tabs">
          <button class="tab active" data-tab="events">Events</button>
          <button class="tab" data-tab="log">Log</button>
          <button class="tab" data-tab="settings">Settings</button>
        </div>

        <div id="tab-events">
          <div class="stat" style="margin-bottom:8px">
            <div>
              <div class="label">Current event</div>
              <div class="value" id="eventName">None</div>
            </div>
            <div>
              <div class="label">Time left</div>
              <div class="value" id="eventTimer">—</div>
            </div>
          </div>

          <div class="item" id="eventBoost" style="display:none">
            <div>
              <div class="name">Event boost</div>
              <div class="desc">Temporary multiplier applied to click and flow.</div>
              <div class="badge" id="eventMultiplierLabel">x1.0</div>
            </div>
            <div>
              <button class="buy" id="eventClaimBtn">Claim reward</button>
            </div>
          </div>

          <div class="secret" id="secretPanel">
            <strong>Secret unlocked:</strong> Konami code detected. Hidden AU available in roster.
          </div>
        </div>

        <div id="tab-log" style="display:none">
          <div class="log" id="log"></div>
        </div>

        <div id="tab-settings" style="display:none">
          <div class="row">
            <button class="pill-button green" id="softSave">Quick save</button>
            <button class="pill-button" id="softLoad">Load</button>
            <button class="pill-button red" id="softReset">Soft reset</button>
          </div>
          <div style="margin-top:10px; color:var(--muted); font-size:12px">
            Soft reset keeps achievements; hard reset clears everything. Saves use your browser storage.
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="toast" id="toast">Saved</div>

  <script>
    // ===== Core state =====
    const initialState = {
      lv: 0,
      totalClicks: 0,
      perClick: 1,
      flow: 0, // DPS
      upgrades: [],
      aus: [],
      milestones: [100, 500, 2000, 10000, 50000],
      nextMilestoneIndex: 0,
      achievements: {},
      event: null, // { name, until, multiplier, claimed }
      secrets: { konami: false },
      version: 1
    };

    const AU_DEFS = [
      { id:'classic_sans', name:'Classic Sans', desc:'Wry grin, steady drip of LV.', baseCost: 25, baseFlow: 0.5 },
      { id:'undyne_hero', name:'Undyne the Hero', desc:'Stronger flow, scales with milestones.', baseCost: 120, baseFlow: 2.2 },
      { id:'papyrus_puzz', name:'Papyrus', desc:'Charming boost, small cost curve.', baseCost: 280, baseFlow: 4.0 },
      { id:'error_sans', name:'Error!Sans', desc:'Glitchy surge. Unlock after LV 500.', baseCost: 2_000, baseFlow: 16.0, req: s => s.lv >= 500 },
      { id:'ink_sans', name:'Ink', desc:'Creative multiplier to all AUs.', baseCost: 8_000, baseFlow: 0, globalMult: 1.12, req: s => s.lv >= 1500 },
      { id:'dusttrust', name:'Dusttrust', desc:'Heavy LV flow, high cost.', baseCost: 25_000, baseFlow: 55.0, req: s => s.lv >= 4000 },
      // Hidden — revealed via Konami
      { id:'distrust_echo', name:'Distrust Echo', desc:'Secret AU: amplifies click value slightly.', baseCost: 7_500, baseFlow: 9.0, hidden: true, clickMult: 1.08 }
    ];

    const UPGRADE_DEFS = [
      { id:'strong_finger', name:'Strong Finger', desc:'Increase LV per click by +1.', baseCost: 15, effect: s => s.perClick += 1 },
      { id:'efficient_flow', name:'Efficient Flow', desc:'Multiply flow by x1.10.', baseCost: 80, effect: s => applyFlowMult(1.10) },
      { id:'golden_touch', name:'Golden Touch', desc:'Multiply click value by x1.25.', baseCost: 160, effect: s => applyClickMult(1.25) },
      { id:'milestone_echo', name:'Milestone Echo', desc:'Each milestone adds +5% flow.', baseCost: 420, effect: s => s.achievements.milestoneEcho = (s.achievements.milestoneEcho||0)+1 },
      { id:'steady_beat', name:'Steady Beat', desc:'Flat +3 flow.', baseCost: 500, effect: s => applyFlatFlow(3) },
      { id:'true_grit', name:'True Grit', desc:'Multiply flow by x1.30.', baseCost: 1_200, effect: s => applyFlowMult(1.30) }
    ];

    const state = loadState() || structuredClone(initialState);

    // ===== Utils =====
    const $ = sel => document.querySelector(sel);
    const $$ = sel => Array.from(document.querySelectorAll(sel));
    const fmt = n => {
      if (n >= 1e9) return (n/1e9).toFixed(2) + 'B';
      if (n >= 1e6) return (n/1e6).toFixed(2) + 'M';
      if (n >= 1e3) return (n/1e3).toFixed(2) + 'K';
      return Math.floor(n).toString();
    };
    const toast = (msg) => {
      const t = $('#toast');
      t.textContent = msg;
      t.classList.add('show');
      setTimeout(()=>t.classList.remove('show'), 1500);
    };
    const log = (msg) => {
      const el = document.createElement('div');
      el.className = 'entry';
      const ts = new Date().toLocaleTimeString();
      el.textContent = `[${ts}] ${msg}`;
      $('#log').prepend(el);
    };

    // ===== Derived calculations =====
    function recalcFlow() {
      let total = 0;
      let globalMult = 1;
      let clickMult = 1;

      // AUs effects
      for (const au of state.aus) {
        const def = AU_DEFS.find(d => d.id === au.id);
        if (!def) continue;
        // Base flow contribution
        total += au.count * (def.baseFlow || 0);
        // Global flow multiplier contributions
        if (def.globalMult) globalMult *= Math.pow(def.globalMult, au.count);
        // Click multiplier contributions
        if (def.clickMult) clickMult *= Math.pow(def.clickMult, au.count);
      }

      // Milestone Echo upgrade effect (each tier adds 5% per milestone reached)
      const echoTier = state.achievements.milestoneEcho || 0;
      const milestonesReached = state.nextMilestoneIndex;
      const echoMult = 1 + (0.05 * milestonesReached * echoTier);

      // Event multiplier
      const eventMult = (state.event && !state.event.claimed) ? state.event.multiplier : 1;

      // Apply multipliers
      state.flow = total * globalMult * echoMult * eventMult;
      state.perClick = state.perClickBase ? state.perClickBase : state.perClick;
      state.perClickEffective = Math.max(1, Math.floor((state.perClickBase || state.perClick) * clickMult * eventMult));
    }
    function applyFlowMult(m){ // multiplicative upgrade stored as a phantom AU
      state.aus.push({ id:'__flow_mult_'+Math.random().toString(36).slice(2), count:1, phantom:true, flowMult:m, baseFlow:0, globalMult:m });
      recalcFlow();
    }
    function applyClickMult(m){
      state.aus.push({ id:'__click_mult_'+Math.random().toString(36).slice(2), count:1, phantom:true, clickMult:m, baseFlow:0 });
      recalcFlow();
    }
    function applyFlatFlow(add){
      state.aus.push({ id:'__flat_flow_'+Math.random().toString(36).slice(2), count:add, phantom:true, baseFlow:1 });
      recalcFlow();
    }

    // ===== Economy and costs =====
    function nextMilestoneTarget(){
      return state.milestones[state.nextMilestoneIndex] ?? null;
    }
    function milestoneProgress(){
      const target = nextMilestoneTarget();
      if (!target) return 1;
      const prev = state.milestones[state.nextMilestoneIndex-1] || 0;
      const span = target - prev;
      const cur = Math.min(target, state.lv);
      return (cur - prev) / span;
    }
    function auCost(def, owned){
      // Smooth exponential curve: base * (1.15 ^ owned)
      return Math.ceil(def.baseCost * Math.pow(1.15, owned));
    }
    function upgradeCost(def, purchased){
      return Math.ceil(def.baseCost * Math.pow(1.45, purchased));
    }

    // ===== Persistence =====
    function saveState(){
      const data = {
        lv: state.lv,
        totalClicks: state.totalClicks,
        perClickBase: state.perClickBase || state.perClick,
        aus: state.aus,
        upgrades: state.upgrades,
        milestones: state.milestones,
        nextMilestoneIndex: state.nextMilestoneIndex,
        achievements: state.achievements,
        event: state.event,
        secrets: state.secrets,
        version: state.version
      };
      localStorage.setItem('au_clicker_save', JSON.stringify(data));
      toast('Saved');
    }
    function loadState(){
      const raw = localStorage.getItem('au_clicker_save');
      if (!raw) return null;
      try {
        const data = JSON.parse(raw);
        // migrate if needed
        if (!data.version) data.version = 1;
        return data;
      } catch(e){
        console.warn('Save parse error', e);
        return null;
      }
    }
    function exportSave(){
      const raw = localStorage.getItem('au_clicker_save') || JSON.stringify(state);
      navigator.clipboard.writeText(raw).then(()=>toast('Save copied'));
    }
    function hardReset(){
      localStorage.removeItem('au_clicker_save');
      Object.assign(state, structuredClone(initialState));
      renderAll();
      toast('Reset complete');
      log('Hard reset performed.');
    }
    function softReset(){
      // Keep milestones achieved (nextMilestoneIndex), wipe LV/AUs/upgrades
      const keep = { milestones: state.milestones, nextMilestoneIndex: state.nextMilestoneIndex, achievements: state.achievements, secrets: state.secrets };
      Object.assign(state, structuredClone(initialState), keep);
      renderAll();
      toast('Soft reset complete');
      log('Soft reset performed.');
    }

    // ===== Rendering =====
    function renderStats(){
      $('#lv').textContent = fmt(state.lv);
      $('#dps').textContent = `${fmt(state.flow)}/s`;
      $('#totalClicks').textContent = fmt(state.totalClicks);
      $('#perClick').textContent = fmt(state.perClickEffective || state.perClick);
      $('#clickValuePreview').textContent = `+${fmt(state.perClickEffective || state.perClick)} LV`;
      $('#unlockedCount').textContent = state.aus.filter(a => !a.phantom && a.count>0).length.toString();
      const target = nextMilestoneTarget();
      $('#nextMilestoneLabel').textContent = target ? `LV ${fmt(target)}` : 'Maxed';
      $('#milestone').textContent = target ? `Target: LV ${fmt(target)}` : 'All milestones reached';
      $('#milestoneProgress').style.width = (milestoneProgress()*100).toFixed(1) + '%';
    }
    function renderAUs(){
      const container = $('#auList');
      container.innerHTML = '';
      AU_DEFS.forEach(def => {
        const owned = state.aus.find(a => a.id === def.id)?.count || 0;
        const meetsReq = def.req ? def.req(state) : true;
        const hidden = def.hidden && !state.secrets.konami;
        const locked = !meetsReq || hidden;

        const item = document.createElement('div');
        item.className = 'item' + (locked ? ' locked' : '');
        const cost = auCost(def, owned);
        item.innerHTML = `
          <div>
            <div class="name">${def.name}</div>
            <div class="desc">${def.desc}</div>
            <div class="badge">Flow: ${def.baseFlow ? def.baseFlow+' / ea' : '—'}${def.globalMult ? ' • Global x'+def.globalMult.toFixed(2) : ''}${def.clickMult ? ' • Click x'+def.clickMult.toFixed(2) : ''}</div>
          </div>
          <div style="display:flex; flex-direction:column; align-items:flex-end; gap:6px">
            <div class="cost">Cost: ${fmt(cost)} LV</div>
            <button class="buy" ${locked ? 'disabled' : ''} data-id="${def.id}">Buy</button>
          </div>
        `;
        container.appendChild(item);
      });

      container.querySelectorAll('.buy').forEach(btn => {
        btn.addEventListener('click', () => {
          const id = btn.getAttribute('data-id');
          buyAU(id);
        });
      });
    }
    function renderUpgrades(){
      const container = $('#upgradeList');
      container.innerHTML = '';
      UPGRADE_DEFS.forEach(def => {
        const purchased = state.upgrades.find(u => u.id === def.id)?.count || 0;
        const cost = upgradeCost(def, purchased);
        const item = document.createElement('div');
        item.className = 'item';
        item.innerHTML = `
          <div>
            <div class="name">${def.name}</div>
            <div class="desc">${def.desc}</div>
            <div class="badge">Purchased: ${purchased}</div>
          </div>
          <div style="display:flex; flex-direction:column; align-items:flex-end; gap:6px">
            <div class="cost">Cost: ${fmt(cost)} LV</div>
            <button class="buy" data-id="${def.id}">Buy</button>
          </div>
        `;
        container.appendChild(item);
      });

      container.querySelectorAll('.buy').forEach(btn => {
        btn.addEventListener('click', () => {
          const id = btn.getAttribute('data-id');
          buyUpgrade(id);
        });
      });
    }
    function renderEvent(){
      const e = state.event;
      if (!e){
        $('#eventName').textContent = 'None';
        $('#eventTimer').textContent = '—';
        $('#eventBoost').style.display = 'none';
      } else {
        $('#eventName').textContent = e.name;
        const secs = Math.max(0, Math.ceil((e.until - Date.now()) / 1000));
        $('#eventTimer').textContent = `${secs}s`;
        $('#eventBoost').style.display = 'grid';
        $('#eventMultiplierLabel').textContent = 'x' + e.multiplier.toFixed(2);
        $('#eventClaimBtn').disabled = e.claimed || secs > 0;
      }
    }
    function renderSecrets(){
      $('#secretPanel').classList.toggle('show', state.secrets.konami);
    }
    function renderTabs(){
      $$('.tab').forEach(t => t.classList.remove('active'));
      // default handled elsewhere
    }
    function renderAll(){
      recalcFlow();
      renderStats();
      renderAUs();
      renderUpgrades();
      renderEvent();
      renderSecrets();
    }

    // ===== Actions =====
    function clickGain(){
      const gain = state.perClickEffective || state.perClick;
      state.lv += gain;
      state.totalClicks += 1;
      maybeMilestone();
      renderStats();
    }
    function buyAU(id){
      const def = AU_DEFS.find(d => d.id === id);
      if (!def) return;
      const owned = state.aus.find(a => a.id === id)?.count || 0;
      const cost = auCost(def, owned);
      const meetsReq = def.req ? def.req(state) : true;
      const hidden = def.hidden && !state.secrets.konami;
      if (!meetsReq || hidden) return;
      if (state.lv < cost){ toast('Not enough LV'); return; }
      state.lv -= cost;
      const entry = state.aus.find(a => a.id === id);
      if (entry) entry.count += 1;
      else state.aus.push({ id, count:1 });
      recalcFlow();
      renderAll();
      log(`Bought ${def.name} (owned ${owned+1})`);
    }
    function buyUpgrade(id){
      const def = UPGRADE_DEFS.find(d => d.id === id);
      if (!def) return;
      const purchased = state.upgrades.find(u => u.id === id)?.count || 0;
      const cost = upgradeCost(def, purchased);
      if (state.lv < cost){ toast('Not enough LV'); return; }
      state.lv -= cost;
      if (id === 'golden_touch'){
        state.perClickBase = (state.perClickBase || state.perClick) * 1.25;
      } else if (id === 'strong_finger'){
        state.perClickBase = (state.perClickBase || state.perClick) + 1;
      }
      def.effect(state);
      const entry = state.upgrades.find(u => u.id === id);
      if (entry) entry.count += 1; else state.upgrades.push({ id, count:1 });
      recalcFlow();
      renderAll();
      log(`Purchased upgrade: ${def.name} (tier ${purchased+1})`);
    }
    function maybeMilestone(){
      const target = nextMilestoneTarget();
      if (!target) return;
      if (state.lv >= target){
        state.nextMilestoneIndex++;
        toast(`Milestone reached: LV ${fmt(target)}`);
        log(`Milestone reached: LV ${fmt(target)} — rewards applied.`);
        // Reward: temporary event
        spawnEvent('Milestone Surge', 20, 1.25);
        renderAll();
      }
    }

    // ===== Events =====
    function spawnEvent(name, seconds, multiplier){
      state.event = { name, until: Date.now() + seconds*1000, multiplier, claimed:false };
      renderEvent();
    }
    function tick(){
      // passive flow
      if (state.flow > 0){
        state.lv += state.flow / 10; // 10 ticks per second
      }
      // event expiry
      if (state.event){
        if (Date.now() >= state.event.until){
          // expired; allow claim if not yet claimed
          $('#eventClaimBtn').disabled = state.event.claimed;
        }
      }
      // random events
      maybeRandomEvent();
      renderStats();
    }
    let lastRandomEvent = 0;
    function maybeRandomEvent(){
      const now = Date.now();
      if (now - lastRandomEvent < 25_000) return; // at most every 25s
      const chance = 0.08; // 8% chance per check
      if (Math.random() < chance){
        lastRandomEvent = now;
        const names = ['Distrust Echo', 'Ink Burst', 'Glitch Flicker', 'Golden Flow'];
        const name = names[Math.floor(Math.random()*names.length)];
        const mult = 1.10 + Math.random()*0.35; // x1.10–x1.45
        const secs = 15 + Math.floor(Math.random()*15); // 15–30s
        spawnEvent(name, secs, mult);
        log(`Event started: ${name} for ${secs}s, multiplier x${mult.toFixed(2)}`);
        recalcFlow();
        renderAll();
      }
    }
    $('#eventClaimBtn').addEventListener('click', () => {
      if (!state.event || state.event.claimed) return;
      if (Date.now() < state.event.until){ toast('Event still active'); return; }
      state.event.claimed = true;
      const reward = Math.max(10, Math.floor((state.flow || 1) * 4));
      state.lv += reward;
      toast(`Claimed reward: +${fmt(reward)} LV`);
      log(`Event reward claimed: +${fmt(reward)} LV`);
      renderAll();
    });

    // ===== Tabs =====
    $$('.tab').forEach(tab => {
      tab.addEventListener('click', () => {
        $$('.tab').forEach(t => t.classList.remove('active'));
        tab.classList.add('active');
        const name = tab.dataset.tab;
        $('#tab-events').style.display = (name==='events')?'block':'none';
        $('#tab-log').style.display = (name==='log')?'block':'none';
        $('#tab-settings').style.display = (name==='settings')?'block':'none';
      });
    });

    // ===== Inputs =====
    $('#clickBtn').addEventListener('click', () => {
      clickGain();
    });

    $('#saveBtn').addEventListener('click', saveState);
    $('#exportBtn').addEventListener('click', exportSave);
    $('#resetBtn').addEventListener('click', hardReset);
    $('#softSave').addEventListener('click', saveState);
    $('#softLoad').addEventListener('click', () => {
      const loaded = loadState();
      if (loaded){ Object.assign(state, loaded); renderAll(); toast('Loaded'); log('Loaded save.'); }
      else toast('No save found');
    });
    $('#softReset').addEventListener('click', softReset);

    // ===== Secret: Konami code =====
    const KONAMI = ['ArrowUp','ArrowUp','ArrowDown','ArrowDown','ArrowLeft','ArrowRight','ArrowLeft','ArrowRight','b','a'];
    let konamiBuffer = [];
    window.addEventListener('keydown', (e) => {
      const key = e.key;
      konamiBuffer.push(key);
      if (konamiBuffer.length > KONAMI.length) konamiBuffer.shift();
      if (KONAMI.every((k,i)=>konamiBuffer[i]===k)){
        konamiBuffer = [];
        if (!state.secrets.konami){
          state.secrets.konami = true;
          toast('Secret unlocked!');
          log('Secret discovered: Konami code.');
          renderSecrets();
          renderAUs();
        }
      }
    });

    // ===== Boot =====
    // Initialize perClickBase to track pure click value before multipliers
    if (!state.perClickBase) state.perClickBase = state.perClick;
    recalcFlow();
    renderAll();
    setInterval(tick, 100); // 10 ticks per second

  </script>
</body>
</html>
