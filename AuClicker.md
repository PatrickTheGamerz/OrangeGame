# AU Clicker

A simple incremental clicker game.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>AU Clicker</title>
  <style>
    body{font-family:sans-serif;background:#111;color:#eee;text-align:center;padding:40px}
    button{padding:12px 20px;font-size:16px;border-radius:8px;cursor:pointer}
    .stats{margin-top:20px}
  </style>
</head>
<body>
  <h1>AU Clicker</h1>
  <button id="clickBtn">Click me!</button>
  <div class="stats">
    <p>AU: <span id="au">0</span></p>
    <p>Clicks: <span id="clicks">0</span></p>
  </div>
  <script>
    let au=0, clicks=0;
    const auEl=document.getElementById('au');
    const clicksEl=document.getElementById('clicks');
    document.getElementById('clickBtn').addEventListener('click',()=>{
      clicks++; au++;
      auEl.textContent=au;
      clicksEl.textContent=clicks;
    });
  </script>
</body>
</html>
