# Galaxian-Explosion
Galaxian Explosion - Space Slot Game

<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>GALAXIAN EXPLOSION</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;background:#050514;color:white;
 font-family:Arial,sans-serif;text-align:center;
 background-image:radial-gradient(#34346b 1px,transparent 1px);
 background-size:24px 24px;
}
header{padding:22px 8px 12px}
h1{font-size:clamp(27px,7vw,44px);color:#ff9b27;
 text-shadow:0 0 15px #ff3500;margin:0}
header p{color:#aebaff}
.panel{max-width:480px;margin:auto;padding:12px}
.stats{display:flex;justify-content:space-around;
 background:#14142d;border:1px solid #45458d;
 border-radius:12px;padding:15px;margin-bottom:15px}
.stat{font-size:13px;color:#b8c4ff}
.stat strong{display:block;color:#fff;font-size:23px;margin-top:5px}
.machine{border:3px solid #f7a52b;border-radius:18px;
 padding:10px;background:linear-gradient(#191936,#09091b);
 box-shadow:0 0 25px #ff650055}
.grid{display:grid;grid-template-columns:repeat(5,1fr);gap:5px}
.cell{height:clamp(62px,16vw,83px);display:flex;
 align-items:center;justify-content:center;
 background:#080819;border:1px solid #484878;border-radius:8px;
 font-size:clamp(27px,8vw,43px)}
.cell.win{border-color:#ffe25b;box-shadow:inset 0 0 18px #ff9d00;
 animation:flash .5s infinite alternate}
@keyframes flash{to{background:#51400a}}
button{border:0;border-radius:12px;padding:14px 20px;
 font-weight:bold;font-size:17px;cursor:pointer}
#spin{width:100%;margin-top:15px;color:#160600;
 background:linear-gradient(90deg,#ff8b19,#ffe45b);
 box-shadow:0 0 18px #ff8b1966}
#spin:disabled{opacity:.6}
.controls{display:flex;justify-content:center;align-items:center;
 gap:15px;margin-top:12px}
.controls button{background:#28284c;color:white}
#message{min-height:54px;padding:15px 3px;color:#ffe28a;
 font-weight:bold}
.small{font-size:12px;color:#8e91b8;line-height:1.6}
#boom{display:none;position:fixed;inset:0;z-index:5;
 background:#ff7a00;align-items:center;justify-content:center;
 flex-direction:column;color:#fff;text-shadow:0 0 20px red;
 animation:supernova .9s ease-out}
#boom h2{font-size:clamp(35px,10vw,65px);margin:10px}
#boom .sun{font-size:100px;animation:pulse .25s infinite alternate}
@keyframes pulse{to{transform:scale(1.3)}}
@keyframes supernova{0%{opacity:0}20%{opacity:1}
 100%{opacity:.95}}
</style>
</head>
<body>
<header>
<h1>☀ GALAXIAN EXPLOSION</h1>
<p>LE DESTIN DU SOLEIL EST ENTRE TES MAINS</p>
</header>

<main class="panel">
<div class="stats">
 <div class="stat">CRÉDITS<strong id="credits">1000</strong></div>
 <div class="stat">MISE<strong id="bet">10</strong></div>
 <div class="stat">GAIN<strong id="win">0</strong></div>
</div>

<div class="machine">
 <div class="grid" id="grid"></div>
</div>

<button id="spin">🚀 SPIN</button>
<div class="controls">
 <button id="minus">− MISE</button>
 <button id="plus">+ MISE</button>
</div>
<div id="message">Prêt à déclencher la supernova ?</div>
<p class="small">
Symboles : ☀️ 🌍 🌙 ⭐ 🚀 💎 🔥<br>
Aligne les symboles sur les lignes horizontales.
</p>
</main>

<div id="boom">
 <div class="sun">☀️</div>
 <h2>SUPERNOVA !</h2>
 <p>LE SOLEIL VIENT D'EXPLOSER !</p>
 <button onclick="document.getElementById('boom').style.display='none'">
 CONTINUER
 </button>
</div>

<script>
const symbols=["☀️","🌍","🌙","⭐","🚀","💎","🔥"];
const grid=document.getElementById("grid");
const creditsEl=document.getElementById("credits");
const betEl=document.getElementById("bet");
const winEl=document.getElementById("win");
const message=document.getElementById("message");
const spinBtn=document.getElementById("spin");
let credits=1000, bet=10, busy=false;

function randomSymbol(){
 return symbols[Math.floor(Math.random()*symbols.length)];
}

function draw(){
 grid.innerHTML="";
 for(let i=0;i<15;i++){
  const cell=document.createElement("div");
  cell.className="cell";
  cell.textContent=randomSymbol();
  grid.appendChild(cell);
 }
}
draw();

function update(){
 creditsEl.textContent=credits;
 betEl.textContent=bet;
}

document.getElementById("minus").onclick=()=>{
 if(!busy){bet=Math.max(10,bet-10);update();}
};
document.getElementById("plus").onclick=()=>{
 if(!busy){bet=Math.min(100,bet+10);update();}
};

spinBtn.onclick=()=>{
 if(busy)return;
 if(credits<bet){
  message.textContent="Crédits insuffisants ! Recharge ta partie.";
  return;
 }
 busy=true;
 spinBtn.disabled=true;
 credits-=bet;
 winEl.textContent="0";
 message.textContent="Les rouleaux tournent...";
 update();

 let ticks=0;
 const cells=[...grid.children];
 const animation=setInterval(()=>{
  cells.forEach(c=>{
   c.textContent=randomSymbol();
   c.classList.remove("win");
  });
  ticks++;
  if(ticks>=12){
   clearInterval(animation);
   finish();
  }
 },100);
};

function finish(){
 const cells=[...grid.children];
 let result=Array.from({length:3},()=>[]);
 for(let r=0;r<3;r++){
  for(let c=0;c<5;c++){
   result[r][c]=randomSymbol();
   cells[r*5+c].textContent=result[r][c];
  }
 }
 let winnings=0;
 let sunExplosion=false;

 for(let r=0;r<3;r++){
  const row=result[r];
  let count=1;
  while(count<5 && row[count]===row[0])count++;
  if(count>=3){
   const multiplier={3:2,4:5,5:15}[count];
   winnings+=bet*multiplier;
   for(let c=0;c<count;c++)
    cells[r*5+c].classList.add("win");
   if(row[0]==="☀️" && count===5)sunExplosion=true;
  }
 }
 credits+=winnings;
 winEl.textContent=winnings;
 update();

 if(sunExplosion){
  message.textContent="SUPERNOVA ! JACKPOT SOLAIRE !";
  document.getElementById("boom").style.display="flex";
 }else if(winnings>0){
  message.textContent="✨ Gagné ! +" + winnings + " crédits virtuels";
 }else{
  message.textContent="Pas de combinaison. Retente ta chance !";
 }
 busy=false;
 spinBtn.disabled=false;
}
</script>
</body>
</html>
