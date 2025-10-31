# Tic‑Tac‑Toe (X and O)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>X and O</title>
  <style>
    body { margin:0; font-family:sans-serif; background:#0f1115; color:#e6e6e6; display:grid; place-items:center; height:100vh; }
    .board { display:grid; grid-template-columns:repeat(3,80px); grid-template-rows:repeat(3,80px); gap:8px; }
    .cell { width:80px; height:80px; display:grid; place-items:center; font-size:34px; font-weight:bold; border:1px solid #232638; border-radius:10px; background:#151930; cursor:pointer; }
    .cell.win { outline:2px solid #73d13d; }
    .bar { margin-top:16px; display:flex; gap:8px; }
    .btn { border:1px solid #232638; background:#111423; color:#e6e6e6; padding:8px 12px; border-radius:8px; cursor:pointer; }
    .status { margin:12px 0; }
  </style>
</head>
<body>
  <h2>X and O</h2>
  <div class="status" id="status">X to move</div>
  <div class="board" id="board"></div>
  <div class="bar">
    <button class="btn" id="reset">Reset</button>
    <button class="btn" id="back">Back</button>
  </div>
  <div class="status">
    X wins: <span id="scoreX">0</span> |
    O wins: <span id="scoreO">0</span> |
    Draws: <span id="scoreD">0</span>
  </div>

  <script>
    const boardEl = document.getElementById('board');
    const statusEl = document.getElementById('status');
    let state, scores = { X:0, O:0, D:0 };

    function initGame() {
      state = { grid:Array(9).fill(null), turn:'X', winner:null, moves:0 };
      boardEl.innerHTML='';
      for(let i=0;i<9;i++){
        const cell=document.createElement('button');
        cell.className='cell';
        cell.dataset.idx=i;
        cell.addEventListener('click', onMove);
        boardEl.appendChild(cell);
      }
      updateStatus();
    }
    function onMove(e){
      if(state.winner) return;
      const i=+e.currentTarget.dataset.idx;
      if(state.grid[i]) return;
      state.grid[i]=state.turn;
      state.moves++;
      e.currentTarget.textContent=state.turn;
      const win=checkWin(state.grid);
      if(win){ state.winner=state.turn; highlightWin(win.line); statusEl.textContent=`${state.turn} wins`; scores[state.turn]++; renderScores(); return; }
      if(state.moves===9){ statusEl.textContent='Draw'; scores.D++; renderScores(); return; }
      state.turn=state.turn==='X'?'O':'X'; updateStatus();
    }
    function updateStatus(){ statusEl.textContent=`${state.turn} to move`; }
    function highlightWin(line){ line.forEach(i=>boardEl.children[i].classList.add('win')); }
    function renderScores(){ document.getElementById('scoreX').textContent=scores.X; document.getElementById('scoreO').textContent=scores.O; document.getElementById('scoreD').textContent=scores.D; }
    function checkWin(g){ const lines=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]]; for(const line of lines){const[a,b,c]=line;if(g[a]&&g[a]===g[b]&&g[a]===g[c]) return {player:g[a],line};} return null; }
    document.getElementById('reset').addEventListener('click',initGame);
    document.getElementById('back').addEventListener('click',()=>window.location.href="index.html");
    initGame(); renderScores();
  </script>
</body>
</html>

