# rama-<!doctype html>
<html lang="id">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Mega Slot — 5 Reel Demo</title>
<style>
  :root{--bg:#071226;--card:#0f1724;--accent:#ffcc00;--muted:#9aa6b2}
  body{margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Arial;background:linear-gradient(180deg,#030816 0%, #071226 100%);color:#e6eef6;display:flex;align-items:center;justify-content:center;min-height:100vh}
  .app{width:1100px;max-width:96%;background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent);border-radius:14px;padding:20px;box-shadow:0 10px 30px rgba(2,6,23,0.6)}
  header{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px}
  .logo svg{width:180px;height:60px}
  h1{font-size:18px;margin:0}
  .meta{color:var(--muted);font-size:13px}
  .board{display:flex;gap:12px;align-items:center;justify-content:center;padding:14px;background:rgba(255,255,255,0.02);border-radius:12px}
  .reel{width:120px;height:180px;border-radius:10px;background:linear-gradient(180deg,#091426,#051424);display:flex;flex-direction:column;align-items:center;justify-content:center;box-shadow:inset 0 2px 6px rgba(0,0,0,0.6);overflow:hidden;position:relative}
  .slot-cell{font-size:44px;height:56px;display:flex;align-items:center;justify-content:center;width:100%}
  .spinning{animation:spin 0.5s linear infinite}
  @keyframes spin{0%{transform:translateY(0)}100%{transform:translateY(-56px)}}
  .controls{display:flex;gap:12px;align-items:center;margin-top:12px;flex-wrap:wrap}
  button{background:linear-gradient(180deg,var(--accent),#ff962f);border:none;padding:10px 16px;border-radius:10px;font-weight:700;color:#111;cursor:pointer;box-shadow:0 6px 18px rgba(255,140,50,0.18)}
  .small{background:#1b2633;padding:8px 10px;border-radius:8px;color:var(--muted);font-weight:600;border:1px solid rgba(255,255,255,0.03)}
  .status{background:var(--card);padding:12px;border-radius:10px;min-width:220px;text-align:center}
  input[type=range]{accent-color:var(--accent);width:160px}
</style>
</head>
<body>
  <div class="app">
    <header>
      <div class="logo">
        <svg viewBox="0 0 340 90" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
          <defs>
            <linearGradient id="gold" x1="0" x2="1">
              <stop offset="0" stop-color="#ffd700"/>
              <stop offset="1" stop-color="#ff3300"/>
            </linearGradient>
          </defs>
          <rect x="6" y="6" width="72" height="72" rx="14" fill="#0b1220" stroke="url(#gold)" stroke-width="4"/>
          <text x="42" y="54" font-family="Verdana,Segoe UI,Roboto" font-size="44" text-anchor="middle" fill="url(#gold)">🎰</text>
          <text x="96" y="52" font-family="Verdana,Segoe UI,Roboto" font-size="32" fill="#ffeebb" font-weight="700">Mega Slot</text>
        </svg>
      </div>
      <div class="meta">Demo — bukan untuk permainan uang nyata</div>
    </header>

    <div class="board" id="reels"></div>

    <div class="controls">
      <div class="status">
        <div>Saldo: <strong id="credits">2000</strong></div>
        <div class="meta" style="font-size:12px;margin-top:6px">Taruhan: <span id="betAmount">20</span></div>
      </div>
      <div>
        <label for="bet">Taruhan</label>
        <input id="bet" type="range" min="1" max="500" value="20" />
      </div>
      <button id="spin">SPIN</button>
      <button id="reset" class="small">Reset Saldo</button>
    </div>
  </div>

<script>
const SYMBOLS = ["⭐","🍒","🍋","🍊","🍉","💎","W","S"];
const MULT = {"⭐":{3:100,4:400,5:1000},"🍒":{3:60,4:200,5:600},"🍋":{3:40,4:120,5:300},"🍊":{3:30,4:100,5:250},"🍉":{3:20,4:60,5:150},"💎":{3:150,4:600,5:2000}};
let credits=2000,bet=20;const reels=document.getElementById('reels');
for(let r=0;r<5;r++){const reel=document.createElement('div');reel.className='reel';for(let c=0;c<3;c++){const cell=document.createElement('div');cell.className='slot-cell';cell.id=`r${r}c${c}`;cell.textContent=SYMBOLS[Math.floor(Math.random()*SYMBOLS.length)];reel.appendChild(cell);}reels.appendChild(reel);}

function randInt(n){const a=new Uint32Array(1);crypto.getRandomValues(a);return a[0]%n;}

async function spinOnce(){if(credits<bet){alert('Saldo habis');return;}credits-=bet;updateUI();
// animasi spin
const reelEls=document.querySelectorAll('.reel');
reelEls.forEach(r=>r.classList.add('spinning'));
await new Promise(res=>setTimeout(res,1000));
reelEls.forEach(r=>r.classList.remove('spinning'));

const final=Array.from({length:5},()=>[randInt(SYMBOLS.length),randInt(SYMBOLS.length),randInt(SYMBOLS.length)]);
for(let r=0;r<5;r++){for(let c=0;c<3;c++){document.getElementById(`r${r}c${c}`).textContent=SYMBOLS[final[r][c]];}}

// Scatter check
let scatters=0;for(let r=0;r<5;r++){for(let c=0;c<3;c++){if(SYMBOLS[final[r][c]]==="S")scatters++;}}
if(scatters>=3){let fs=10;if(scatters===4)fs=15;if(scatters>=5)fs=20;freeSpins+=fs;alert(`Scatter! Dapat ${fs} Free Spins`);}

// Build grid
const grid=[[],[],[]];for(let r=0;r<5;r++){for(let c=0;c<3;c++){grid[c][r]=document.getElementById(`r${r}c${c}`).textContent;}}
let win=evaluate(grid);if(win>0){credits+=win;}
updateUI();}

let freeSpins=0;
function evaluate(grid){let payout=0;for(let row=0;row<3;row++){let symbol=grid[row][0];let count=1;let multiplier=1;for(let col=1;col<5;col++){if(grid[row][col]===symbol||grid[row][col]==="W"){if(grid[row][col]==="W")multiplier*=2;count++;}else break;}if(count>=3&&MULT[symbol]){payout+=MULT[symbol][count]*bet*multiplier;}}
// expanding wild check
for(let r=0;r<5;r++){for(let c=0;c<3;c++){if(grid[c][r]==="W"&&c===1){ // wild di tengah row
  payout+=bet*50;}}
}
return payout;}

function updateUI(){document.getElementById('credits').textContent=credits;document.getElementById('betAmount').textContent=bet;}

document.getElementById('spin').onclick=()=>{if(freeSpins>0){freeSpins--;spinOnce();}else spinOnce();};
document.getElementById('bet').oninput=e=>{bet=Number(e.target.value);updateUI();};
document.getElementById('reset').onclick=()=>{credits=2000;freeSpins=0;updateUI();};
updateUI();
</script>
</body>
</html>
