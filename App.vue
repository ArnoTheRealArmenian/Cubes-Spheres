<template>
  <div>
    <canvas id="c"></canvas>

    <canvas id="s"></canvas>
  </div>
</template>
<script>
import * as THREE from '../node_modules/three/build/three.module.js';

export default {
  name: 'App',
  components: {},

  mounted() {
    main();
    main2();
  }
}

const cubes = [];

// THE CUBE
function main() {
  const canvas = document.querySelector('#c');
  canvas.width  = canvas.clientWidth;
  canvas.height = canvas.clientHeight;
  const renderer = new THREE.WebGLRenderer({canvas});
  renderer.setViewport(0, 0, canvas.clientWidth, canvas.clientHeight);

  const fov = 75;
  const aspect = canvas.height/canvas.width;  // the canvas default
  const near = 0.1;
  const far = 25;
  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
  camera.position.z = -3;
  camera.position.y = 6;
  // camera.rotation.x = -Math.PI / 6;

  const scene = new THREE.Scene();
  const groundTexture = new THREE.TextureLoader().load("./assets/grassbackground.png"); // loads the images
  groundTexture.wrapS = groundTexture.wrapT = THREE.RepeatWrapping; // sets the repeat
  groundTexture.repeat.set( 10000, 10000 ); // repeats the image
  groundTexture.anisotropy = 16; // the more the value is little, the worse the texture is seen in the long distance
  groundTexture.encoding = THREE.sRGBEncoding; // converts the final color in the fragment shader

  const groundMaterial = new THREE.MeshStandardMaterial({map: groundTexture});

  const mesh = new THREE.Mesh(new THREE.PlaneBufferGeometry(10000, 10000), groundMaterial);
  mesh.position.y = 0;
  mesh.rotation.x = - Math.PI / 2;
  mesh.receiveShadow = true;
  scene.add(mesh);
  {
    const color = 0xFFFFFF;
    const intensity = 1;
    const light = new THREE.DirectionalLight(color, intensity);
    light.position.set(0, 1, 1);
    scene.add(light);
  }

  for(let i = 0; i < 10; i++) {
    const aaa = getRandomInt(0.5, 1.5);

    const cube = cGenerator({X:aaa,Y:aaa, Z:aaa}, {X:getRandomInt(-5, 10),Y:.8,Z:getRandomInt(-5, 10)},getRandomColor());
    scene.add(cube);
    cubes.push(cube);
  }

  canvas.addEventListener("mousemove", function (event) {
    if (event.movementX > 0) {
      camera.rotation.y -= 0.005 / 2*Math.PI;
    }
    else {
      camera.rotation.y += 0.005 / 2*Math.PI;
    }
    if (event.movementY > 0) {
      camera.rotation.x -= 0.005 / 2*Math.PI;
    }
    else {
      camera.rotation.x += 0.005 / 2*Math.PI;
    }
    render();
  });

  window.addEventListener("keypress", function (event) {
    if (event.key === " ") {
      camera.position.y += 0.05;
      return;
    }
    if (event.key === "a") {
      camera.position.x -= 0.05;
      return;
    }
    if (event.key === "d") {
      camera.position.x += 0.05;
    }
    if (event.key === "w") {
      camera.position.z -= 0.05;
    }
    if (event.key === "s") {
      camera.position.z += 0.05;
    }
  });

  window.addEventListener("keydown", function (event) {
    if (event.shiftKey) {
      camera.position.y -= 0.05;
    }
  });

  function render(time) {
    time *= 0.0005;
    cubes.forEach(cube => {
      cube.rotation.x = time;
      cube.rotation.y = time;
    });

    renderer.render(scene, camera);

    requestAnimationFrame(render);
  }
  requestAnimationFrame(render);
}

function cGenerator(sizes, positions, color) {
  console.log(color);
  const geometry = new THREE.BoxGeometry(sizes.X, sizes.Y, sizes.Z);
  const material = new THREE.MeshPhongMaterial({color});  // sussy color
  const cube = new THREE.Mesh(geometry, material);

  cube.position.x = positions.X;
  cube.position.y = positions.Y;
  cube.position.z = positions.Z;

  return cube;
}

function getRandomColor() {
  const letters = '0123456789ABCDEF';
  let color = '#';
  for (let i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
}
getRandomColor();

function getRandomInt(min, max) {
  return (Math.random() * max) - min;
}
// THE SPHERE
function main2() {
  const canvas = document.querySelector('#s');
  canvas.width = canvas.clientWidth;
  canvas.height = canvas.clientHeight;
  const renderer = new THREE.WebGLRenderer({canvas});
  renderer.setViewport(0, 0, canvas.clientWidth, canvas.clientHeight);

  const fov = 75;
  const aspect = canvas.height/canvas.width;
  const near = 0.1;
  const far = 5;

  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);

  camera.position.z = 3;

  const scene = new THREE.Scene();
  const geometry = new THREE.SphereGeometry(1, 150, 150); // radius,
  const material = new THREE.MeshPhongMaterial({color: '#eaf4fc'});
  const sphere = new THREE.Mesh(geometry, material);

  {
    const color = 0xFFFFFFF;
    const intensity = 1;
    const light = new THREE.DirectionalLight(color, intensity);
    light.position.set(3, 2, 3);
    scene.add(light);
  }

  function render(time) {
    time *= 0.0005;

    sphere.rotation.x = time;
    sphere.rotation.y = time;

    renderer.render(scene, camera);

    requestAnimationFrame(render);
  }
  requestAnimationFrame(render);

  scene.add(sphere);
  renderer.render(scene, camera);
}
</script>

<style>
  #c {
    width: 747px;
    height: 729px;
    margin-right: 9px;
  }

  #s {
    width: 747px;
    height: 729px;
  }
</style>
