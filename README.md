# space-game
<!doctype html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Aventure Spatiale – Ultimate</title>

<style>
*{margin:0;padding:0;box-sizing:border-box}
body{
  font-family:'Comic Sans MS',cursive;
  background:radial-gradient(circle,#120020,#000);
  color:#fff;overflow-x:hidden
}

/* 🌌 */
.stars{position:fixed;inset:0;z-index:0}
.star{position:absolute;width:2px;height:2px;background:white;border-radius:50%;animation:twinkle 2s infinite}
@keyframes twinkle{50%{opacity:.2}}

header{text-align:center;padding:20px;position:relative;z-index:2}
h1{
  font-size:clamp(2rem,5vw,3rem);
  background:linear-gradient(90deg,#ff6b9d,#ffc371);
  -webkit-background-clip:text;color:transparent;
  animation:glow 2s infinite
}
@keyframes glow{50%{filter:brightness(1.5)}}

.ui{
  display:flex;gap:10px;justify-content:center;
  flex-wrap:wrap;margin-top:10px
}
button{
  padding:10px 18px;border:none;border-radius:20px;
  background:#ffc371;font-weight:bold;cursor:pointer
}

.score{
  position:fixed;top:10px;right:10px;
  background:#8a2be2;padding:12px 18px;
  border-radius:20px;font-weight:bold;z-index:5
}

.container{max-width:1100px;margin:auto;padding:15px;z-index:2;position:relative}

/* 🪐 */
.planet{
  background:rgba(255,255,255,.08);
  border:2px solid #ffc371;
  border-radius:22px;padding:20px;
  margin:20px 0;cursor:pointer;
  animation:float 4s ease-in-out infinite
}
@keyframes float{50%{transform:translateY(-6px)}}
.planet:hover{border-color:#ff6b9d;box-shadow:0 0 25px #ff6b9d}
.planet h2{color:#ffc371}

.quiz{display:none;margin-top:15px}
.option{
  background:#8a2be2;padding:12px;border-radius:14px;
  margin:10px 0;cursor:pointer
}
.option:hover{background:#ff6b9d;transform:translateX(10px)}
.correct{background:#00ff88!important}
.wrong{background:#ff3333!important}

/* 👾 */
.boss{background:rgba(255,0,0,.2);border-color:red;animation:shake .3s infinite}
@keyframes shake{
  25%{transform:translateX(-2px)}
  50%{transform:translateX(2px)}
}

/* 🎬 CINÉ */
.cinematic{
  position:fixed;inset:0;background:black;
  color:#ffc371;display:none;
  align-items:center;justify-content:center;
  text-align:center;z-index:20;
  font-size:clamp(1.2rem,4vw,2rem);
  animation:fadein 2s
}
@keyframes fadein{from{opacity:0}to{opacity:1}}

</style>
</head>

<body>

<div class="stars" id="stars"></div>

<div class="score">Score : <span id="score">0</span></div>

<header>
<h1>🚀 Aventure Spatiale</h1>
<div class="ui">
<button onclick="toggleMusic()">🎵 Musique</button>
<button onclick="changeSkin()">🚀 Skin</button>
<button onclick="toggleHard()">😈 Mode difficile</button>
</div>
</header>

<div class="container" id="game"></div>

<div class="cinematic" id="cine">
<div id="cineText"></div>
</div>

<audio id="music" loop src="https://cdn.pixabay.com/audio/2022/10/09/audio_8b0fa3f3c6.mp3"></audio>
<audio id="bossSound" src="https://cdn.pixabay.com/audio/2022/10/25/audio_946f0a2b42.mp3"></audio>

<script>
const planets=[
 ["🟡 Mercure","Proche du Soleil ?"],
 ["🟠 Vénus","Planète la plus chaude ?"],
 ["🌍 Terre","Planète habitable ?"],
 ["🔴 Mars","Planète rouge ?"],
 ["🟤 Jupiter","La plus grande ?"],
 ["🪐 Saturne","Avec anneaux ?"],
 ["🔵 Uranus","Planète penchée ?"],
 ["🔷 Neptune","Vents violents ?"]
];

let score=+localStorage.score||0;
let done=JSON.parse(localStorage.done||"[]");
let hard=localStorage.hard==="true";
let skin=localStorage.skin||"🚀";

const scoreEl=document.getElementById("score");
scoreEl.textContent=score;

function render(){
 game.innerHTML="";
 planets.forEach((p,i)=>{
  game.innerHTML+=`
  <div class="planet" onclick="openQ(${i})">
   <h2>${skin} ${p[0]} ${done.includes(i)?"✅":""}</h2>
   <div class="quiz" id="q${i}">
    <div class="option" onclick="answer(event,${i},true)">Oui</div>
    <div class="option" onclick="answer(event,${i},false)">Non</div>
   </div>
  </div>`;
 });
 if(done.length===planets.length){
  game.innerHTML+=`
  <div class="planet boss" onclick="finalBoss()">
   <h2>👾 BOSS FINAL</h2>
  </div>`;
 }
}
render();

function openQ(i){
 document.querySelectorAll(".quiz").forEach(q=>q.style.display="none");
 document.getElementById("q"+i).style.display="block";
}

function answer(e,i,ok){
 e.stopPropagation();
 if(done.includes(i))return;
 if(ok){
  done.push(i);
  score+=hard?10:20;
 }
 save();render();
}

function finalBoss(){
 bossSound.play();
 score+=hard?50:150;
 save();
 cinematic();
}

function cinematic(){
 const cine=document.getElementById("cine");
 const txt=document.getElementById("cineText");
 cine.style.display="flex";
 const lines=[
  "🌌 L’Univers est enfin en paix…",
  "🚀 Tu as vaincu le Gardien du Néant",
  "✨ Ton nom entre dans la légende",
  "🏆 FIN"
 ];
 let i=0;
 const interval=setInterval(()=>{
  txt.textContent=lines[i++];
  if(i===lines.length)clearInterval(interval);
 },2000);
}

function toggleMusic(){
 music.paused?music.play():music.pause();
}
function changeSkin(){
 skin=skin==="🚀"?"🔥":skin==="🔥"?"🌈":"🚀";
 localStorage.skin=skin;render();
}
function toggleHard(){
 hard=!hard;
 localStorage.hard=hard;
 alert(hard?"😈 MODE DIFFICILE ACTIVÉ":"🙂 MODE NORMAL");
}

function save(){
 localStorage.score=score;
 localStorage.done=JSON.stringify(done);
 scoreEl.textContent=score;
}

function stars(){
 for(let i=0;i<120;i++){
  const s=document.createElement("div");
  s.className="star";
  s.style.top=Math.random()*100+"%";
  s.style.left=Math.random()*100+"%";
  stars.appendChild(s);
 }
}
stars();
</script>

</body>
</html>
