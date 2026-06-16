
# Sudoku CSP Solver

An AI-powered Sudoku project built for **AI2002 — Artificial Intelligence** at **FAST-NUCES Chiniot-Faisalabad**.

Includes:
- A **Python CLI solver** using Constraint Satisfaction Problem (CSP) techniques
- A **playable web UI** where you can solve puzzles yourself — or let the AI solve them for you

---

## Gameplay

https://github.com/user-attachments/assets/f3d50f09-a848-4e67-906e-60493aad039f[sudoku_index.html](https://github.com/user-attachments/files/28994250/sudoku_index.html)


## Play It Online

Open `index.ht<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Sudoku — CSP Solver</title>
  <style>
    :root {
      --bg:        #0f1117;
      --surface:   #1a1d27;
      --border:    #2e3246;
      --accent:    #7c6af7;
      --accent2:   #a78bfa;
      --green:     #34d399;
      --red:       #f87171;
      --yellow:    #fbbf24;
      --text:      #e2e8f0;
      --muted:     #64748b;
      --given:     #c4b5fd;
      --cell-bg:   #1e2133;
      --cell-hover:#252840;
      --selected:  #2d2060;
      --highlight: #1e284a;
      --box-border:#7c6af7;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'Segoe UI', system-ui, sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 24px 16px;
    }

    header {
      text-align: center;
      margin-bottom: 28px;
    }
    header h1 {
      font-size: 2rem;
      font-weight: 800;
      letter-spacing: -1px;
      background: linear-gradient(135deg, var(--accent2), var(--green));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
    header p {
      color: var(--muted);
      font-size: 0.85rem;
      margin-top: 4px;
    }

    .layout {
      display: flex;
      gap: 28px;
      align-items: flex-start;
      flex-wrap: wrap;
      justify-content: center;
    }

    /* ── BOARD ── */
    .board-wrap {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 14px;
    }

    #board {
      display: grid;
      grid-template-columns: repeat(9, 1fr);
      border: 2px solid var(--box-border);
      border-radius: 8px;
      overflow: hidden;
      width: min(450px, 92vw);
      height: min(450px, 92vw);
      box-shadow: 0 0 40px rgba(124,106,247,0.15);
    }

    .cell {
      background: var(--cell-bg);
      border: 1px solid var(--border);
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      font-size: clamp(14px, 3vw, 22px);
      font-weight: 600;
      transition: background 0.1s;
      position: relative;
      user-select: none;
      color: var(--accent2);
    }

    /* 3×3 box borders */
    .cell:nth-child(3n) { border-right: 2px solid var(--box-border); }
    .cell:nth-child(9n) { border-right: 1px solid var(--border); }
    .cell[data-row="2"] { border-bottom: 2px solid var(--box-border); }
    .cell[data-row="5"] { border-bottom: 2px solid var(--box-border); }

    .cell:hover:not(.given):not(.solving) { background: var(--cell-hover); }
    .cell.selected  { background: var(--selected) !important; }
    .cell.highlight { background: var(--highlight); }
    .cell.given     { color: var(--given); cursor: default; font-weight: 700; }
    .cell.user-input{ color: var(--text); }
    .cell.error     { color: var(--red) !important; background: rgba(248,113,113,0.1) !important; }
    .cell.solved-anim { color: var(--green) !important; animation: pop 0.3s ease; }

    @keyframes pop {
      0%   { transform: scale(0.7); opacity: 0; }
      60%  { transform: scale(1.1); }
      100% { transform: scale(1);   opacity: 1; }
    }

    /* ── NUMPAD ── */
    .numpad {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 6px;
      width: min(450px, 92vw);
    }
    .numpad button {
      background: var(--surface);
      border: 1px solid var(--border);
      color: var(--text);
      font-size: 1.1rem;
      font-weight: 600;
      padding: 10px 0;
      border-radius: 6px;
      cursor: pointer;
      transition: background 0.15s, border-color 0.15s;
    }
    .numpad button:hover { background: var(--cell-hover); border-color: var(--accent); }
    .numpad button.erase { color: var(--red); font-size: 0.8rem; }

    /* ── SIDE PANEL ── */
    .panel {
      display: flex;
      flex-direction: column;
      gap: 14px;
      width: 220px;
    }

    .card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 10px;
      padding: 16px;
    }
    .card h3 {
      font-size: 0.75rem;
      text-transform: uppercase;
      letter-spacing: 1px;
      color: var(--muted);
      margin-bottom: 12px;
    }

    /* difficulty */
    .diff-btns { display: flex; flex-direction: column; gap: 6px; }
    .diff-btn {
      background: transparent;
      border: 1px solid var(--border);
      color: var(--text);
      padding: 8px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 0.85rem;
      text-align: left;
      transition: all 0.15s;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .diff-btn:hover { border-color: var(--accent); background: var(--cell-hover); }
    .diff-btn.active { border-color: var(--accent); background: rgba(124,106,247,0.15); color: var(--accent2); }
    .diff-dot { width: 8px; height: 8px; border-radius: 50%; }
    .d-easy   { background: var(--green); }
    .d-medium { background: var(--yellow); }
    .d-hard   { background: #fb923c; }
    .d-vhard  { background: var(--red); }

    /* action buttons */
    .action-btns { display: flex; flex-direction: column; gap: 8px; }
    .btn {
      padding: 10px;
      border-radius: 7px;
      border: none;
      font-size: 0.88rem;
      font-weight: 600;
      cursor: pointer;
      transition: opacity 0.15s, transform 0.1s;
      letter-spacing: 0.3px;
    }
    .btn:active { transform: scale(0.97); }
    .btn-primary { background: var(--accent); color: #fff; }
    .btn-primary:hover { opacity: 0.88; }
    .btn-outline { background: transparent; border: 1px solid var(--border); color: var(--text); }
    .btn-outline:hover { border-color: var(--accent); }
    .btn-danger  { background: transparent; border: 1px solid var(--red); color: var(--red); }
    .btn-danger:hover { background: rgba(248,113,113,0.1); }

    /* stats */
    .stat-row { display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 0.82rem; }
    .stat-row:last-child { margin-bottom: 0; }
    .stat-label { color: var(--muted); }
    .stat-val   { color: var(--text); font-weight: 600; }

    /* status banner */
    #status {
      padding: 10px 14px;
      border-radius: 7px;
      font-size: 0.82rem;
      font-weight: 500;
      display: none;
      border: 1px solid transparent;
    }
    #status.info    { display:block; background:rgba(124,106,247,.12); border-color:var(--accent);  color:var(--accent2); }
    #status.success { display:block; background:rgba(52,211,153,.12);  border-color:var(--green);   color:var(--green); }
    #status.error   { display:block; background:rgba(248,113,113,.12); border-color:var(--red);     color:var(--red); }

    /* timer */
    #timer { font-size: 1.5rem; font-weight: 700; font-variant-numeric: tabular-nums; color: var(--accent2); }

    @media (max-width: 600px) {
      .panel { width: min(450px, 92vw); }
      .layout { flex-direction: column; }
    }
  </style>
</head>
<body>

<header>
  <h1>♟ Sudoku CSP</h1>
  <p>Play it yourself — or let the AI solver take over</p>
</header>

<div class="layout">

  <!-- Board + numpad -->
  <div class="board-wrap">
    <div id="board"></div>
    <div class="numpad" id="numpad"></div>
    <div id="status"></div>
  </div>

  <!-- Side panel -->
  <div class="panel">

    <div class="card">
      <h3>Difficulty</h3>
      <div class="diff-btns">
        <button class="diff-btn active" data-diff="easy">
          Easy <span class="diff-dot d-easy"></span>
        </button>
        <button class="diff-btn" data-diff="medium">
          Medium <span class="diff-dot d-medium"></span>
        </button>
        <button class="diff-btn" data-diff="hard">
          Hard <span class="diff-dot d-hard"></span>
        </button>
        <button class="diff-btn" data-diff="veryhard">
          Very Hard <span class="diff-dot d-vhard"></span>
        </button>
      </div>
    </div>

    <div class="card">
      <h3>Timer</h3>
      <div id="timer">00:00</div>
    </div>

    <div class="card">
      <h3>Actions</h3>
      <div class="action-btns">
        <button class="btn btn-primary" id="btnSolve">🤖 AI Solve</button>
        <button class="btn btn-outline"  id="btnCheck">✔ Check My Answer</button>
        <button class="btn btn-outline"  id="btnHint">💡 Get Hint</button>
        <button class="btn btn-danger"   id="btnReset">↺ Reset Board</button>
      </div>
    </div>

    <div class="card">
      <h3>Stats (last solve)</h3>
      <div class="stat-row"><span class="stat-label">Backtrack calls</span>   <span class="stat-val" id="stCalls">—</span></div>
      <div class="stat-row"><span class="stat-label">Backtrack failures</span><span class="stat-val" id="stFails">—</span></div>
      <div class="stat-row"><span class="stat-label">Technique</span>         <span class="stat-val">CSP + AC-3</span></div>
    </div>

  </div>
</div>

<script>
// ═══════════════════════════════════════════════
//  PUZZLES
// ═══════════════════════════════════════════════
const PUZZLES = {
  easy: [
    [0,0,4,0,3,0,0,5,0],
    [6,0,9,4,0,0,0,0,0],
    [0,0,5,1,0,0,4,8,9],
    [0,0,0,0,6,0,9,3,0],
    [3,0,0,8,0,7,0,0,2],
    [0,2,6,0,4,0,0,0,0],
    [4,5,3,0,0,9,6,0,0],
    [0,0,0,0,0,4,7,0,5],
    [0,9,0,0,5,0,2,0,0]
  ],
  medium: [
    [0,0,0,2,6,0,7,0,1],
    [6,8,0,0,7,0,0,9,0],
    [1,9,0,0,0,4,5,0,0],
    [8,2,0,1,0,0,0,4,0],
    [0,0,4,6,0,2,9,0,0],
    [0,5,0,0,0,3,0,2,8],
    [0,0,9,3,0,0,0,7,4],
    [0,4,0,0,5,0,0,3,6],
    [7,0,3,0,1,8,0,0,0]
  ],
  hard: [
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,3,0,8,5],
    [0,0,1,0,2,0,0,0,0],
    [0,0,0,5,0,7,0,0,0],
    [0,0,4,0,0,0,1,0,0],
    [0,9,0,0,0,0,0,0,0],
    [5,0,0,0,0,0,0,7,3],
    [0,0,2,0,1,0,0,0,0],
    [0,0,0,0,4,0,0,0,9]
  ],
  veryhard: [
    [1,0,0,0,0,7,0,9,0],
    [0,3,0,0,2,0,0,0,8],
    [0,0,9,6,0,0,5,0,0],
    [0,0,5,3,0,0,9,0,0],
    [0,1,0,0,8,0,0,0,2],
    [6,0,0,0,0,4,0,0,0],
    [3,0,0,0,0,0,0,1,0],
    [0,4,0,0,0,0,0,0,7],
    [0,0,7,0,0,0,3,0,0]
  ]
};

// ═══════════════════════════════════════════════
//  CSP SOLVER (JS port of sudoku_csp.py)
// ═══════════════════════════════════════════════

function getPeers(r, c) {
  const peers = new Set();
  for (let col = 0; col < 9; col++) if (col !== c) peers.add(r*9+col);
  for (let row = 0; row < 9; row++) if (row !== r) peers.add(row*9+c);
  const br = 3*Math.floor(r/3), bc = 3*Math.floor(c/3);
  for (let row = br; row < br+3; row++)
    for (let col = bc; col < bc+3; col++)
      if (row !== r || col !== c) peers.add(row*9+col);
  return [...peers];
}

const PEERS = {};
for (let r = 0; r < 9; r++)
  for (let c = 0; c < 9; c++)
    PEERS[r*9+c] = getPeers(r, c);

function initDomains(board) {
  const d = {};
  for (let r = 0; r < 9; r++) {
    for (let c = 0; c < 9; c++) {
      const k = r*9+c;
      if (board[r][c] !== 0) {
        d[k] = new Set([board[r][c]]);
      } else {
        const used = new Set(PEERS[k].map(pk => board[Math.floor(pk/9)][pk%9]).filter(v => v !== 0));
        d[k] = new Set([1,2,3,4,5,6,7,8,9].filter(v => !used.has(v)));
      }
    }
  }
  return d;
}

function cloneDomains(d) {
  const nd = {};
  for (const k in d) nd[k] = new Set(d[k]);
  return nd;
}

function revise(d, xi, xj) {
  let revised = false;
  if (d[xj].size === 1) {
    const [v] = d[xj];
    if (d[xi].has(v) && d[xi].size > 1) { d[xi].delete(v); revised = true; }
    else if (d[xi].has(v) && d[xi].size === 1) { d[xi].delete(v); revised = true; }
  }
  return revised;
}

function ac3(d) {
  const queue = [];
  for (const k in d) for (const p of PEERS[k]) queue.push([+k, p]);
  while (queue.length) {
    const [xi, xj] = queue.shift();
    if (revise(d, xi, xj)) {
      if (d[xi].size === 0) return false;
      for (const xk of PEERS[xi]) if (xk !== xj) queue.push([xk, xi]);
    }
  }
  return true;
}

function selectVar(d) {
  let best = null, bestLen = Infinity;
  for (const k in d) {
    const sz = d[k].size;
    if (sz > 1 && sz < bestLen) { bestLen = sz; best = +k; }
  }
  return best;
}

function orderValues(cell, d) {
  return [...d[cell]].sort((a, b) => {
    let ca = 0, cb = 0;
    for (const p of PEERS[cell]) {
      if (d[p].size > 1) { if (d[p].has(a)) ca++; if (d[p].has(b)) cb++; }
    }
    return ca - cb;
  });
}

function forwardCheck(cell, val, d) {
  const nd = cloneDomains(d);
  nd[cell] = new Set([val]);
  for (const p of PEERS[cell]) {
    if (nd[p].size > 1) {
      nd[p].delete(val);
      if (nd[p].size === 0) return [false, null];
    }
  }
  return [true, nd];
}

let btCalls = 0, btFails = 0;

function backtrack(d) {
  btCalls++;
  if (Object.values(d).every(s => s.size === 1)) return d;
  const cell = selectVar(d);
  if (cell === null) return null;
  for (const val of orderValues(cell, d)) {
    const [ok, nd] = forwardCheck(cell, val, d);
    if (ok) {
      const nd2 = cloneDomains(nd);
      if (ac3(nd2)) {
        const res = backtrack(nd2);
        if (res) return res;
      }
    }
  }
  btFails++;
  return null;
}

function solve(board) {
  btCalls = 0; btFails = 0;
  const d = initDomains(board);
  if (!ac3(d)) return null;
  const res = backtrack(d);
  if (!res) return null;
  const sol = Array.from({length:9}, (_,r) =>
    Array.from({length:9}, (_,c) => [...res[r*9+c]][0])
  );
  return sol;
}

// ═══════════════════════════════════════════════
//  UI STATE
// ═══════════════════════════════════════════════
let currentDiff   = 'easy';
let puzzle        = [];   // original puzzle (0 = empty)
let userBoard     = [];   // what user has filled
let solution      = null; // correct solution
let selectedCell  = null; // [r,c]
let timerInt      = null;
let elapsed       = 0;
let gameOver      = false;

// ═══════════════════════════════════════════════
//  RENDER BOARD
// ═══════════════════════════════════════════════
function renderBoard() {
  const board = document.getElementById('board');
  board.innerHTML = '';
  for (let r = 0; r < 9; r++) {
    for (let c = 0; c < 9; c++) {
      const cell = document.createElement('div');
      cell.className = 'cell';
      cell.dataset.row = r;
      cell.dataset.col = c;
      const val = userBoard[r][c];
      if (puzzle[r][c] !== 0) {
        cell.textContent = puzzle[r][c];
        cell.classList.add('given');
      } else if (val !== 0) {
        cell.textContent = val;
        cell.classList.add('user-input');
      }
      cell.addEventListener('click', () => selectCellUI(r, c));
      board.appendChild(cell);
    }
  }
  if (selectedCell) highlightCell(...selectedCell);
}

function getCell(r, c) {
  return document.querySelector(`#board .cell[data-row="${r}"][data-col="${c}"]`);
}

function selectCellUI(r, c) {
  if (gameOver) return;
  if (puzzle[r][c] !== 0) return; // given cell
  selectedCell = [r, c];
  highlightCell(r, c);
}

function highlightCell(r, c) {
  document.querySelectorAll('#board .cell').forEach(el => {
    el.classList.remove('selected', 'highlight');
  });
  const val = userBoard[r][c] || puzzle[r][c];
  // highlight same row, col, box
  for (let i = 0; i < 9; i++) {
    getCell(r, i)?.classList.add('highlight');
    getCell(i, c)?.classList.add('highlight');
  }
  const br = 3*Math.floor(r/3), bc = 3*Math.floor(c/3);
  for (let dr = 0; dr < 3; dr++)
    for (let dc = 0; dc < 3; dc++)
      getCell(br+dr, bc+dc)?.classList.add('highlight');
  // highlight same number
  if (val) {
    for (let ri = 0; ri < 9; ri++)
      for (let ci = 0; ci < 9; ci++)
        if ((puzzle[ri][ci] === val || userBoard[ri][ci] === val))
          getCell(ri, ci)?.classList.add('highlight');
  }
  getCell(r, c)?.classList.add('selected');
}

// ═══════════════════════════════════════════════
//  INPUT
// ═══════════════════════════════════════════════
function enterNumber(n) {
  if (!selectedCell || gameOver) return;
  const [r, c] = selectedCell;
  if (puzzle[r][c] !== 0) return;
  userBoard[r][c] = n;
  const cell = getCell(r, c);
  cell.textContent = n || '';
  cell.classList.remove('error', 'solved-anim', 'user-input');
  if (n) cell.classList.add('user-input');
  highlightCell(r, c);
  checkWin();
}

function buildNumpad() {
  const np = document.getElementById('numpad');
  np.innerHTML = '';
  for (let i = 1; i <= 9; i++) {
    const btn = document.createElement('button');
    btn.textContent = i;
    btn.addEventListener('click', () => enterNumber(i));
    np.appendChild(btn);
  }
  const erase = document.createElement('button');
  erase.textContent = '⌫ Erase';
  erase.className = 'erase';
  erase.addEventListener('click', () => enterNumber(0));
  np.appendChild(erase);
}

document.addEventListener('keydown', e => {
  if (!selectedCell || gameOver) return;
  const key = parseInt(e.key);
  if (key >= 1 && key <= 9) { enterNumber(key); return; }
  if (e.key === 'Backspace' || e.key === 'Delete' || e.key === '0') { enterNumber(0); return; }
  // arrow navigation
  let [r, c] = selectedCell;
  if (e.key === 'ArrowRight') c = Math.min(8, c+1);
  else if (e.key === 'ArrowLeft')  c = Math.max(0, c-1);
  else if (e.key === 'ArrowDown')  r = Math.min(8, r+1);
  else if (e.key === 'ArrowUp')    r = Math.max(0, r-1);
  else return;
  e.preventDefault();
  selectCellUI(r, c);
});

// ═══════════════════════════════════════════════
//  TIMER
// ═══════════════════════════════════════════════
function startTimer() {
  clearInterval(timerInt);
  elapsed = 0;
  updateTimerDisplay();
  timerInt = setInterval(() => { elapsed++; updateTimerDisplay(); }, 1000);
}
function stopTimer() { clearInterval(timerInt); }
function updateTimerDisplay() {
  const m = String(Math.floor(elapsed/60)).padStart(2,'0');
  const s = String(elapsed%60).padStart(2,'0');
  document.getElementById('timer').textContent = m+':'+s;
}

// ═══════════════════════════════════════════════
//  LOAD PUZZLE
// ═══════════════════════════════════════════════
function loadPuzzle(diff) {
  currentDiff = diff;
  puzzle = PUZZLES[diff].map(r => [...r]);
  userBoard = puzzle.map(r => [...r]);
  solution = solve(puzzle.map(r=>[...r]));
  selectedCell = null;
  gameOver = false;
  document.getElementById('stCalls').textContent = '—';
  document.getElementById('stFails').textContent = '—';
  setStatus('');
  renderBoard();
  buildNumpad();
  startTimer();
}

// ═══════════════════════════════════════════════
//  WIN CHECK
// ═══════════════════════════════════════════════
function checkWin() {
  for (let r = 0; r < 9; r++)
    for (let c = 0; c < 9; c++)
      if (userBoard[r][c] === 0) return;
  // All filled — validate
  if (validateBoard(userBoard)) {
    stopTimer();
    gameOver = true;
    setStatus('🎉 Congratulations! You solved it in ' + document.getElementById('timer').textContent + '!', 'success');
  }
}

function validateBoard(b) {
  const digits = new Set([1,2,3,4,5,6,7,8,9]);
  for (let r = 0; r < 9; r++) {
    if (JSON.stringify([...new Set(b[r])].sort()) !== JSON.stringify([1,2,3,4,5,6,7,8,9].map(String))) {
      // simpler check:
      const s = new Set(b[r]);
      if (s.size !== 9 || !([...s].every(v => digits.has(v)))) return false;
    }
    const col = new Set(); const ok_col = new Set([1,2,3,4,5,6,7,8,9]);
    for (let i = 0; i < 9; i++) col.add(b[i][r]);
    if (col.size !== 9) return false;
  }
  for (let br = 0; br < 3; br++) for (let bc = 0; bc < 3; bc++) {
    const box = new Set();
    for (let dr = 0; dr < 3; dr++) for (let dc = 0; dc < 3; dc++) box.add(b[br*3+dr][bc*3+dc]);
    if (box.size !== 9) return false;
  }
  return true;
}

// ═══════════════════════════════════════════════
//  STATUS
// ═══════════════════════════════════════════════
function setStatus(msg, type='') {
  const el = document.getElementById('status');
  el.textContent = msg;
  el.className = type || '';
  if (!type) el.style.display = 'none';
}

// ═══════════════════════════════════════════════
//  BUTTONS
// ═══════════════════════════════════════════════
document.getElementById('btnSolve').addEventListener('click', () => {
  if (!solution) { setStatus('No solution found for this puzzle.', 'error'); return; }
  stopTimer();
  gameOver = true;
  userBoard = solution.map(r => [...r]);

  // Animate solution cells
  let delay = 0;
  for (let r = 0; r < 9; r++) {
    for (let c = 0; c < 9; c++) {
      if (puzzle[r][c] === 0) {
        const cell = getCell(r, c);
        setTimeout(() => {
          cell.textContent = solution[r][c];
          cell.classList.remove('user-input','error');
          cell.classList.add('solved-anim');
        }, delay);
        delay += 18;
      }
    }
  }
  document.getElementById('stCalls').textContent = btCalls;
  document.getElementById('stFails').textContent = btFails;
  setTimeout(() => setStatus('✅ Solved by AI using AC-3 + Backtracking CSP!', 'success'), delay+100);
});

document.getElementById('btnCheck').addEventListener('click', () => {
  let errors = 0;
  for (let r = 0; r < 9; r++) {
    for (let c = 0; c < 9; c++) {
      const cell = getCell(r, c);
      if (puzzle[r][c] === 0 && userBoard[r][c] !== 0) {
        cell.classList.remove('error');
        if (solution && userBoard[r][c] !== solution[r][c]) {
          cell.classList.add('error');
          errors++;
        }
      }
    }
  }
  if (errors === 0) setStatus('✅ All filled cells are correct! Keep going!', 'success');
  else setStatus(`⚠️ Found ${errors} mistake${errors>1?'s':''}. Wrong cells are highlighted in red.`, 'error');
});

document.getElementById('btnHint').addEventListener('click', () => {
  if (!solution || gameOver) return;
  // find first empty cell
  for (let r = 0; r < 9; r++) {
    for (let c = 0; c < 9; c++) {
      if (puzzle[r][c] === 0 && userBoard[r][c] === 0) {
        userBoard[r][c] = solution[r][c];
        const cell = getCell(r, c);
        cell.textContent = solution[r][c];
        cell.classList.add('solved-anim');
        selectCellUI(r, c);
        setStatus(`💡 Hint: Row ${r+1}, Col ${c+1} = ${solution[r][c]}`, 'info');
        checkWin();
        return;
      }
    }
  }
  setStatus('No empty cells left!', 'info');
});

document.getElementById('btnReset').addEventListener('click', () => {
  userBoard = puzzle.map(r => [...r]);
  selectedCell = null;
  gameOver = false;
  setStatus('');
  renderBoard();
  startTimer();
});

// Difficulty buttons
document.querySelectorAll('.diff-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    loadPuzzle(btn.dataset.diff);
  });
});

// ═══════════════════════════════════════════════
//  INIT
// ═══════════════════════════════════════════════
loadPuzzle('easy');
</script>
</body>
</html>
ml` in any browser — no installation needed.

**Features:**
- 4 difficulty levels: Easy, Medium, Hard, Very Hard
- Type numbers into cells yourself to play
- **Check** your answers for mistakes
- **Hint** button to reveal one cell
- **AI Solve** — watch the CSP solver fill the board with animation
- Live timer
- Arrow key + keyboard number input support

---

## AI Techniques Used

| Technique | Description |
|-----------|-------------|
| **AC-3** | Arc Consistency Algorithm 3 — reduces domains before search |
| **Backtracking Search** | Recursive search with pruning |
| **MRV Heuristic** | Minimum Remaining Values — picks the most constrained cell first |
| **LCV Heuristic** | Least Constraining Value — orders values to minimize conflicts |
| **Forward Checking** | Eliminates invalid values from peers after each assignment |

---

## Python CLI Solver

### Requirements
```bash
pip install -r requirements.txt
```
*(Only standard library used — no packages needed)*

### Run
```bash
python sudoku_csp.py
```

Solves all 4 puzzles (easy → very hard) and prints stats:
```
Backtrack calls   : 1
Backtrack failures: 0
Comment: Very few backtracks — AC-3 + MRV solved it almost directly.
```

### Puzzle Format
Each `.txt` file is a 9×9 grid where `0` = empty cell:
```
004030050
609400000
005100489
...
```

---

## License

MIT License
