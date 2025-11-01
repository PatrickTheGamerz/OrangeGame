<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>UNDERTALE AU Clicker (HTML Only)</title>
<style>
  body {
    background: black;
    color: white;
    font-family: "Courier New", monospace;
    text-align: center;
    margin: 0;
    padding: 20px;
  }
  h1 { margin-bottom: 10px; }
  #clickBtn {
    font-size: 24px;
    padding: 20px 40px;
    margin: 20px;
    cursor: pointer;
    background: white;
    color: black;
    border: 3px solid white;
  }
  #stats { margin: 20px 0; }
  .character {
    border: 1px solid white;
    margin: 10px auto;
    padding: 10px;
    width: 300px;
    background: #111;
  }
  button.buy {
    margin-top: 5px;
    padding: 5px 10px;
    cursor: pointer;
  }
</style>
</head>
<body>

<h1>UNDERTALE: AU CLICKER</h1>
<div id="stats">
  <p>LV: <span id="lv">0</span></p>
  <p>Total Clicks: <span id="clicks">0</span></p>
</div>

<button id="clickBtn">CLICK!</button>

<h2>Characters</h2>
<div id="characters"></div>

<script>
let lv = 0;
let clicks = 0;

const characters = [
  { name: "Classic Sans", cost: 10, cps: 1, owned: 0 },
  { name: "Papyrus (Cool Dude)", cost: 50, cps: 5, owned: 0 },
  { name: "Underswap Sans", cost: 200, cps: 20, owned: 0 },
  { name: "Dust Sans", cost: 1000, cps: 100, owned: 0 }
];

const lvSpan = document.getElementById("lv");
const clicksSpan = document.getElementById("clicks");
const charsDiv = document.getElementById("characters");

function updateStats() {
  lvSpan.textContent = lv;
  clicksSpan.textContent = clicks;
}

function renderCharacters() {
  charsDiv.innerHTML = "";
  characters.forEach((c, i) => {
    if (lv >= c.cost / 2) { // reveal early
      const div = document.createElement("div");
      div.className = "character";
      div.innerHTML = `
        <strong>${c.name}</strong><br>
        Cost: ${c.cost} LV<br>
        Gives: ${c.cps} LV/sec<br>
        Owned: ${c.owned}<br>
        <button class="buy">Buy</button>
      `;
      div.querySelector(".buy").onclick = () => buyCharacter(i);
      charsDiv.appendChild(div);
    }
  });
}

function buyCharacter(i) {
  const c = characters[i];
  if (lv >= c.cost) {
    lv -= c.cost;
    c.owned++;
    c.cost = Math.floor(c.cost * 1.5);
    updateStats();
    renderCharacters();
  }
}

document.getElementById("clickBtn").onclick = () => {
  lv++;
  clicks++;
  updateStats();
  renderCharacters();
};

// Passive LV gain
setInterval(() => {
  let gain = 0;
  characters.forEach(c => gain += c.cps * c.owned);
  if (gain > 0) {
    lv += gain;
    updateStats();
    renderCharacters();
  }
}, 1000);

updateStats();
renderCharacters();
</script>

</body>
</html>
