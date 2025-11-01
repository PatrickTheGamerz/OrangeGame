# MULTIVERSAL AU CLICKER

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>MULTIVERSAL AU CLICKER</title>
  <style>
    body{margin:0; font-family: ui-sans-serif, system-ui; background:#080b12; color:#e6e6e6; text-align:center; padding:40px}
    .energy{font-size:28px; margin:20px 0}
    .btn{padding:12px 20px; border-radius:12px; border:1px solid rgba(122,162,247,.28); background:#111423; color:#e6e6e6; cursor:pointer}
    .btn:hover{background:#14182a}
    .upgrades{margin-top:30px}
    .upgrade{margin:10px 0}
  </style>
</head>
<body>
  <h1>MULTIVERSAL AU CLICKER</h1>
  <div class="energy">AU Energy: <span id="energy">0</span></div>
  <button class="btn" id="clickBtn">Gather AU Energy</button>

  <div class="upgrades">
    <h2>Unlocks</h2>
    <div class="upgrade">
      <button class="btn" id="buyAuto">Buy Auto‑Clicker (cost 50)</button>
    </div>
    <div class="upgrade">
      <button class="btn" id="buyBoost">Buy Multiverse Boost (cost 200)</button>
    </div>
  </div>

  <p><a href="index.html" class="btn">← Back to Hub</a></p>

  <script>
    let energy = 0;
    let autoClickers = 0;
    let boost = 1;

    const energyEl = document.getElementById('energy');
    const clickBtn = document.getElementById('clickBtn');
    const buyAuto = document.getElementById('buyAuto');
    const buyBoost = document.getElementById('buyBoost');

    function updateDisplay(){
      energyEl.textContent = energy.toFixed(0);
    }

    clickBtn.addEventListener('click', ()=>{
      energy += 1 * boost;
      updateDisplay();
    });

    buyAuto.addEventListener('click', ()=>{
      if(energy >= 50){
        energy -= 50;
        autoClickers++;
        updateDisplay();
      }
    });

    buyBoost.addEventListener('click', ()=>{
      if(energy >= 200){
        energy -= 200;
        boost++;
        updateDisplay();
      }
    });

    setInterval(()=>{
      if(autoClickers > 0){
        energy += autoClickers * boost;
        updateDisplay();
      }
    }, 1000);

    updateDisplay();
  </script>
</body>
</html>
