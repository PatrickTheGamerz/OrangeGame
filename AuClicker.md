<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Minimal Clicker</title>
  <style>
    :root {
      --bg: #0f1115;
      --panel: #171923;
      --accent: #5ee6a7;
      --text: #e6e8ef;
      --muted: #9aa0aa;
      --warn: #ffbf69;
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, "Helvetica Neue", Arial, sans-serif;
      background: radial-gradient(1200px 600px at 20% -10%, #141822 0%, var(--bg) 50%);
      color: var(--text);
      display: grid;
      place-items: center;
    }
    .app {
      width: min(880px, 92vw);
      display: grid;
      gap: 16px;
      grid-template-columns: 1fr 1fr;
      align-items: start;
    }
    .panel {
      background: linear-gradient(180deg, #191c27 0%, var(--panel) 100%);
      border: 1px solid #262a35;
      border-radius: 12px;
      box-shadow: 0 12px 24px rgba(0,0,0,0.35), inset 0 1px 0 rgba(255,255,255,0.04);
      padding: 16px;
    }
    .panel h2 {
      margin: 0 0 8px;
      font-size: 16px;
      letter-spacing: 0.2px;
      color: var(--muted);
      font-weight: 600;
    }
    .metric {
      display: flex; align-items: baseline; gap: 12px;
      padding: 8px 0 12px;
      border-bottom: 1px dashed #2a2f3d;
    }
    .metric .value { font-size: 32px; font-weight: 700; color: var(--text); }
    .metric .rate { font-size: 13px; color: var(--muted); }
    .actions { display: grid; gap: 12px; margin-top: 12px; }
    button {
      all: unset;
      display: inline-grid; place-items: center;
      padding: 12px 16px;
      border-radius: 10px;
      background: #232836;
      color: var(--text);
      border: 1px solid #2c3241;
      cursor: pointer;
      transition: transform 0.05s ease, box-shadow 0.15s ease, border-color 0.2s ease, background 0.2s ease;
    }
    button:hover { border-color: #3a4154; box-shadow: 0 6px 16px rgba(0,0,0,0.25); }
    button:active { transform: translateY(1px) scale(0.995); }
    .cta {
      font-size: 18px; font-weight: 700;
      background: linear-gradient(180deg, #243047 0%, #202636 100%);
      border: 1px solid #33405a;
    }
    .grid { display: grid; gap: 10px; }
    .grid.cols-2 { grid-template-columns: 1fr 1fr; }
    .item {
      display: grid; grid-template-columns: 1fr auto; gap: 8px; align-items: center;
      padding: 10px; border-radius: 8px; background: #1c2030; border: 1px solid #2a3143;
    }
    .item h3 { margin: 0; font-size: 14px; font-weight: 700; color: var(--text); }
    .item p { margin: 0; font-size: 12px; color: var(--muted); }
    .price { font-size: 12px; color: var(--warn); }
    .disabled { opacity: 0.55; cursor: not-allowed; }
    .badge {
      display: inline-block; padding: 4px 8px; border-radius: 999px; font-size: 11px;
      background: #243047; color: var(--accent); border: 1px solid #2f3d56;
    }
    .footer {
      grid-column: 1 / -1; text-align: center; font-size: 12px; color: var(--muted);
    }
  </style>
</head>
<body>
  <main class="app" id="app">
    <section class="panel" id="leftPanel">
      <h2>Clicker</h2>
      <div class="metric">
        <div class="value" id="count">0</div>
        <div class="rate" id="rate">+0 /s</div>
      </div>
      <div class="actions">
        <button class="cta" id="clickButton">Click</button>
        <div class="grid cols-2">
          <button id="buyCursor"><span>Buy Cursor</span><span class="price" id="cursorPrice">Cost: 10</span></button>
          <button id="buyFactory"><span>Buy Factory</span><span class="price" id="factoryPrice">Cost: 100</span></button>
        </div>
        <div class="grid">
          <button id="upgradeClick"><span>Upgrade Click</span><span class="price" id="clickUpgradePrice">Cost: 25</span></button>
          <button id="upgradeAutomation"><span>Upgrade Automation</span><span class="price" id="automationUpgradePrice">Cost: 150</span></button>
        </div>
      </div>
    </section>

    <section class="panel" id="rightPanel">
      <h2>Status</h2>
      <div class="grid">
        <div class="item">
          <div>
            <h3>Automation</h3>
            <p id="ownedCursor">Cursors: 0</p>
            <p id="ownedFactory">Factories: 0</p>
          </div>
          <span class="badge" id="pps">0 /s</span>
        </div>
        <div class="item">
          <div>
            <h3>Click power</h3>
            <p id="clickPowerLabel">Per click: 1</p>
          </div>
          <span class="badge" id="multLabel">x1.00</span>
        </div>
        <div class="item">
          <div>
            <h3>Milestones</h3>
            <p id="milestoneLabel">None yet</p>
          </div>
          <span class="badge" id="nextMilestone">Next: 1k</span>
        </div>
      </div>
    </section>

    <footer class="footer">
      Minimal Clicker — autosaves locally. Reset with Ctrl+Shift+R (hard reload).
    </footer>
  </main>

  <script>
    // ---- Data model ---------------------------------------------------------
    const state = {
      count: 0,
      perClick: 1,
      clickLevel: 0,
      autoLevel: 0,
      owned: { cursor: 0, factory: 0 },
      prices: {
        cursor: 10,
        factory: 100,
        clickUpgrade: 25,
        automationUpgrade: 150
      },
      multipliers: { click: 1.0, auto: 1.0 },
      lastTick: performance.now(),
    };

    const CFG = {
      cursorBasePps: 0.1,   // per second per cursor
      factoryBasePps: 2.0,  // per second per factory
      priceScale: 1.15,     // exponential price scaling per purchase
      clickScale: 1.6,      // per-click upgrade scale
      autoScale: 1.4,       // automation multiplier scale
      milestoneSteps: [1000, 5000, 20000, 100000, 500000, 1000000]
    };

    // ---- DOM refs -----------------------------------------------------------
    const $ = id => document.getElementById(id);
    const ui = {
      count: $("count"),
      rate: $("rate"),
      ppsBadge: $("pps"),
      multLabel: $("multLabel"),
      clickPowerLabel: $("clickPowerLabel"),
      ownedCursor: $("ownedCursor"),
      ownedFactory: $("ownedFactory"),
      milestoneLabel: $("milestoneLabel"),
      nextMilestone: $("nextMilestone"),
      clickButton: $("clickButton"),
      buyCursor: $("buyCursor"),
      buyFactory: $("buyFactory"),
      cursorPrice: $("cursorPrice"),
      factoryPrice: $("factoryPrice"),
      upgradeClick: $("upgradeClick"),
      upgradeAutomation: $("upgradeAutomation"),
      clickUpgradePrice: $("clickUpgradePrice"),
      automationUpgradePrice: $("automationUpgradePrice"),
    };

    // ---- Persistence --------------------------------------------------------
    const KEY = "minimal-clicker-save-v1";
    function save() { localStorage.setItem(KEY, JSON.stringify(state)); }
    function load() {
      const raw = localStorage.getItem(KEY);
      if (!raw) return;
      try {
        const data = JSON.parse(raw);
        Object.assign(state, data);
      } catch (_) { /* ignore */ }
    }

    // ---- Math helpers -------------------------------------------------------
    function fmt(n) {
      if (n < 1000) return n.toFixed(0);
      const units = ["k","M","B","T","Q"];
      let u = -1, v = n;
      while (v >= 1000 && u < units.length - 1) { v /= 1000; u++; }
      return v.toFixed(v < 10 ? 2 : v < 100 ? 1 : 0) + units[u];
    }
    function round2(n) { return Math.round(n * 100) / 100; }

    // ---- Game logic ---------------------------------------------------------
    function currentPps() {
      const base =
        state.owned.cursor * CFG.cursorBasePps +
        state.owned.factory * CFG.factoryBasePps;
      return base * state.multipliers.auto;
    }

    function nextMilestoneTarget() {
      for (const m of CFG.milestoneSteps) if (state.count < m) return m;
      return CFG.milestoneSteps[CFG.milestoneSteps.length - 1];
    }

    function applyMilestones() {
      const reached = CFG.milestoneSteps.filter(m => state.count >= m);
      if (reached.length === 0) {
        ui.milestoneLabel.textContent = "None yet";
        ui.nextMilestone.textContent = "Next: " + fmt(nextMilestoneTarget());
        return;
      }
      const last = reached[reached.length - 1];
      const bonus = 1 + reached.length * 0.05; // +5% click power per milestone
      state.multipliers.click = bonus;
      ui.milestoneLabel.textContent = "Reached: " + fmt(last) + " (+5% x " + reached.length + ")";
      ui.nextMilestone.textContent = "Next: " + fmt(nextMilestoneTarget());
    }

    function add(n) {
      state.count += n;
      save();
      render();
    }

    function purchase(kind) {
      const priceKey = kind;
      const price = state.prices[priceKey];
      if (state.count < price) return;
      state.count -= price;
      if (kind === "cursor" || kind === "factory") {
        state.owned[kind] += 1;
        state.prices[kind] = Math.ceil(state.prices[kind] * CFG.priceScale);
      }
      save();
      render();
    }

    function upgrade(type) {
      if (type === "click") {
        if (state.count < state.prices.clickUpgrade) return;
        state.count -= state.prices.clickUpgrade;
        state.clickLevel += 1;
        state.perClick = Math.ceil(state.perClick * CFG.clickScale);
        state.prices.clickUpgrade = Math.ceil(state.prices.clickUpgrade * CFG.priceScale);
      } else if (type === "automation") {
        if (state.count < state.prices.automationUpgrade) return;
        state.count -= state.prices.automationUpgrade;
        state.autoLevel += 1;
        state.multipliers.auto = round2(state.multipliers.auto * CFG.autoScale);
        state.prices.automationUpgrade = Math.ceil(state.prices.automationUpgrade * CFG.priceScale);
      }
      save();
      render();
    }

    // ---- Ticking ------------------------------------------------------------
    function tick(now) {
      const dt = Math.max(0, now - state.lastTick) / 1000;
      state.lastTick = now;
      const pps = currentPps();
      if (pps > 0) {
        state.count += pps * dt;
        // prevent floating drift in storage
        state.count = round2(state.count);
        render(false); // lightweight render during tick
      }
      applyMilestones();
      save();
    }

    // ---- UI updates ---------------------------------------------------------
    function render(force = true) {
      ui.count.textContent = fmt(Math.floor(state.count));
      const pps = currentPps();
      ui.rate.textContent = "+" + round2(pps) + " /s";
      ui.ppsBadge.textContent = round2(pps) + " /s";

      ui.clickPowerLabel.textContent = "Per click: " + Math.floor(state.perClick * state.multipliers.click);
      ui.multLabel.textContent = "x" + state.multipliers.click.toFixed(2);

      ui.ownedCursor.textContent = "Cursors: " + state.owned.cursor;
      ui.ownedFactory.textContent = "Factories: " + state.owned.factory;

      ui.cursorPrice.textContent = "Cost: " + Math.ceil(state.prices.cursor);
      ui.factoryPrice.textContent = "Cost: " + Math.ceil(state.prices.factory);
      ui.clickUpgradePrice.textContent = "Cost: " + Math.ceil(state.prices.clickUpgrade);
      ui.automationUpgradePrice.textContent = "Cost: " + Math.ceil(state.prices.automationUpgrade);

      // enable/disable buttons based on affordability
      const afford = k => state.count >= state.prices[k];
      toggleDisabled(ui.buyCursor, !afford("cursor"));
      toggleDisabled(ui.buyFactory, !afford("factory"));
      toggleDisabled(ui.upgradeClick, !afford("clickUpgrade"));
      toggleDisabled(ui.upgradeAutomation, !afford("automationUpgrade"));

      if (force) applyMilestones();
    }

    function toggleDisabled(el, disabled) {
      el.classList.toggle("disabled", disabled);
    }

    // ---- Events -------------------------------------------------------------
    function bind() {
      ui.clickButton.addEventListener("click", () => {
        const gain = state.perClick * state.multipliers.click;
        add(gain);
      });
      ui.buyCursor.addEventListener("click", () => purchase("cursor"));
      ui.buyFactory.addEventListener("click", () => purchase("factory"));
      ui.upgradeClick.addEventListener("click", () => upgrade("click"));
      ui.upgradeAutomation.addEventListener("click", () => upgrade("automation"));
      document.addEventListener("keydown", e => {
        if (e.code === "Space") { e.preventDefault(); ui.clickButton.click(); }
      });
    }

    // ---- Boot ---------------------------------------------------------------
    function boot() {
      load();
      bind();
      render();
      state.lastTick = performance.now();
      const loop = (t) => { tick(t); requestAnimationFrame(loop); };
      requestAnimationFrame(loop);
    }
    boot();
  </script>
</body>
</html>
