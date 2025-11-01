<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>AU Clicker</title>
<style>
  :root{
    --bg:#0d1117;
    --panel:#0f1420;
    --panel-2:#161b22;
    --text:#e6e6e6;
    --accent:#7aa2f7;
    --accent-2:#9cdcfe;
    --danger:#ff4d6d;
    --success:#59ffa0;
    --shadow:0 12px 30px rgba(122,162,247,.18);
  }
  *{box-sizing:border-box}
  body{
    margin:0;
    background:radial-gradient(1200px 600px at 50% 50%, #0f1420 20%, #0d1117 68%);
    color:var(--text);
    font-family:ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
    height:100vh;
    display:grid;
    grid-template-columns: 280px 1fr 320px;
    grid-template-rows: 100%;
  }

  /* Loader overlay */
  #loading{
    position:fixed; inset:0;
    display:grid; place-items:center;
    background:#0d1117;
    z-index:9999;
  }
  #loaderBox{
    width:400px; max-width:90vw;
    padding:20px; border-radius:12px;
    background:linear-gradient(180deg,#121826,#0e1322);
    border:1px solid rgba(122,162,247,.28);
    box-shadow:var(--shadow);
    text-align:center;
  }
  #progressBar{
    width:100%; height:14px;
    background:#0c121c;
    border-radius:8px; overflow:hidden;
    margin-top:12px;
  }
  #progressBar div{
    height:100%; width:0%;
    background:var(--accent);
    transition:width .2s ease;
  }
  #progressLabel{margin-top:8px; font-size:14px; opacity:.85}

  /* LEFT: STATS */
  #statsPanel{
    grid-column:1;
    display:flex;
    flex-direction:column;
    gap:12px;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border-right:1px solid rgba(122,162,247,.28);
  }
  #statsPanel h2{margin:0 0 8px; font-size:18px; letter-spacing:.6px}
  .statRow{
    display:flex; justify-content:space-between; align-items:center;
    padding:10px 12px; border-radius:10px; background:#0c121c;
    border:1px solid rgba(122,162,247,.18);
  }
  .statLabel{opacity:.8}
  .statValue{font-weight:600; color:var(--accent)}

  /* CENTER: STAGE */
  #stage{
    grid-column:2;
    position:relative;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
  }
  #soulWrap{
    position:relative;
    width:220px; height:220px;
    filter:drop-shadow(0 0 20px rgba(255,0,70,.45));
    transition:transform .12s ease;
  }
  #soul{
    width:100%; height:100%;
    background: radial-gradient(circle at 50% 35%, #ff6a85 0%, #ff2f57 40%, #d3133f 70%);
    clip-path: polygon(50% 18%, 63% 3%, 78% 3%, 100% 27%, 100% 55%, 50% 100%, 0 55%, 0 27%, 22% 3%, 37% 3%);
    cursor:pointer;
    animation:pulse 1.8s ease-in-out infinite;
    border:2px solid rgba(255,255,255,.06);
  }
  #soulWrap:active{transform:scale(0.96)}
  @keyframes pulse{
    0%{transform:scale(1)}
    50%{transform:scale(1.06)}
    100%{transform:scale(1)}
  }
  .spark{
    position:absolute; left:50%; top:50%;
    width:10px; height:10px; border-radius:50%;
    background:rgba(255,255,255,.85);
    transform:translate(-50%,-50%);
    animation:sparkUp .5s ease forwards;
  }
  @keyframes sparkUp{
    0%{opacity:1; transform:translate(-50%,-50%) scale(1)}
    100%{opacity:0; transform:translate(-50%,-110%) scale(0.2)}
  }

  /* Menu under SOUL */
  #menuBtn{
    margin-top:18px;
    padding:10px 20px;
    border-radius:10px;
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:var(--text); cursor:pointer;
    box-shadow:var(--shadow);
    transition:transform .08s ease, box-shadow .2s ease;
  }
  #menuBtn:hover{transform:translateY(-1px); box-shadow:0 14px 36px rgba(122,162,247,.28)}
  #menuTray{
    margin-top:12px;
    display:none;
    gap:8px; padding:12px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border:1px solid rgba(122,162,247,.28);
    border-radius:12px;
    flex-direction:column;
  }
  .trayBtn{
    padding:8px 12px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.22);
    background:#0d1118; color:var(--text);
    transition:transform .08s ease, box-shadow .15s ease;
  }
  .trayBtn:hover{transform:translateY(-1px); box-shadow:0 10px 24px rgba(122,162,247,.22)}

  /* Reset overlay */
  #resetOverlay{
    position:absolute; inset:0; display:none;
    align-items:center; justify-content:center;
    background:rgba(0,0,0,.35);
    backdrop-filter: blur(2px);
  }
  #resetCard{
    width:380px; max-width:95vw;
    padding:16px; border-radius:12px;
    background:linear-gradient(180deg,#121826,#0e1322);
    border:1px solid rgba(122,162,247,.28);
    box-shadow:var(--shadow);
  }
  #resetCard h3{margin:0 0 10px}
  #resetCard .desc{opacity:.85; margin-bottom:12px}
  .row{display:flex; gap:8px; justify-content:flex-end;}
  .btn{
    padding:10px 14px; border-radius:8px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:#0d1118; color:var(--text);
  }
  .btn.primary{background:#12203a}
  .btn.danger{background:#2a0f18; border-color:#ff4d6d}
  .btn.danger:hover{background:#3a1320}

  /* RIGHT: AU SELECT */
  #auPanel{
    grid-column:3;
    display:flex; flex-direction:column;
    padding:16px;
    background:linear-gradient(180deg, var(--panel), var(--panel-2));
    border-left:1px solid rgba(122,162,247,.28);
  }
  #auPanel h2{margin:0 0 8px; font-size:18px; letter-spacing:.6px}
  #auHeader{display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;}
  #auHeader .subtitle{font-size:13px; color:var(--accent-2); opacity:.85}
  #auContent{flex:1; display:flex; flex-direction:column; gap:8px}
  .auBtn, .auBack{
    padding:10px 12px; border-radius:10px; cursor:pointer;
    border:1px solid rgba(122,162,247,.28);
    background:linear-gradient(180deg,#111423,#0d1120);
    color:var(--text);
    transition:transform .08s ease, box-shadow .15
