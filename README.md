[navidad_mundial.html](https://github.com/user-attachments/files/28990655/navidad_mundial.html)

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Filtrando Ilusiones – Navidad del Mundo</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;}
body{background:#000;overflow:hidden;font-family:'Georgia',serif;touch-action:none;}
#canvas{display:block;width:100vw;height:100vh;}

/* HUD title */
#hud{position:fixed;top:0;left:0;right:0;z-index:10;pointer-events:none;
  padding:14px 16px 10px;
  background:linear-gradient(to bottom,rgba(0,0,0,0.75) 0%,transparent 100%);}
#hud h1{color:#F5E6C8;font-size:15px;font-style:italic;letter-spacing:0.5px;
  text-shadow:0 1px 6px rgba(0,0,0,0.9);text-align:center;}
#hud p{color:#C8A050;font-size:11px;text-align:center;margin-top:3px;letter-spacing:1px;}

/* Story overlay */
#story{
  position:fixed;inset:0;z-index:50;
  display:flex;flex-direction:column;align-items:center;justify-content:center;
  background:rgba(0,0,0,0);
  pointer-events:none;
  transition:background 0.5s ease;
}
#story.active{background:rgba(0,0,0,0.82);pointer-events:all;}

#story-card{
  width:92%;max-width:380px;max-height:80vh;overflow-y:auto;
  background:linear-gradient(145deg,#3D1500,#1A0800);
  border:1.5px solid #C8A050;border-radius:16px;
  padding:24px 20px 20px;
  transform:scale(0.6) translateY(60px);opacity:0;
  transition:transform 0.5s cubic-bezier(0.34,1.56,0.64,1), opacity 0.4s ease;
  box-shadow:0 0 60px rgba(200,160,80,0.3),inset 0 0 40px rgba(0,0,0,0.4);
}
#story.active #story-card{transform:scale(1) translateY(0);opacity:1;}

#story-flag{font-size:44px;text-align:center;display:block;margin-bottom:4px;
  filter:drop-shadow(0 2px 8px rgba(200,160,80,0.6));}
#story-country{color:#F5E6C8;font-size:22px;font-style:italic;
  text-align:center;letter-spacing:1px;margin-bottom:4px;
  text-shadow:0 0 20px rgba(200,160,80,0.5);}
#story-subtitle{color:#C8A050;font-size:13px;text-align:center;
  margin-bottom:16px;letter-spacing:0.5px;font-style:italic;}
#story-divider{border:none;border-top:1px solid rgba(200,160,80,0.4);margin:0 0 16px;}
#story-text{color:#E8D5A8;font-size:14px;line-height:1.8;text-align:justify;}
#story-text em{color:#F5C842;font-style:normal;}

#close-btn{
  display:block;margin:20px auto 0;
  background:transparent;border:1px solid #C8A050;
  color:#C8A050;font-size:13px;font-family:'Georgia',serif;
  padding:8px 28px;border-radius:30px;cursor:pointer;letter-spacing:1px;
  transition:all 0.2s;
}
#close-btn:active{background:rgba(200,160,80,0.15);}

/* Pulse ring on tap */
.pulse-ring{
  position:fixed;pointer-events:none;z-index:40;
  width:60px;height:60px;border-radius:50%;
  border:2px solid rgba(200,160,80,0.8);
  transform:translate(-50%,-50%) scale(0);
  animation:pulse-anim 0.6s ease-out forwards;
}
@keyframes pulse-anim{
  0%{transform:translate(-50%,-50%) scale(0);opacity:1;}
  100%{transform:translate(-50%,-50%) scale(2.5);opacity:0;}
}

/* Country dots */
.country-dot{
  position:fixed;pointer-events:none;z-index:8;
  width:10px;height:10px;border-radius:50%;
  background:#F5C842;
  box-shadow:0 0 8px 2px rgba(245,200,66,0.7);
  transform:translate(-50%,-50%);
  transition:transform 0.2s,box-shadow 0.2s;
}

/* Gyro hint */
#hint{position:fixed;bottom:18px;left:0;right:0;text-align:center;
  color:rgba(200,160,80,0.6);font-size:11px;letter-spacing:1px;
  pointer-events:none;z-index:10;animation:fade-hint 3s ease 2s forwards;}
@keyframes fade-hint{0%{opacity:1;}100%{opacity:0;}}

/* Stars bg */
#stars{position:fixed;inset:0;z-index:0;pointer-events:none;}
</style>
</head>
<body>
<canvas id="stars"></canvas>
<canvas id="canvas"></canvas>

<div id="hud">
  <h1>Filtrando Ilusiones: Un Viaje por la Navidad del Mundo</h1>
  <p>✦ PULSA CADA PAÍS PARA DESCUBRIR SU CUENTO ✦</p>
</div>

<div id="story">
  <div id="story-card">
    <span id="story-flag">🌍</span>
    <div id="story-country">País</div>
    <div id="story-subtitle">El cuento</div>
    <hr id="story-divider">
    <p id="story-text"></p>
    <button id="close-btn" onclick="closeStory()">✦ Cerrar ✦</button>
  </div>
</div>

<div id="hint">Rota el globo con el dedo · Pulsa un país</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
// ─── DATA ────────────────────────────────────────────────────────────────────
const COUNTRIES = [
  {
    name:"Finlandia", flag:"🇫🇮",
    subtitle:"La carta que llegó al bosque nevado",
    lat:64, lon:26, color:0x4AAEDC,
    text:`En un pequeño pueblo entre abetos cubiertos de nieve, <em>Liisa</em> escribió su carta a Joulupukki —el Papá Noel finlandés— y la dejó junto a la chimenea.\n\nEsa noche, las auroras boreales iluminaron el cielo y ella vio a lo lejos, entre los pinos, una luz que se movía. Al despertar, su regalo estaba allí: un trineo en miniatura tallado a mano.\n\nLiisa supo que <em>Joulupukki vive de verdad en Laponia</em>, y que los renos conocen cada nombre.`
  },
  {
    name:"Alemania", flag:"🇩🇪",
    subtitle:"El secreto del mercado de Núremberg",
    lat:51, lon:10, color:0x4A8A3A,
    text:`Cada diciembre, <em>Klara</em> esperaba el momento de visitar el mercado navideño con su abuela. Entre el aroma de las galletas de jengibre y las luces de las casitas medievales, su abuela le confió un secreto.\n\n«Si cierras los ojos junto al árbol central y pides un deseo, el espíritu del Adviento lo escucha.»\n\nKlara lo hizo. Pidió que su familia estuviera siempre unida. Y ese año, su tío que vivía lejos <em>apareció de sorpresa en la puerta de casa</em>.`
  },
  {
    name:"España", flag:"🇪🇸",
    subtitle:"La noche de los Reyes",
    lat:40, lon:-4, color:0xDDAA00,
    text:`<em>Carmen</em> no podía dormir. Era la noche del 5 de enero y había dejado sus zapatos en el balcón, llenos de paja para los camellos.\n\nSu abuelo le contó que Melchor, Gaspar y Baltasar viajaban desde muy lejos siguiendo la misma estrella de siempre, y que si los niños se quedaban despiertos, <em>los Reyes pasaban de largo</em>.\n\nCarmen cerró los ojos con fuerza. A la mañana siguiente, los zapatos estaban llenos de caramelos y un pequeño libro con su nombre escrito en la portada. Los Reyes siempre saben.`
  },
  {
    name:"México", flag:"🇲🇽",
    subtitle:"La última posada",
    lat:23, lon:-102, color:0xCC3388,
    text:`Durante nueve noches, <em>Diego</em> y su familia habían ido de casa en casa cantando y pidiendo posada, como hicieron María y José.\n\nLa última noche, Diego cargaba la estrella de la piñata. Cuando por fin la puerta se abrió y los recibieron con ponche caliente y buñuelos, Diego sintió que ese momento era la Navidad de verdad.\n\nNo los regalos, sino el camino, el frío, la espera, y <em>la alegría de que al final siempre hay alguien que abre la puerta</em>.`
  },
  {
    name:"Brasil", flag:"🇧🇷",
    subtitle:"Papai Noel en la favela",
    lat:-15, lon:-47, color:0x2A9A44,
    text:`En Río de Janeiro, <em>Isabela</em> vivía en una favela donde las casas trepaban por el cerro como estrellas de colores. En Navidad, todo el barrio se llenaba de guirnaldas y música.\n\nUn niño nuevo, llegado del interior, preguntó si Papai Noel también llegaba allí. Isabela sonrió y señaló las luces en cada ventana: «Aquí no necesita trineo. <em>Sube a pie, como todos</em>.»\n\nEsa noche bailaron juntos hasta tarde. El regalo más grande fue el amigo nuevo.`
  },
  {
    name:"Etiopía", flag:"🇪🇹",
    subtitle:"La luz de Genna",
    lat:9, lon:38, color:0xCC7722,
    text:`En Lalibela, <em>Dawit</em> se despertó antes del amanecer para participar en la procesión del Genna —la Navidad etíope, que se celebra el <em>7 de enero</em>—.\n\nVestido de blanco, sostenía una vela encendida junto a cientos de personas. Las iglesias de piedra brillaban en la oscuridad y el canto llenaba el aire frío de la montaña.\n\nSu abuela le dijo: «La luz que llevas en la mano es la misma que iluminó el mundo hace dos mil años.» <em>Dawit no olvidó esa noche en toda su vida.</em>`
  },
  {
    name:"Japón", flag:"🇯🇵",
    subtitle:"El cubo rojo de Tokio",
    lat:36, lon:138, color:0xDD3333,
    text:`<em>Hana</em> no entendía por qué cada Navidad su familia pedía pollo frito en KFC, hasta que su abuelo le contó la historia.\n\nHace muchos años, un hombre vio en una vitrina de Tokio al Coronel Sanders vestido de Papá Noel y pensó: «Este hombre trae alegría.» Desde entonces, el pollo navideño se convirtió en tradición.\n\nEsa noche, bajo las luces de neón de Shibuya, Hana entendió que <em>la magia no siempre llega del cielo</em>: a veces llega en una caja roja y huele a especias.`
  },
  {
    name:"Australia", flag:"🇦🇺",
    subtitle:"Papá Noel en la playa",
    lat:-25, lon:134, color:0xDDAA00,
    text:`En Sydney, <em>Tom</em> estaba convencido de que Papá Noel no podría llegar: hacía 35 grados y no había chimenea.\n\nPero su madre le explicó que en Australia, Santa cambia el trineo por una tabla de surf. El 25 de diciembre, mientras la familia hacía barbacoa en la playa, apareció en el agua un hombre con traje rojo y gorro playero, riendo a carcajadas.\n\nTom corrió descalzo por la arena y recibió su regalo: una caña de pescar. <em>El verano más feliz que recordaría.</em>`
  },
];

// ─── STARS BACKGROUND ────────────────────────────────────────────────────────
(function drawStars(){
  const c=document.getElementById('stars');
  c.width=window.innerWidth;c.height=window.innerHeight;
  const ctx=c.getContext('2d');
  ctx.fillStyle='#000';ctx.fillRect(0,0,c.width,c.height);
  for(let i=0;i<300;i++){
    const x=Math.random()*c.width,y=Math.random()*c.height;
    const r=Math.random()*1.5;
    const alpha=0.4+Math.random()*0.6;
    ctx.beginPath();ctx.arc(x,y,r,0,Math.PI*2);
    ctx.fillStyle=`rgba(255,255,240,${alpha})`;ctx.fill();
  }
})();

// ─── THREE.JS GLOBE ──────────────────────────────────────────────────────────
const W=window.innerWidth, H=window.innerHeight;
const renderer=new THREE.WebGLRenderer({canvas:document.getElementById('canvas'),antialias:true,alpha:true});
renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
renderer.setSize(W,H);

const scene=new THREE.Scene();
const camera=new THREE.PerspectiveCamera(45,W/H,0.1,100);
camera.position.z=2.8;

// Globe
const RADIUS=1.0;
const globeGeo=new THREE.SphereGeometry(RADIUS,64,64);

// Parchment-style earth shader material
const globeMat=new THREE.MeshPhongMaterial({
  color:0x8B6914,
  emissive:0x1A0800,
  specular:0x332200,
  shininess:15,
});
const globe=new THREE.Mesh(globeGeo,globeMat);
scene.add(globe);

// Ocean layer (slightly larger, semi-transparent blue)
const oceanMat=new THREE.MeshPhongMaterial({
  color:0x1A4A6A,emissive:0x000A14,
  transparent:true,opacity:0.55,side:THREE.FrontSide,
});
const ocean=new THREE.Mesh(new THREE.SphereGeometry(RADIUS*0.999,64,64),oceanMat);
scene.add(ocean);

// Atmosphere glow
const atmMat=new THREE.MeshPhongMaterial({
  color:0x224488,emissive:0x112244,
  transparent:true,opacity:0.12,side:THREE.BackSide,
});
const atm=new THREE.Mesh(new THREE.SphereGeometry(RADIUS*1.08,32,32),atmMat);
scene.add(atm);

// Continent outline dots (simplified blobs)
const CONTINENTS=[
  // North America
  {lat:50,lon:-100,r:0.28},{lat:35,lon:-100,r:0.22},{lat:20,lon:-100,r:0.12},
  // South America
  {lat:-10,lon:-55,r:0.20},{lat:-30,lon:-58,r:0.14},
  // Europe
  {lat:50,lon:10,r:0.14},{lat:55,lon:20,r:0.10},
  // Africa
  {lat:5,lon:20,r:0.22},{lat:-20,lon:25,r:0.16},
  // Asia
  {lat:50,lon:80,r:0.30},{lat:30,lon:100,r:0.22},{lat:60,lon:120,r:0.18},
  // Australia
  {lat:-25,lon:134,r:0.16},
  // Greenland
  {lat:72,lon:-40,r:0.10},
  // SE Asia
  {lat:15,lon:105,r:0.10},
];

function latLonToVec3(lat,lon,r){
  const phi=(90-lat)*Math.PI/180;
  const theta=(lon+180)*Math.PI/180;
  return new THREE.Vector3(
    -r*Math.sin(phi)*Math.cos(theta),
    r*Math.cos(phi),
    r*Math.sin(phi)*Math.sin(theta)
  );
}

// Draw continent patches
CONTINENTS.forEach(c=>{
  const pos=latLonToVec3(c.lat,c.lon,RADIUS+0.002);
  const geo=new THREE.SphereGeometry(c.r,16,16);
  const mat=new THREE.MeshPhongMaterial({color:0xC8A050,emissive:0x3A2800,shininess:5});
  const mesh=new THREE.Mesh(geo,mat);
  mesh.position.copy(pos);
  scene.add(mesh);
});

// Country markers
const markerMeshes=[];
COUNTRIES.forEach((c,i)=>{
  const pos=latLonToVec3(c.lat,c.lon,RADIUS+0.022);
  // Pulsing dot
  const geo=new THREE.SphereGeometry(0.028,16,16);
  const mat=new THREE.MeshPhongMaterial({color:c.color,emissive:new THREE.Color(c.color).multiplyScalar(0.4),shininess:80});
  const mesh=new THREE.Mesh(geo,mat);
  mesh.position.copy(pos);
  mesh.userData={index:i,country:c};
  scene.add(mesh);
  markerMeshes.push(mesh);

  // Halo ring
  const ringGeo=new THREE.RingGeometry(0.035,0.048,24);
  const ringMat=new THREE.MeshBasicMaterial({color:c.color,transparent:true,opacity:0.55,side:THREE.DoubleSide});
  const ring=new THREE.Mesh(ringGeo,ringMat);
  ring.position.copy(pos);
  ring.lookAt(0,0,0);ring.rotateX(Math.PI/2);
  ring.userData={isRing:true,index:i};
  scene.add(ring);
});

// Lights
const ambientLight=new THREE.AmbientLight(0x332200,0.6);
scene.add(ambientLight);
const sunLight=new THREE.DirectionalLight(0xFFE0A0,1.2);
sunLight.position.set(3,2,2);
scene.add(sunLight);
const rimLight=new THREE.DirectionalLight(0x2244AA,0.4);
rimLight.position.set(-3,-1,-2);
scene.add(rimLight);

// Rotation state
let isDragging=false;
let prevMouse={x:0,y:0};
let rotVel={x:0,y:0};
let autoRot=true;
let autoRotSpeed=0.003;

// Touch / mouse
const canvas=document.getElementById('canvas');

function onDown(x,y){isDragging=true;autoRot=false;prevMouse={x,y};rotVel={x:0,y:0};}
function onMove(x,y){
  if(!isDragging)return;
  const dx=x-prevMouse.x, dy=y-prevMouse.y;
  rotVel.x=dy*0.005; rotVel.y=dx*0.005;
  globe.rotation.x+=rotVel.x; globe.rotation.y+=rotVel.y;
  ocean.rotation.copy(globe.rotation);
  atm.rotation.copy(globe.rotation);
  markerMeshes.forEach(m=>{m.rotation.copy(globe.rotation);});
  scene.children.filter(c=>c.userData&&c.userData.isRing).forEach(r=>{r.rotation.copy(globe.rotation);});
  prevMouse={x,y};
}
function onUp(){isDragging=false;setTimeout(()=>{autoRot=true;},2000);}

canvas.addEventListener('mousedown',e=>onDown(e.clientX,e.clientY));
canvas.addEventListener('mousemove',e=>onMove(e.clientX,e.clientY));
canvas.addEventListener('mouseup',onUp);
canvas.addEventListener('touchstart',e=>{e.preventDefault();const t=e.touches[0];onDown(t.clientX,t.clientY);},{passive:false});
canvas.addEventListener('touchmove',e=>{e.preventDefault();const t=e.touches[0];onMove(t.clientX,t.clientY);},{passive:false});
canvas.addEventListener('touchend',e=>{e.preventDefault();onUp();handleTap(e.changedTouches[0].clientX,e.changedTouches[0].clientY);},{passive:false});
canvas.addEventListener('click',e=>handleTap(e.clientX,e.clientY));

// Raycasting for country selection
const raycaster=new THREE.Raycaster();
const mouse=new THREE.Vector2();
let tapStart={x:0,y:0};

canvas.addEventListener('mousedown',e=>{tapStart={x:e.clientX,y:e.clientY};});
canvas.addEventListener('touchstart',e=>{const t=e.touches[0];tapStart={x:t.clientX,y:t.clientY};},{passive:true});

function handleTap(cx,cy){
  // Only fire if not a drag
  if(Math.abs(cx-tapStart.x)>10||Math.abs(cy-tapStart.y)>10)return;

  mouse.x=(cx/window.innerWidth)*2-1;
  mouse.y=-(cy/window.innerHeight)*2+1;
  raycaster.setFromCamera(mouse,camera);

  const hits=raycaster.intersectObjects(markerMeshes);
  if(hits.length>0){
    const idx=hits[0].object.userData.index;
    spawnPulse(cx,cy);
    openStory(idx);
  }
}

function spawnPulse(x,y){
  const div=document.createElement('div');
  div.className='pulse-ring';
  div.style.left=x+'px'; div.style.top=y+'px';
  document.body.appendChild(div);
  setTimeout(()=>div.remove(),700);
}

// Story panel
function openStory(i){
  const c=COUNTRIES[i];
  document.getElementById('story-flag').textContent=c.flag;
  document.getElementById('story-country').textContent=c.name;
  document.getElementById('story-subtitle').textContent=c.subtitle;
  // Render text with em tags
  document.getElementById('story-text').innerHTML=c.text.replace(/\n\n/g,'<br><br>');
  document.getElementById('story').classList.add('active');
  autoRot=false;
}

function closeStory(){
  document.getElementById('story').classList.remove('active');
  setTimeout(()=>{autoRot=true;},800);
}

// Gyroscope (DeviceOrientation) for AR-like feel
let gyroEnabled=false;
let gyroAlpha=0,gyroBeta=0,gyroGamma=0;
let gyroOffset={alpha:null,beta:null};

function requestGyro(){
  if(typeof DeviceOrientationEvent!=='undefined'&&typeof DeviceOrientationEvent.requestPermission==='function'){
    DeviceOrientationEvent.requestPermission().then(r=>{if(r==='granted')enableGyro();});
  } else {
    enableGyro();
  }
}

function enableGyro(){
  window.addEventListener('deviceorientation',e=>{
    if(gyroOffset.alpha===null){gyroOffset.alpha=e.alpha;gyroOffset.beta=e.beta;}
    gyroBeta=(e.beta-gyroOffset.beta)*Math.PI/180;
    gyroAlpha=(e.alpha-gyroOffset.alpha)*Math.PI/180;
    gyroEnabled=true;
  });
}
requestGyro();

// Marker pulse animation
let t=0;
function animateMarkers(){
  t+=0.04;
  markerMeshes.forEach((m,i)=>{
    const s=1+0.18*Math.sin(t+i*0.8);
    m.scale.setScalar(s);
  });
}

// Sync all globe children rotation
function syncRotation(){
  const r=globe.rotation;
  ocean.rotation.set(r.x,r.y,r.z);
  atm.rotation.set(r.x,r.y,r.z);
  scene.children.forEach(c=>{
    if(c.userData&&(c.userData.index!==undefined)){
      // Recompute position based on rotated globe
    }
  });
}

// For correct marker position: attach markers to globe
// Detach and re-add to globe group
scene.remove(ocean);scene.remove(atm);
globe.add(ocean);
scene.children.filter(c=>c.isMesh&&c!==globe).forEach(c=>{
  scene.remove(c);globe.add(c);
});
globe.children.forEach(c=>{
  if(c.userData&&c.userData.isRing){
    // fix ring orientation
  }
});

// Render loop
function animate(){
  requestAnimationFrame(animate);
  if(autoRot){
    globe.rotation.y+=autoRotSpeed;
  }
  if(gyroEnabled&&autoRot){
    globe.rotation.x+=(gyroBeta*0.3-globe.rotation.x)*0.05;
    globe.rotation.z+=(-gyroAlpha*0.3-globe.rotation.z)*0.05;
  }
  animateMarkers();
  renderer.render(scene,camera);
}
animate();

// Resize
window.addEventListener('resize',()=>{
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth,window.innerHeight);
});
</script>
</body>
</html>
