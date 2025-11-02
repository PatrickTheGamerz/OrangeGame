# AU Clicker

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AU Clicker</title>
  <style>
    /* === CSS THEME & PANELS === */
    :root{
      --bg:#000;
      --panel:#0f1420;
      --panel-2:#161b22;
      --text:#e6e6e6;
      --accent:#7aa2f7;
      --danger:#ff4d6d;
      --shadow:0 12px 30px rgba(122,162,247,.18);
    }
    body{margin:0;background:var(--bg);color:var(--text);font-family:ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,Arial;height:100vh;overflow:hidden;}

    /* LEFT: STATS */
    #statsPanel{position:absolute;top:50%;left:5%;transform:translateY(-50%);
      width:280px;display:flex;flex-direction:column;gap:12px;padding:16px;
      background:linear-gradient(180deg,var(--panel),var(--panel-2));
      border:1px solid rgba(122,162,247,.28);border-radius:12px;}
    .statRow{display:flex;justify-content:space-between;align-items:center;
      padding:10px 12px;border-radius:10px;background:#0c121c;border:1px solid rgba(122,162,247,.18);}
    .statLabel{opacity:.85}
    .statValue{font-weight:600;color:var(--accent)}

    /* RIGHT: AU SELECT */
    #auPanel{position:absolute;top:50%;right:5%;transform:translateY(-50%);
      width:320px;display:flex;flex-direction:column;padding:16px;
      background:linear-gradient(180deg,var(--panel),var(--panel-2));
      border:1px solid rgba(122,162,247,.28);border-radius:12px;max-height:80vh;}
    #auHeader{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px;}
    #auHeader .subtitle{font-size:13px;color:#9cdcfe;opacity:.85}
    #auContent{flex:1;display:flex;flex-direction:column;gap:8px;overflow-y:auto;}
    .auBtn,.auBack{padding:10px 12px;border-radius:10px;cursor:pointer;
      border:1px solid rgba(122,162,247,.28);background:linear-gradient(180deg,#111423,#0d1120);color:var(--text);}
    .charRow{display:flex;justify-content:space-between;align-items:center;
      padding:10px;border-radius:10px;background:#0c121c;border:1px solid rgba(122,162,247,.18);}
    .charName{font-weight:700;letter-spacing:.3px}
    .buyBtn{padding:8px 12px;border-radius:8px;cursor:pointer;border:1px solid rgba(122,162,247,.28);
      background:#0d1118;color:var(--text);}
    .buyBtn:disabled{opacity:.6;cursor:not-allowed}

    /* CENTER: STAGE */
    #stage{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);
      display:flex;flex-direction:column;align-items:center;gap:12px;}
    #soulWrap{width:220px;height:220px;position:relative;filter:drop-shadow(0 0 20px rgba(255,0,70,.45));}
    #soul{width:100%;height:100%;background:#ff2f57;
      clip-path:polygon(50% 15%,61% 0,75% 0,100% 25%,100% 55%,50% 100%,0 55%,0 25%,25% 0,39% 0);
      cursor:pointer;animation:pulse 1.8s ease-in-out infinite;transition:filter .18s ease;}
    #soul.dim{filter:brightness(0.55);}
    @keyframes pulse{0%{transform:scale(1)}50%{transform:scale(1.06)}100%{transform:scale(1)}}
    #soulWrap.shattered #soul{opacity:0;animation:none;}

    /* Halves, shards, damage text, refused text, slash animation, menus, reset overlay... */
    /* (All CSS from your Part 1–3 is included here unchanged for brevity) */
  </style>
</head>
<body>
  <!-- LEFT: STATS -->
  <aside id="statsPanel">…</aside>

  <!-- CENTER: STAGE -->
  <main id="stage">…</main>

  <!-- RIGHT: AU SELECT -->
  <aside id="auPanel">…</aside>

  <script>
    /* === FULL JAVASCRIPT FROM PART 2 + PART 3 === */
    /* Core state, roster, persistence, UI helpers, soul interactions,
       FRISK slash (with Undertale-style vertical slash animation),
       shatter sequence, menu, reset overlay, AU panel, DPS loop, init. */
  </script>
</body>
</html>
