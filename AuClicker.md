<!-- AuClicker.md -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>MULTIVERSAL AU CLICKER</title>
  <style>
    :root{
      --bg:#0d0f18; --text:#e6e6e6; --muted:#a7b0c0;
      --accent:#7aa2f7; --accent2:#c28fff; --gold:#ffce63;
    }
    *{box-sizing:border-box}
    body{
      margin:0; height:100vh;
      display:flex; flex-direction:column; align-items:center; justify-content:center;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      background: radial-gradient(800px 400px at 50% 30%, #1a1f35, var(--bg));
      color:var(--text); text-align:center;
    }
    h1{
      font-size:28px; margin-bottom:20px;
      background:linear-gradient(90deg,var(--accent),var(--accent2));
      -webkit-background-clip:text; color:transparent;
    }
    #energy{
      font-size:22px; margin:12px 0; color:var(--gold);
    }
    .btn{
      border:1px solid rgba(122,162,247,.28);
      background:linear-gradient(180deg,#111423,#0d1120);
      color:var(--text); padding:14px 22px; border-radius:14px;
      cursor:pointer; font-size:16px;
      transition:transform .08s ease, box-shadow .15s ease;
      box-shadow:0 6px 16px rgba(122,162,247,.1);
    }
    .btn:hover{ transform:translateY(-2px); box-shadow:0 10px 24px rgba(122,162,247,.22) }
    #aus{ margin-top:20px; font-size:14px; color:var(--muted); max-width:480px }
    #back{ position:absolute; top:16px; left:16px }
  </style>
</head>
<body>
  <button class="btn" id="back">← Back to Hub</button>
  <h1>MULTIVERSAL AU CLICKER</h1>
  <div id="energy">AU Energy: 0</div>
  <button class="btn" id="clicker">Gather AU Energy</button>
  <div id="aus">Unlocked AUs: None</div>

  <script>
    let energy=0;
    const energyEl=document.getElementById('energy');
    const ausEl=document.getElementById('aus');
    const btn=document.getElementById('clicker');
    const back=document.getElementById('back');

    const unlocks=[
      {threshold:10, name:'Underfell'},
      {threshold:50, name:'Underswap'},
      {threshold:150, name:'Horrortale'},
      {threshold:500, name:'OuterTale'},
      {threshold:1000, name:'Multiversal Core'}
    ];
    let unlocked=[];

    btn.addEventListener('click',()=>{
      energy++;
      energyEl.textContent='AU Energy: '+energy;
      checkUnlocks();
    });

    function checkUnlocks(){
      for(const u of unlocks){
        if(energy>=u.threshold && !unlocked.includes(u.name)){
          unlocked.push(u.name);
          ausEl.textContent='Unlocked AUs: '+unlocked.join(', ');
        }
      }
    }

    back.addEventListener('click',()=>{
      window.location.href='index.html'; // adjust if your hub file has a different name
    });
  </script>
</body>
</html>
