[navidad_mundial(2).html](https://github.com/user-attachments/files/28993600/navidad_mundial.2.html)

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Filtrando Ilusiones – Navidad del Mundo</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;}
html,body{width:100%;height:100%;overflow:hidden;background:#050A18;font-family:Georgia,serif;}

/* Estrellas */
#stars{position:fixed;inset:0;z-index:0;}

/* Globo contenedor */
#globe-wrap{
  position:fixed;inset:0;z-index:1;
  display:flex;align-items:center;justify-content:center;
}
#globe{
  position:relative;
  width:min(80vw,80vh);
  height:min(80vw,80vh);
  border-radius:50%;
  cursor:grab;
  user-select:none;
  touch-action:none;
}
#globe:active{cursor:grabbing;}

/* Capas del globo */
.globe-layer{
  position:absolute;inset:0;border-radius:50%;
}
#ocean{
  background:radial-gradient(circle at 35% 35%, #1E6FA8, #0D3A5C 60%, #061A30);
  box-shadow:
    inset -18px -18px 40px rgba(0,0,0,0.7),
    inset 8px 8px 20px rgba(100,180,255,0.15),
    0 0 60px rgba(30,80,180,0.4),
    0 0 120px rgba(10,40,100,0.2);
}
#continents{position:absolute;inset:0;border-radius:50%;overflow:hidden;}
#shine{
  position:absolute;inset:0;border-radius:50%;
  background:radial-gradient(circle at 30% 25%, rgba(255,255,255,0.18) 0%, transparent 55%);
  pointer-events:none;z-index:10;
}
#atmosphere{
  position:absolute;inset:-3%;border-radius:50%;
  background:radial-gradient(circle at 50% 50%, transparent 60%, rgba(30,100,200,0.25) 80%, rgba(10,50,150,0.4) 100%);
  pointer-events:none;z-index:11;
}

/* Puntos de países */
.country-pin{
  position:absolute;
  width:14px;height:14px;
  border-radius:50%;
  transform:translate(-50%,-50%);
  cursor:pointer;
  z-index:20;
  transition:transform 0.2s;
  box-shadow:0 0 0 2px rgba(255,255,255,0.3);
}
.country-pin::after{
  content:'';position:absolute;inset:-4px;border-radius:50%;
  animation:ping 1.8s ease-out infinite;
}
@keyframes ping{
  0%{box-shadow:0 0 0 0 currentColor;opacity:0.8;}
  100%{box-shadow:0 0 0 12px transparent;opacity:0;}
}
.country-pin:active{transform:translate(-50%,-50%) scale(1.4);}

/* Etiqueta de país */
.pin-label{
  position:absolute;
  white-space:nowrap;
  font-size:11px;color:#F5E6C8;
  text-shadow:0 1px 4px rgba(0,0,0,0.9);
  pointer-events:none;
  transform:translate(-50%, -130%);
  background:rgba(0,0,0,0.55);
  padding:2px 6px;border-radius:8px;
  border:0.5px solid rgba(200,160,80,0.4);
}

/* HUD */
#hud{
  position:fixed;top:0;left:0;right:0;z-index:30;
  padding:12px 16px 8px;
  background:linear-gradient(to bottom,rgba(5,10,24,0.9),transparent);
  pointer-events:none;
}
#hud h1{
  color:#F5E6C8;font-size:13px;font-style:italic;
  text-align:center;letter-spacing:0.3px;
  text-shadow:0 1px 6px rgba(0,0,0,1);
}
#hud p{color:#C8A050;font-size:10px;text-align:center;margin-top:2px;letter-spacing:0.8px;}

#hint-bottom{
  position:fixed;bottom:16px;left:0;right:0;z-index:30;
  text-align:center;color:rgba(200,160,80,0.7);font-size:10px;
  letter-spacing:0.8px;pointer-events:none;
  animation:fadeout 4s ease 2.5s forwards;
}
@keyframes fadeout{to{opacity:0;}}

/* Panel cuento */
#overlay{
  position:fixed;inset:0;z-index:50;
  background:rgba(2,6,18,0);
  display:flex;align-items:center;justify-content:center;
  pointer-events:none;
  transition:background 0.4s;
}
#overlay.open{background:rgba(2,6,18,0.88);pointer-events:all;}

#panel{
  width:92%;max-width:370px;max-height:82vh;
  overflow-y:auto;-webkit-overflow-scrolling:touch;
  background:linear-gradient(150deg,#2A0E00,#110500);
  border:1.5px solid #C8A050;border-radius:18px;
  padding:22px 18px 18px;
  transform:translateY(40px) scale(0.92);opacity:0;
  transition:transform 0.45s cubic-bezier(0.34,1.56,0.64,1),opacity 0.35s;
}
#overlay.open #panel{transform:translateY(0) scale(1);opacity:1;}

#p-flag{font-size:46px;text-align:center;display:block;margin-bottom:2px;}
#p-name{color:#F5E6C8;font-size:21px;font-style:italic;text-align:center;
  letter-spacing:0.8px;text-shadow:0 0 16px rgba(200,160,80,0.5);}
#p-sub{color:#C8A050;font-size:12px;text-align:center;margin:4px 0 14px;font-style:italic;}
#p-line{border:none;border-top:1px solid rgba(200,160,80,0.35);margin:0 0 14px;}
#p-text{color:#E8D5A8;font-size:13.5px;line-height:1.85;text-align:justify;}
#p-text em{color:#F5C842;font-style:normal;font-weight:bold;}
#p-close{
  display:block;margin:18px auto 0;
  background:transparent;border:1px solid #C8A050;
  color:#C8A050;font-size:12px;font-family:Georgia,serif;
  padding:7px 26px;border-radius:30px;cursor:pointer;
  letter-spacing:0.8px;
}
#p-close:active{background:rgba(200,160,80,0.12);}
</style>
</head>
<body>

<canvas id="stars"></canvas>

<div id="hud">
  <h1>✦ Filtrando Ilusiones: Un Viaje por la Navidad del Mundo ✦</h1>
  <p>PULSA CADA PAÍS PARA DESCUBRIR SU CUENTO</p>
</div>

<div id="globe-wrap">
  <div id="globe">
    <div class="globe-layer" id="ocean"></div>
    <canvas class="globe-layer" id="continents"></canvas>
    <div class="globe-layer" id="shine"></div>
    <div id="atmosphere"></div>
  </div>
</div>

<div id="hint-bottom">Arrastra el globo con el dedo · Pulsa un país</div>

<div id="overlay">
  <div id="panel">
    <span id="p-flag"></span>
    <div id="p-name"></div>
    <div id="p-sub"></div>
    <hr id="p-line">
    <p id="p-text"></p>
    <button id="p-close" onclick="closePanel()">✦ Cerrar ✦</button>
  </div>
</div>

<script>
// ── DATOS ────────────────────────────────────────────────────────────────────
const DATA = [
  {name:"Finlandia", flag:"🇫🇮", color:"#4AAEDC",
   sub:"La carta que llegó al bosque nevado",
   lat:64, lon:26,
   text:`En un pequeño pueblo entre abetos cubiertos de nieve, <em>Liisa</em> escribió su carta a Joulupukki —el Papá Noel finlandés— y la dejó junto a la chimenea.\n\nEsa noche, las auroras boreales iluminaron el cielo y ella vio a lo lejos, entre los pinos, una luz que se movía. Al despertar, su regalo estaba allí: un trineo en miniatura tallado a mano.\n\nLiisa supo que <em>Joulupukki vive de verdad en Laponia</em>, y que los renos conocen cada nombre.`},
  {name:"Alemania", flag:"🇩🇪", color:"#6AB84A",
   sub:"El secreto del mercado de Núremberg",
   lat:51, lon:10,
   text:`Cada diciembre, <em>Klara</em> esperaba el momento de visitar el mercado navideño con su abuela. Entre el aroma de las galletas de jengibre y las luces de las casitas medievales, su abuela le confió un secreto.\n\n«Si cierras los ojos junto al árbol central, el espíritu del Adviento escucha tu deseo.»\n\nKlara lo hizo. Pidió que su familia estuviera siempre unida. Y ese año, <em>su tío que vivía lejos apareció de sorpresa en la puerta de casa</em>.`},
  {name:"España", flag:"🇪🇸", color:"#F5C842",
   sub:"La noche de los Reyes",
   lat:40, lon:-4,
   text:`<em>Carmen</em> no podía dormir. Era la noche del 5 de enero y había dejado sus zapatos en el balcón, llenos de paja para los camellos.\n\nSu abuelo le contó que Melchor, Gaspar y Baltasar viajaban desde muy lejos siguiendo la misma estrella de siempre, y que <em>si los niños se quedaban despiertos, los Reyes pasaban de largo</em>.\n\nA la mañana siguiente, los zapatos estaban llenos de caramelos y un pequeño libro con su nombre escrito en la portada. Los Reyes siempre saben.`},
  {name:"México", flag:"🇲🇽", color:"#E050A0",
   sub:"La última posada",
   lat:23, lon:-102,
   text:`Durante nueve noches, <em>Diego</em> y su familia habían ido de casa en casa cantando y pidiendo posada, como hicieron María y José.\n\nLa última noche, Diego cargaba la estrella de la piñata. Cuando por fin la puerta se abrió y los recibieron con ponche caliente y buñuelos, Diego sintió que ese momento era la Navidad de verdad.\n\nNo los regalos, sino el camino, el frío, la espera, y <em>la alegría de que al final siempre hay alguien que abre la puerta</em>.`},
  {name:"Brasil", flag:"🇧🇷", color:"#4ACA70",
   sub:"Papai Noel en la favela",
   lat:-15, lon:-47,
   text:`En Río de Janeiro, <em>Isabela</em> vivía en una favela donde las casas trepaban por el cerro como estrellas de colores. En Navidad, todo el barrio se llenaba de guirnaldas y música.\n\nUn niño nuevo preguntó si Papai Noel también llegaba allí. Isabela sonrió y señaló las luces en cada ventana:\n\n«¿Ves eso? Aquí no necesita trineo. <em>Sube a pie, como todos.</em>» Esa noche bailaron juntos hasta tarde. El regalo más grande fue el amigo nuevo.`},
  {name:"Etiopía", flag:"🇪🇹", color:"#E08830",
   sub:"La luz de Genna",
   lat:9, lon:38,
   text:`En Lalibela, <em>Dawit</em> se despertó antes del amanecer para la procesión del Genna —la Navidad etíope, <em>el 7 de enero</em>—.\n\nVestido de blanco, sostenía una vela junto a cientos de personas. Las iglesias de piedra brillaban en la oscuridad y el canto llenaba el aire frío de la montaña.\n\nSu abuela le dijo: «La luz que llevas en la mano es la misma que iluminó el mundo hace dos mil años.» <em>Dawit no olvidó esa noche en toda su vida.</em>`},
  {name:"Japón", flag:"🇯🇵", color:"#E84040",
   sub:"El cubo rojo de Tokio",
   lat:36, lon:138,
   text:`<em>Hana</em> no entendía por qué cada Navidad su familia pedía pollo frito en KFC, hasta que su abuelo le contó la historia.\n\nHace muchos años, un hombre vio en Tokio al Coronel Sanders vestido de Papá Noel y pensó: «Este hombre trae alegría.» Desde entonces, <em>el pollo navideño se convirtió en tradición</em>.\n\nEsa noche, bajo las luces de neón de Shibuya, Hana entendió que la magia no siempre llega del cielo: a veces llega en una caja roja y huele a especias.`},
  {name:"Australia", flag:"🇦🇺", color:"#F0B020",
   sub:"Papá Noel en la playa",
   lat:-25, lon:134,
   text:`En Sydney, <em>Tom</em> estaba convencido de que Papá Noel no podría llegar: hacía 35 grados y no había chimenea.\n\nPero su madre le explicó que en Australia, Santa cambia el trineo por una tabla de surf. El 25 de diciembre, mientras la familia hacía barbacoa en la playa, apareció un hombre con traje rojo y gorro playero, riendo a carcajadas.\n\nTom corrió descalzo por la arena y recibió su regalo: una caña de pescar. <em>El verano más feliz que recordaría.</em>`},
];

// ── ESTRELLAS ────────────────────────────────────────────────────────────────
(function(){
  const c=document.getElementById('stars');
  c.width=window.innerWidth; c.height=window.innerHeight;
  const ctx=c.getContext('2d');
  ctx.fillStyle='#050A18'; ctx.fillRect(0,0,c.width,c.height);
  for(let i=0;i<350;i++){
    const x=Math.random()*c.width, y=Math.random()*c.height;
    const r=Math.random()*1.4+0.2;
    const a=0.4+Math.random()*0.6;
    ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2);
    ctx.fillStyle=`rgba(255,252,220,${a})`; ctx.fill();
  }
})();

// ── CONTINENTES EN CANVAS ─────────────────────────────────────────────────────
const globeEl = document.getElementById('globe');
const cvs = document.getElementById('continents');

function resizeGlobe(){
  const sz = Math.min(window.innerWidth*0.82, window.innerHeight*0.82);
  globeEl.style.width = sz+'px';
  globeEl.style.height = sz+'px';
  cvs.width = sz; cvs.height = sz;
  drawContinents(rotX, rotY);
  placePins();
}

// Continentes como polígonos lat/lon
const CONTINENTS = [
  // América del Norte
  [[70,-140],[70,-60],[60,-55],[50,-55],[45,-60],[30,-80],[20,-87],[15,-85],[10,-83],[8,-77],[8,-75],[20,-105],[30,-110],[32,-117],[38,-122],[48,-124],[58,-137],[70,-140]],
  // Groenlandia
  [[76,-73],[83,-35],[76,-18],[72,-22],[68,-28],[68,-52],[72,-60],[76,-73]],
  // América del Sur
  [[10,-75],[10,-62],[5,-52],[0,-50],[-5,-35],[-10,-37],[-23,-43],[-33,-52],[-40,-62],[-55,-68],[-55,-64],[-40,-65],[-30,-50],[-20,-40],[-5,-35],[0,-48],[5,-60],[10,-75]],
  // Europa
  [[71,28],[70,20],[58,5],[51,2],[43,-9],[36,-6],[36,10],[40,18],[45,14],[44,28],[52,23],[55,22],[60,28],[65,25],[68,18],[71,28]],
  // Escandinavia
  [[71,28],[70,30],[68,28],[65,14],[57,8],[57,11],[60,11],[63,14],[68,18],[71,28]],
  // África
  [[37,10],[37,25],[30,33],[22,37],[12,42],[0,42],[-10,40],[-20,35],[-34,26],[-34,18],[-22,14],[-18,12],[-5,10],[0,8],[5,2],[4,-8],[5,-15],[15,-17],[22,-17],[30,-10],[37,10]],
  // Asia principal
  [[70,30],[72,60],[72,130],[60,140],[50,142],[40,130],[22,114],[10,104],[5,100],[5,95],[20,90],[22,88],[25,85],[28,77],[20,73],[22,60],[30,57],[25,55],[25,50],[30,48],[35,36],[42,28],[52,30],[60,30],[70,30]],
  // Península Arábiga
  [[30,48],[22,60],[12,45],[12,43],[15,42],[22,40],[28,34],[30,35],[30,48]],
  // Subcontinente indio
  [[28,77],[25,85],[22,88],[20,90],[8,77],[8,80],[12,80],[20,87],[22,88]],
  // Japón (simplificado)
  [[45,141],[43,141],[34,130],[31,130],[33,131],[35,136],[38,141],[40,141],[42,140],[45,141]],
  // Sureste asiático
  [[22,100],[20,100],[15,100],[10,99],[5,100],[5,103],[1,104],[5,100],[10,99],[15,102],[20,100],[22,100]],
  // Australia
  [[-15,130],[-12,136],[-12,142],[-20,148],[-38,146],[-38,140],[-32,134],[-32,126],[-22,114],[-20,116],[-15,130]],
  // Nueva Zelanda sur
  [[-46,168],[-40,172],[-34,173],[-46,168]],
];

let rotX = 0.3, rotY = -0.3;

function latLonTo2D(lat, lon, rX, rY, R){
  const phi = (90-lat)*Math.PI/180;
  const theta = (lon+180)*Math.PI/180;
  // 3D point on sphere
  let x = R*Math.sin(phi)*Math.cos(theta);
  let y = R*Math.cos(phi);
  let z = R*Math.sin(phi)*Math.sin(theta);
  // Rotate around Y axis (longitude rotation)
  const cosY=Math.cos(rY), sinY=Math.sin(rY);
  const x2 = x*cosY + z*sinY;
  const z2 = -x*sinY + z*cosY;
  // Rotate around X axis (tilt)
  const cosX=Math.cos(rX), sinX=Math.sin(rX);
  const y2 = y*cosX - z2*sinX;
  const z3 = y*sinX + z2*cosX;
  return {x:x2, y:y2, z:z3, visible: z3>0};
}

function drawContinents(rX, rY){
  const sz = cvs.width;
  const R = sz/2;
  const ctx = cvs.getContext('2d');
  ctx.clearRect(0,0,sz,sz);

  // Draw each continent
  CONTINENTS.forEach(poly=>{
    // Check if at least some points are visible
    const pts = poly.map(([lat,lon])=>latLonTo2D(lat,lon,rX,rY,R*0.97));
    const visibleCount = pts.filter(p=>p.z>0).length;
    if(visibleCount < poly.length*0.3) return;

    ctx.beginPath();
    let started=false;
    pts.forEach(p=>{
      if(p.z <= -R*0.1) return;
      const px = R + p.x;
      const py = R - p.y;
      if(!started){ctx.moveTo(px,py);started=true;}
      else ctx.lineTo(px,py);
    });
    ctx.closePath();
    // Continent color with depth shading
    ctx.fillStyle='#C8A050';
    ctx.strokeStyle='#8B6914';
    ctx.lineWidth=0.8;
    ctx.fill();
    ctx.stroke();
  });
}

// ── PINS ──────────────────────────────────────────────────────────────────────
let pins=[];

function placePins(){
  // Remove old pins
  pins.forEach(p=>{p.el&&p.el.remove();});
  pins=[];
  const sz = globeEl.offsetWidth;
  const R = sz/2;

  DATA.forEach((d,i)=>{
    const p = latLonTo2D(d.lat, d.lon, rotX, rotY, R*0.97);
    if(p.z < 0) return; // behind globe

    const pin = document.createElement('div');
    pin.className='country-pin';
    pin.style.cssText=`
      left:${R+p.x}px; top:${R-p.y}px;
      background:${d.color};
      color:${d.color};
    `;
    // Scale by depth
    const scale = 0.7 + 0.5*(p.z/R);
    pin.style.transform=`translate(-50%,-50%) scale(${scale.toFixed(2)})`;

    // Label
    const lbl=document.createElement('div');
    lbl.className='pin-label';
    lbl.textContent=d.flag+' '+d.name;
    pin.appendChild(lbl);

    pin.addEventListener('click',e=>{e.stopPropagation();openPanel(i);});
    pin.addEventListener('touchend',e=>{e.preventDefault();e.stopPropagation();openPanel(i);},{passive:false});

    globeEl.appendChild(pin);
    pins.push({el:pin, data:d});
  });
}

// ── DRAG ROTATION ─────────────────────────────────────────────────────────────
let dragging=false, lastX=0, lastY=0, velX=0, velY=0;
let autoRotate=true;
let tapX=0,tapY=0;

globeEl.addEventListener('mousedown',e=>{
  dragging=true;lastX=e.clientX;lastY=e.clientY;
  velX=0;velY=0;autoRotate=false;
  tapX=e.clientX;tapY=e.clientY;
});
window.addEventListener('mousemove',e=>{
  if(!dragging)return;
  const dx=e.clientX-lastX, dy=e.clientY-lastY;
  rotY+=dx*0.008; rotX+=dy*0.008;
  rotX=Math.max(-1.2,Math.min(1.2,rotX));
  velX=dy*0.006; velY=dx*0.006;
  lastX=e.clientX;lastY=e.clientY;
  drawContinents(rotX,rotY);placePins();
});
window.addEventListener('mouseup',e=>{
  dragging=false;
  if(Math.abs(e.clientX-tapX)<6&&Math.abs(e.clientY-tapY)<6) return;
  setTimeout(()=>{autoRotate=true;},1500);
});

globeEl.addEventListener('touchstart',e=>{
  dragging=true;
  const t=e.touches[0];
  lastX=t.clientX;lastY=t.clientY;
  tapX=t.clientX;tapY=t.clientY;
  velX=0;velY=0;autoRotate=false;
},{passive:true});
window.addEventListener('touchmove',e=>{
  if(!dragging||e.touches.length!==1)return;
  const t=e.touches[0];
  const dx=t.clientX-lastX, dy=t.clientY-lastY;
  rotY+=dx*0.008; rotX+=dy*0.008;
  rotX=Math.max(-1.2,Math.min(1.2,rotX));
  velX=dy*0.005; velY=dx*0.005;
  lastX=t.clientX;lastY=t.clientY;
  drawContinents(rotX,rotY);placePins();
},{passive:true});
window.addEventListener('touchend',e=>{
  dragging=false;
  const t=e.changedTouches[0];
  if(Math.abs(t.clientX-tapX)<10&&Math.abs(t.clientY-tapY)<10) return;
  setTimeout(()=>{autoRotate=true;},1500);
});

// ── ANIMACIÓN ──────────────────────────────────────────────────────────────────
let lastTime=0;
function animate(ts){
  requestAnimationFrame(animate);
  const dt=Math.min((ts-lastTime)/16,3); lastTime=ts;
  if(!dragging){
    if(autoRotate) rotY+=0.004*dt;
    // inercia
    if(!autoRotate&&(Math.abs(velX)>0.0005||Math.abs(velY)>0.0005)){
      rotX+=velX*dt; rotY+=velY*dt;
      velX*=0.92; velY*=0.92;
      rotX=Math.max(-1.2,Math.min(1.2,rotX));
      drawContinents(rotX,rotY);placePins();
    } else if(autoRotate){
      drawContinents(rotX,rotY);placePins();
    }
  }
}
requestAnimationFrame(animate);

// ── PANEL CUENTO ──────────────────────────────────────────────────────────────
function openPanel(i){
  const d=DATA[i];
  document.getElementById('p-flag').textContent=d.flag;
  document.getElementById('p-name').textContent=d.name;
  document.getElementById('p-sub').textContent=d.sub;
  document.getElementById('p-text').innerHTML=d.text.replace(/\n\n/g,'<br><br>');
  document.getElementById('overlay').classList.add('open');
  autoRotate=false;
}
function closePanel(){
  document.getElementById('overlay').classList.remove('open');
  setTimeout(()=>{autoRotate=true;},600);
}
document.getElementById('overlay').addEventListener('click',function(e){
  if(e.target===this) closePanel();
});

// Init
resizeGlobe();
window.addEventListener('resize',resizeGlobe);
</script>
</body>
</html>
