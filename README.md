# Aitana-baby
Mi universo 
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Universo Luna Aitana</title>
<style>
body{margin:0;overflow:hidden;background:black;font-family:Arial}
#intro{
position:absolute;
width:100%;
height:100%;
display:flex;
flex-direction:column;
justify-content:center;
align-items:center;
background:black;
color:white;
z-index:10;
}
button{
padding:15px 30px;
font-size:18px;
border:none;
border-radius:30px;
background:linear-gradient(45deg,#ff6a00,#ff0000);
color:white;
cursor:pointer;
}
#name{
position:absolute;
top:20px;
width:100%;
text-align:center;
font-size:24px;
color:white;
display:none;
z-index:5;
}
</style>
</head>
<body>

<div id="intro">
<h1>¿Quieres conocer mi universo?</h1>
<button onclick="startUniverse()">Tocar para abrir</button>
</div>

<div id="name">Luna Aitana</div>

<audio id="music" src="musica.mp3"></audio>

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>

<script>

let scene,camera,renderer,planets=[];
let starField;

function startUniverse(){

document.getElementById("intro").style.display="none";
document.getElementById("name").style.display="block";
document.getElementById("music").play();

scene=new THREE.Scene();

camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,10000);
camera.position.z=2000;

renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

createStars();
createBlackHole();
createPlanets();

animateIntro();
}

function createStars(){
const geometry=new THREE.BufferGeometry();
const vertices=[];
for(let i=0;i<15000;i++){
vertices.push(
THREE.MathUtils.randFloatSpread(8000),
THREE.MathUtils.randFloatSpread(8000),
THREE.MathUtils.randFloatSpread(8000)
);
}
geometry.setAttribute('position',new THREE.Float32BufferAttribute(vertices,3));
const material=new THREE.PointsMaterial({color:0xffffff,size:1});
starField=new THREE.Points(geometry,material);
scene.add(starField);
}

function createBlackHole(){
const coreGeo=new THREE.SphereGeometry(250,64,64);
const coreMat=new THREE.MeshBasicMaterial({color:0x000000});
const core=new THREE.Mesh(coreGeo,coreMat);
scene.add(core);

const ringGeo=new THREE.TorusGeometry(400,80,32,200);
const ringMat=new THREE.MeshBasicMaterial({color:0xff5500});
const ring=new THREE.Mesh(ringGeo,ringMat);
ring.rotation.x=Math.PI/2;
ring.name="disk";
scene.add(ring);
}

function createPlanets(){
const loader=new THREE.TextureLoader();
for(let i=1;i<=9;i++){
const texture=loader.load("foto"+i+".jpg");
const geo=new THREE.SphereGeometry(70,64,64);
const mat=new THREE.MeshStandardMaterial({map:texture});
const planet=new THREE.Mesh(geo,mat);
planet.userData={
angle:Math.random()*Math.PI*2,
distance:1000+(i*300),
speed:0.0005+(i*0.0001)
};
scene.add(planet);
planets.push(planet);
}
const light=new THREE.PointLight(0xffaa55,3,5000);
scene.add(light);
}

function animateIntro(){
let speed=30;
function fly(){
if(camera.position.z>800){
camera.position.z-=speed;
requestAnimationFrame(fly);
renderer.render(scene,camera);
}else{
animate();
}
}
fly();
}

function animate(){
requestAnimationFrame(animate);

starField.rotation.y+=0.0002;

const disk=scene.getObjectByName("disk");
if(disk)disk.rotation.z+=0.02;

planets.forEach(p=>{
p.userData.angle+=p.userData.speed;
p.position.x=Math.cos(p.userData.angle)*p.userData.distance;
p.position.z=Math.sin(p.userData.angle)*p.userData.distance;
});

renderer.render(scene,camera);
}

window.addEventListener("resize",()=>{
camera.aspect=window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

</script>
</body>
</html>
