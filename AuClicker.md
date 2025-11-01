<!-- AuClicker.md -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>MULTIVERSAL AU CLICKER</title>
  <style>
    body{
      margin:0; font-family: ui-sans-serif, system-ui;
      background:#0d0f18; color:#e6e6e6; text-align:center;
      display:flex; flex-direction:column; align-items:center; justify-content:center;
      height:100vh; overflow:hidden;
    }
    h1{
      font-size:28px;
      background:linear-gradient(90deg,#7aa2f7,#c28fff);
      -webkit-background-clip:text; color:transparent;
      margin-bottom:20px;
    }
    #energy{
      font-size:22px; margin:12px 0; color:#ffce63;
    }
    .btn{
      border:1px solid rgba(122,162,247,.28);
      background:linear-gradient(180deg,#111423,#0d1120);
      color:#e6e6e6; padding:14px 22px; border-radius:14px;
      cursor:pointer; font-size:16px;
      transition:transform .08s ease, box-shadow .15s ease;
      box-shadow:0 6px 16px rgba(122,162,247,.1);
    }
    .btn:hover{ transform:translateY(-2px); box-shadow:0 10px 24px rgba(122,162,247,.22) }
    #aus{ margin-top:20px; font-size:14px; color:#a7b0c0 }
  </style>
</head>
<body>
  <h1>MULTIVERSAL AU CLICKER</h1>
  <div id="energy">AU Energy: 0</div>
  <button class="btn" id="clicker">Gather AU Energy</button>
  <div id="aus">Unlocked AUs: None</div>

  <script>
    let energy=0;
    const energyEl=document.getElementById('energy');
    const ausEl=document.getElementById('aus');
    const btn=document.getElementById('clicker');

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
  </script>
</body>
</html>
