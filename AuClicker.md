<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>AU Clicker</title>
</head>
<body>
  <h1>Multiversal AU Clicker</h1>
  <button id="clicker">Click me!</button>
  <p id="score">Energy: 0</p>

  <script>
    let energy = 0;
    const btn = document.getElementById('clicker');
    const score = document.getElementById('score');

    btn.addEventListener('click', ()=>{
      energy++;
      score.textContent = 'Energy: ' + energy;
    });
  </script>
</body>
</html>
