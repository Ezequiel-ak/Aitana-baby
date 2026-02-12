# Aitana-baby
Mi universo 
<aitana💕>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Universo de Luna Aitana</title>

<style>
body { margin:0; overflow:hidden; background:black; font-family:Arial; }
#intro {
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
button {
  padding:15px 30px;
  font-size:18px;
  border:none;
  border-radius:30px;
  background:linear-gradient(45deg,#ff6a00,#ff0000);
  color:white;
  cursor:pointer;
}
</style>
</head>
<body>

<div id="intro">
  <h1>¿Quieres conocer mi universo?</h1>
  <button onclick="startUniverse()">Tocar para abrir</button>
</div>

<audio id="music" src="musica.mp3"></audio>

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/examples/js/controls/OrbitControls.js"></script>

<script>

let scene, camera, renderer, controls;
let planets = [];
let textMesh;
let introStars;

function startUniverse() {

  document.getElementById("intro").style.display="none";
  document.getElementById("music").play();

  scene = new THREE.Scene();

  camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 10000);
  camera.position.set(0,0,2000);

  renderer = new THREE.WebGLRenderer({antialias:true});
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.3;
  document.body.appendChild(renderer.domElement);

  controls = new THREE.OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.minDistance = 200;
  controls.maxDistance = 5000;

  createStarField();
  createBlackHole();
  createPlanets();
  createText();

  animateIntro();
}

function createStarField(){
  const geometry = new THREE.BufferGeometry();
  const vertices = [];
  for(let i=0;i<20000;i++){
    vertices.push(
      THREE.MathUtils.randFloatSpread(8000),
      THREE.MathUtils.randFloatSpread(8000),
      THREE.MathUtils.randFloatSpread(8000)
    );
  }
  geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices,3));
  const material = new THREE.PointsMaterial({color:0xffffff,size:1});
  introStars = new THREE.Points(geometry, material);
  scene.add(introStars);
}

function createBlackHole(){

  // Núcleo negro
  const coreGeo = new THREE.SphereGeometry(200,64,64);
  const coreMat = new THREE.MeshBasicMaterial({color:0x000000});
  const core = new THREE.Mesh(coreGeo, coreMat);
  scene.add(core);

  // Disco fuego
  const ringGeo = new THREE.TorusGeometry(350,60,32,200);
  const ringMat = new THREE.MeshBasicMaterial({color:0xff5500});
  const ring = new THREE.Mesh(ringGeo, ringMat);
  ring.rotation.x = Math.PI/2;
  ring.name="accretionDisk";
  scene.add(ring);

  // Luz intensa
  const light = new THREE.PointLight(0xffaa55,5,3000);
  scene.add(light);
}

function createPlanets(){

  const loader = new THREE.TextureLoader();

  for(let i=1;i<=9;i++){

    const texture = loader.load("foto"+i+".jpg");
    const geo = new THREE.SphereGeometry(60,64,64);
    const mat = new THREE.MeshStandardMaterial({map:texture});
    const planet = new THREE.Mesh(geo,mat);

    planet.userData = {
      angle: Math.random()*Math.PI*2,
      distance: 800 + (i*250),
      speed: 0.0005 + (i*0.0001)
    };

    scene.add(planet);
    planets.push(planet);
  }
}

function createText(){

  const loader = new THREE.FontLoader();
  loader.load('https://threejs.org/examples/fonts/helvetiker_regular.typeface.json',function(font){

    const geo = new THREE.TextGeometry("Luna Aitana",{
      font:font,
      size:80,
      height:10
    });

    const mat = new THREE.MeshBasicMaterial({color:0xffffff});
    textMesh = new THREE.Mesh(geo,mat);
    scene.add(textMesh);
  });
}

function animateIntro(){

  let speed = 40;

  function fly(){
    if(camera.position.z > 600){
      camera.position.z -= speed;
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

  introStars.rotation.y += 0.0003;

  const disk = scene.getObjectByName("accretionDisk");
  if(disk) disk.rotation.z += 0.02;

  planets.forEach(p=>{
    p.userData.angle += p.userData.speed;
    p.position.x = Math.cos(p.userData.angle)*p.userData.distance;
    p.position.z = Math.sin(p.userData.angle)*p.userData.distance;
  });

  if(textMesh){
    textMesh.rotation.y += 0.01;
    textMesh.position.x = Math.cos(Date.now()*0.0005)*500;
    textMesh.position.z = Math.sin(Date.now()*0.0005)*500;
  }

  controls.update();
  renderer.render(scene,camera);
}

window.addEventListener('resize',()=>{
  camera.aspect = window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});

</script>
</body>
</html>
