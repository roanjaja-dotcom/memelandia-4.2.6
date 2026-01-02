<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Memelandia 4.2.6</title>
<style>
body{margin:0;font-family:Arial;background:#111;color:#fff}
header{background:#000;padding:10px;display:flex;align-items:center;justify-content:space-between}
#title{font-size:1.8em}
#userBox{display:flex;align-items:center;gap:8px;cursor:pointer}
#avatar{width:32px;height:32px;border-radius:50%;background:#333;display:flex;align-items:center;justify-content:center;font-size:.8em}
#top{display:flex;justify-content:center;flex-wrap:wrap;background:#000;padding:5px}
button{padding:6px 10px;border:none;border-radius:6px;cursor:pointer;margin:4px}
#memes{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:10px;padding:10px}
.meme{background:#222;padding:5px;border-radius:8px;position:relative}
.meme img{width:100%;border-radius:6px}
.like{position:absolute;bottom:6px;right:6px;background:#ff004c;color:#fff;border-radius:50%;width:34px;height:34px;display:flex;align-items:center;justify-content:center;cursor:pointer}
.modal{position:fixed;inset:0;background:rgba(0,0,0,.8);display:none;align-items:center;justify-content:center}
.box{background:#111;padding:15px;border-radius:10px;width:320px}
input, select{width:95%;padding:6px;margin:5px;border-radius:6px;border:none}
canvas{border-radius:8px;display:block;margin:10px auto}
iframe{width:100%;border-radius:8px}
</style>
</head>
<body>
<header>
<div id="title">MEMELANDIA 4.2.6</div>
<div id="userBox" onclick="openProfile()">
<div id="avatar">👤</div>
<div id="usernameText"></div>
</div>
</header>

<div id="top">
<button onclick="openUpload()">➕ Subir</button>
<button onclick="openCreateMeme()">🎨 Crear Meme</button>
<button onclick="openAnimate()">🕹 Animación</button>
<button onclick="openAI()">🤖 IA</button>
<button onclick="openProfile()">👤 Perfil</button>
<button onclick="sortLikes()">🏆 Ranking</button>
<button onclick="toggleTheme()">🌙/☀️</button>
<button onclick="showStats()">📊 Stats</button>
<button onclick="shuffleMemes()">🎲 Aleatorio</button>
<button onclick="addRandomMeme()">🔥 Meme nuevo</button>
<button onclick="openMinigames()">🎮 Mini juegos</button>
<button onclick="openVideos()">▶️ Ver videos</button>
<button onclick="openChat()">💬 Chat</button>
<button onclick="addBulkMemes()">✨ Más memes</button>
</div>

<section id="memes"></section>

<!-- VIDEOS -->
<div class="modal" id="videos"><div class="box">
<h3>Videos YouTuber</h3>
<iframe height="200" src="https://www.youtube.com/embed/dQw4w9WgXcQ" allowfullscreen></iframe>
<iframe height="200" src="https://www.youtube.com/embed/9bZkp7q19f0" allowfullscreen></iframe>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- MINI JUEGOS -->
<div class="modal" id="minigames"><div class="box">
<h3>Mini Juegos</h3>
<button onclick="startGame('obby')">🟦 Obby</button>
<button onclick="startGame('brainrot')">🧠 Steal a Brainrot</button>
<button onclick="startGame('forest')">🌲 99 Noches</button>
<canvas id="game" width="300" height="200" style="display:none;background:#222;margin-top:10px"></canvas>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- CHAT -->
<div class="modal" id="chat"><div class="box">
<h3>Chat</h3>
<div id="chatLog" style="height:120px;overflow:auto;background:#000;padding:5px"></div>
<input id="chatName" placeholder="Tu nombre">
<input id="chatMsg" placeholder="Mensaje">
<button onclick="sendChat()">Enviar</button>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- STATS -->
<div class="modal" id="stats"><div class="box">
<h3>Estadísticas</h3>
<p id="statsText"></p>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- UPLOAD -->
<div class="modal" id="upload"><div class="box">
<h3>Nuevo meme</h3>
<input id="url" placeholder="URL imagen">
<input id="text" placeholder="Texto">
<button onclick="addMeme()">Subir</button>
<button onclick="closeAll()">Cancelar</button>
</div></div>

<!-- CREATE MEME -->
<div class="modal" id="createMeme"><div class="box">
<h3>Crear Meme</h3>
<input id="cmText" placeholder="Texto del meme">
<select id="cmIcon">
<option>😀</option>
<option>😎</option>
<option>🤖</option>
<option>🐶</option>
<option>🐱</option>
</select>
<button onclick="addCustomMeme()">Crear</button>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- ANIMATION -->
<div class="modal" id="animate"><div class="box">
<h3>Animación Interactiva</h3>
<canvas id="animCanvas" width="300" height="300" style="background:#222"></canvas>
<p>Haz clic en cabeza, manos, hombros o pies para moverlas</p>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- PROFILE -->
<div class="modal" id="profile"><div class="box">
<h3>Perfil</h3>
<input id="username" placeholder="Nombre">
<input id="avatarInput" placeholder="Emoji">
<button onclick="saveProfile()">Guardar</button>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<!-- IA -->
<div class="modal" id="ai"><div class="box">
<h3>IA Meme</h3>
<input id="prompt" placeholder="Idea del meme">
<button onclick="genAI()">Generar</button>
<button onclick="closeAll()">Cerrar</button>
</div></div>

<script>
const memesDiv=document.getElementById('memes');
const usernameText=document.getElementById('usernameText');
const avatarBox=document.getElementById('avatar');
function loadJSON(k,f){try{const r=localStorage.getItem(k);return r?JSON.parse(r):f}catch{localStorage.removeItem(k);return f}}
let user=loadJSON('user42',{name:'Anon',avatar:'👤'});
let memes=loadJSON('memes42',[{img:'https://i.imgflip.com/1bij.jpg',txt:'Meme clásico',likes:0},{img:'https://i.imgflip.com/30b1gx.jpg',txt:'Drake meme',likes:0},{img:'https://i.imgflip.com/26am.jpg',txt:'Doge',likes:0}]);
function syncUser(){usernameText.textContent=user.name;avatarBox.textContent=user.avatar}
syncUser();
function render(){memesDiv.innerHTML='';memes.forEach((m,i)=>{const d=document.createElement('div');d.className='meme';d.innerHTML=`<img src="${m.img}"><p>${m.txt}</p><div class="like" onclick="like(${i})">❤️ ${m.likes}</div>`;memesDiv.appendChild(d)});localStorage.setItem('memes42',JSON.stringify(memes))}
render();
function like(i){memes[i].likes++;render()}
function addMeme(){if(!url.value)return alert('Falta URL');memes.unshift({img:url.value,txt:text.value||'',likes:0});closeAll();render()}
function genAI(){const ideas=['Cuando abrís la heladera','POV una partida más','Yo confiando en el auto guardado'];const imgs=['https://i.imgflip.com/1bij.jpg','https://i.imgflip.com/26am.jpg'];const idea=prompt.value||ideas[Math.floor(Math.random()*ideas.length)];memes.unshift({img:imgs[Math.floor(Math.random()*imgs.length)],txt:'IA: '+idea,likes:0});closeAll();render()}
function saveProfile(){user.name=username.value||user.name;user.avatar=avatarInput.value||user.avatar;localStorage.setItem('user42',JSON.stringify(user));syncUser();closeAll()}
function openUpload(){document.getElementById('upload').style.display='flex'}
function openProfile(){document.getElementById('profile').style.display='flex'}
function openAI(){document.getElementById('ai').style.display='flex'}
function openChat(){document.getElementById('chat').style.display='flex'}
function openVideos(){document.getElementById('videos').style.display='flex'}
function openMinigames(){document.getElementById('minigames').style.display='flex'}
function openCreateMeme(){document.getElementById('createMeme').style.display='flex'}
function openAnimate(){document.getElementById('animate').style.display='flex';drawMuñeco()}
function closeAll(){document.querySelectorAll('.modal').forEach(m=>m.style.display='none')}
function sortLikes(){memes.sort((a,b)=>b.likes-a.likes);render()}
let dark=true
function toggleTheme(){dark=!dark;document.body.style.background=dark?'#111':'#eee';document.body.style.color=dark?'#fff':'#000'}
function shuffleMemes(){memes.sort(()=>Math.random()-0.5);render()}
function addRandomMeme(){const extra=[{img:'https://i.imgflip.com/5c7lwq.jpg',txt:'GigaChad',likes:0},{img:'https://i.imgflip.com/5w3.jpg',txt:'This is fine',likes:0}];memes.unshift(extra[Math.floor(Math.random()*extra.length)]);render()}
function addBulkMemes(){const extra=[
{img:'https://i.imgflip.com/2hgfwc.jpg',txt:'Nuevo Meme 1',likes:0},
{img:'https://i.imgflip.com/3vzej.jpg',txt:'Nuevo Meme 2',likes:0},
{img:'https://i.imgflip.com/4t0m5.jpg',txt:'Nuevo Meme 3',likes:0}
];memes.unshift(...extra);render()}
function sendChat(){const name=chatName.value||'Anon';const msg=chatMsg.value;if(!msg)return;const log=document.getElementById('chatLog');log.innerHTML+=`<div><b>${name}:</b> ${msg}</div>`;chatMsg.value='';log.scrollTop=log.scrollHeight}
function showStats(){statsText.textContent=`Memes: ${memes.length} | Likes: ${memes.reduce((a,b)=>a+b.likes,0)}`;document.getElementById('stats').style.display='flex'}
let ctx,player
function startGame(type){const c=document.getElementById('game');c.style.display='block';ctx=c.getContext('2d');player={x:10,y:150};requestAnimationFrame(loop)}
function loop(){if(!ctx||!player)return;ctx.clearRect(0,0,300,200);ctx.fillStyle='lime';ctx.fillRect(player.x,player.y,20,20);requestAnimationFrame(loop)}
document.addEventListener('keydown',e=>{if(!player)return;if(e.key==='ArrowRight')player.x+=5;if(e.key==='ArrowLeft')player.x-=5;if(e.key==='ArrowUp')player.y-=5;if(e.key==='ArrowDown')player.y+=5})
function addCustomMeme(){const txt=cmText.value||'';const ic=cmIcon.value||'😀';memes.unshift({img:'https://i.imgflip.com/1bij.jpg',txt:ic+' '+txt,likes:0});render();closeAll()}

// Animación muñeco interactiva
let animCanvas=document.getElementById('animCanvas');
let animCtx=animCanvas.getContext('2d');
let bodyParts={head:{x:140,y:50,r:20},leftHand:{x:100,y:120,r:15},rightHand:{x:180,y:120,r:15},leftFoot:{x:120,y:200,r:15},rightFoot:{x:160,y:200,r:15},shoulders:{x:140,y:100,r:20}};
function drawMuñeco(){animCtx.clearRect(0,0,300,300);animCtx.fillStyle='orange';
for(const p in bodyParts){let bp=bodyParts[p];animCtx.beginPath();animCtx.arc(bp.x,bp.y,bp.r,0,2*Math.PI);animCtx.fill();}}
animCanvas.addEventListener('click',e=>{const rect=animCanvas.getBoundingClientRect();const x=e.clientX-rect.left;const y=e.clientY-rect.top;
for(const p in bodyParts){let bp=bodyParts[p];if(Math.hypot(x-bp.x,y-bp.y)<=bp.r){bp.x+=Math.random()*20-10;bp.y+=Math.random()*20-10;drawMuñeco();break;}}})

console.assert(Array.isArray(memes),'Memes OK')
console.assert(typeof openVideos==='function','Videos OK')
</script>
</body>
</html>
