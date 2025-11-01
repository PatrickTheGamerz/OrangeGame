<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>UNDERTALE: AU CLICKER (HTML Only)</title>
<meta name="viewport" content="width=device-width, initial-scale=1" />
<style>
  :root {
    --bg: #000;
    --fg: #fff;
    --panel: #111;
    --accent: #fff;
    --disabled: #555;
  }
  * { box-sizing: border-box; }
  body {
    background: var(--bg);
    color: var(--fg);
    font-family: "Courier New", Courier, monospace;
    margin: 0;
    padding: 24px;
    text-align: center;
  }
  h1 { margin: 0 0 8px; letter-spacing: 1px; }
  #stats {
    display: inline-block;
    text-align: left;
    margin: 8px 0 16px;
    border: 1px solid var(--accent);
    padding: 12px 16px;
    background: var(--panel);
    min-width: 240px;
  }
  #clickBtn {
    font-size: 22px;
    padding: 16px 36px;
    margin: 16px 0 24px;
    cursor: pointer;
    background: var(--fg);
    color: var(--bg);
    border: 3px solid var(--accent);
  }
  #sections {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 16px;
    max-width: 1000px;
    margin: 0 auto;
  }
  .panel {
    border: 1px solid var(--accent);
    background: var(--panel);
    padding: 12px;
    text-align: left;
  }
  .character {
    border-top: 1px solid var(--accent);
    padding: 10px 0;
  }
  .row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
  }
  .meta { font-size: 13px; opacity: 0.9; }
  button.buy {
    padding: 8px 12px;
    border: 2px solid var(--accent);
    background: var(--fg);
    color: var(--bg);
    cursor: pointer;
    min-width: 88px;
  }
  button.buy:disabled {
    background: var(--disabled);
    border-color: var(--disabled);
    color: #222;
    cursor: not-allowed;
  }
  .small { font-size: 12px; opacity: 0.9; }
  .right { text-align: right; }
</style>
</head>
<body>

<h1>UNDERTALE: AU CLICKER</h1>
<div id="stats">
  <div><strong>LV:</strong> <span id="lv">0</span></div>
  <div><strong>Total clicks:</strong> <span id="clicks">0</span></div>
  <div class="small"><strong>LV/sec:</strong> <span id="lvs">0</span></div>
</div>

<button id="clickBtn" aria-label="Click to gain LV">CLICK!</button>

<div id="sections">
  <section class="panel" id="charactersPanel">
    <h3>Characters</h3>
    <div id="characters"></div>
  </section>

  <section class="panel" id="logPanel">
    <h3>Event log</h3>
    <div id="log" class="small">Welcome! Click to gain LV and buy characters for passive LV/sec.</div>
  </section>
</div>

<script>
(function() {
  // --- State ---
  let state = {
    lv: 0,
    clicks: 0,
    characters: [
      { id: "classic-sans", name: "Classic Sans", cost: 10, cps: 1, owned: 0 },
      { id: "papyrus-cool", name: "Papyrus (Cool Dude)", cost: 50, cps: 5, owned: 0 },
      { id: "underswap-sans", name: "Underswap Sans", cost: 200, cps: 20, owned: 0 },
      { id: "dust-sans", name: "Dust Sans", cost: 1000, cps: 100, owned: 0 }
    ]
  };

  // --- DOM refs ---
  const lvSpan = document.getElementById("lv");
  const clicksSpan = document.getElementById("clicks");
  const lvsSpan = document.getElementById("lvs");
  const clickBtn = document.getElementById("clickBtn");
  const charsDiv = document.getElementById("characters");
  const logDiv = document.getElementById("log");

  // --- Utils ---
  const fmt = (n) => {
    if (n < 1000) return String(n);
    const units = ["k","M","B","T","Q"];
    let u = -1, v = n;
    while (v >= 1000 && u < units.length - 1) { v /= 1000; u++; }
    return v.toFixed(v >= 100 ? 0 : v >= 10 ? 1 : 2) + units[u];
  };
  const log = (msg) => { logDiv.textContent = msg; };

  // --- Core render ---
  function computeLVps() {
    return state.characters.reduce((sum, c) => sum + c.cps * c.owned, 0);
  }

  function updateStats() {
    lvSpan.textContent = fmt(state.lv);
    clicksSpan.textContent = fmt(state.clicks);
    lvsSpan.textContent = fmt(computeLVps());
  }

  function renderCharacters() {
    charsDiv.innerHTML = "";
    state.characters.forEach((c, idx) => {
      const wrap = document.createElement("div");
      wrap.className = "character";
      const canAfford = state.lv >= c.cost;

      wrap.innerHTML = `
        <div class="row">
          <div>
            <strong>${c.name}</strong><br />
            <span class="meta">Gives ${fmt(c.cps)} LV/sec</span>
          </div>
          <div class="right">
            <div class="meta">Owned: ${c.owned}</div>
            <button class="buy" data-idx="${idx}">${canAfford ? "Buy" : "Buy"}</button>
            <div class="small">Cost: ${fmt(c.cost)} LV</div>
          </div>
        </div>
      `;

      const btn = wrap.querySelector("button.buy");
      btn.disabled = !canAfford;
      btn.addEventListener("click", () => buyCharacter(idx));
      charsDiv.appendChild(wrap);
    });
  }

  // --- Actions ---
  function clickOnce() {
    state.lv += 1;
    state.clicks += 1;
    updateStats();
    // Only re-render affordability states
    renderCharacters();
  }

  function buyCharacter(i) {
    const c = state.characters[i];
    if (!c) return;
    if (state.lv < c.cost) {
      log(`Not enough LV for ${c.name}. Need ${fmt(c.cost)} LV.`);
      return;
    }
    state.lv -= c.cost;
    c.owned += 1;
    // Scale cost progressively
    c.cost = Math.floor(c.cost * 1.5);
    updateStats();
    renderCharacters();
    log(`Purchased ${c.name}. LV/sec is now ${fmt(computeLVps())}.`);
  }

  function tickPassive() {
    const gain = computeLVps();
    if (gain > 0) {
      state.lv += gain;
      updateStats();
      // Re-render buttons affordability only, no layout thrash
      renderCharacters();
    }
  }

  // --- Init ---
  function init() {
    updateStats();
    renderCharacters();
    clickBtn.addEventListener("click", clickOnce);
    setInterval(tickPassive, 1000);
  }

  // Ensure DOM loaded before init
  if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", init);
  } else {
    init();
  }
})();
</script>

</body>
</html>
