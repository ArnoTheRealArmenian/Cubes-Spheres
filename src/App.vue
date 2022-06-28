<template>
  <div style="overflow: hidden">
    <canvas id="c"></canvas>
  </div>
</template>
<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
// import { FirstPersonControls } from 'three/examples/jsm/controls/FirstPersonControls.js';
import { PointerLockControls } from 'three/examples/jsm/controls/PointerLockControls.js';

export default {
  name: 'App',
  components: {},

  mounted() {
    main();
  }
}

// let prevTime = performance.now();
// const velocity = new THREE.Vector3();

// La Lamborghini
function main() {
  const canvas = document.querySelector('#c');
  const renderer = new THREE.WebGLRenderer({canvas});
  renderer.setSize(window.innerWidth, window.innerHeight);

  renderer.setClearColor(0x87ceeb);

  const fov = 70;
  const aspect = window.innerWidth/window.innerHeight;  // the canvas default
  const near = 0.1;
  const far = 100;
  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
  camera.position.set(-4, 5, 4);

  const scene = new THREE.Scene();

  const groundTexture = new THREE.TextureLoader().load("./assets/grassbackground2.png"); // loads the images
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
    const light = new THREE.PointLight(color, intensity);
    light.position.set(-3.5, 3, -12);
    scene.add(light);
  }

  let loader = new GLTFLoader();
  loader.load('./assets/lamborghini_aventador_svj_sdc__free/scene.gltf', function(gltf) {
    const car = gltf.scene.children[0];
    car.scale.set(1, 1, 1);
    car.position.set(-3.5, 1.2, -14);
    scene.add(gltf.scene);
  });

  loader.load('./assets/voice_stage/scene.gltf', function(gltf) {
    const stage = gltf.scene.children[0];
    stage.scale.set(0.01, 0.01, 0.01);
    stage.position.set(0, 2.8, 0);
    scene.add(gltf.scene);
  });

  // const controls = new FirstPersonControls(camera);
  // scene.add(controls.object);

  const controls1 = new PointerLockControls(camera);
  scene.add(controls1.object);


  window.addEventListener('click', function () {

    controls1.lock();
    canvas.EventListener("mousemove", (event) => {
      let scale = 1;
      let mouseX = 0;
      let mouseY = 0;
      camera.rotation.order = "YXZ"; // this is not the default (it changes x to y and y to x)
      mouseX = - (event.clientX / canvas.clientWidth) * 2 + 1; // +1 sets your camera at an angle
      mouseY = - (event.clientY / canvas.clientHeight) * 2 + 1; // *2 is the sensitivity

      camera.rotation.x = mouseY / scale; // the scale plays are role on the sensitivity as well
      camera.rotation.y = mouseX / scale;
      if (event === "Escape") {
        window.removeEventListener('mousemove');
      }
    });

  });

  window.addEventListener('resize', function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix(); // updates camera aspect ratio with window aspect ratio

    renderer.setSize(window.innerWidth, window.innerHeight);
  });

  function animate() {
    requestAnimationFrame(animate);
    // playerControls();
    renderer.render(scene, camera);

  }
  requestAnimationFrame(animate);
  // const time = performance.now();
  // let delta = (time - prevTime) / 1000;
  const onKeyDown = function (event) {
    switch (event.code) {
      case 'KeyW':
        controls1.moveForward(0.25);
        break
      case 'KeyA':
        controls1.moveRight(-0.25)
        break
      case 'KeyS':
        controls1.moveForward(-0.25)
        break
      case 'KeyD':
        controls1.moveRight(0.25)
        break
    }
    if (event.shiftKey) {
      camera.position.y -= 0.25;
    }
  }
  window.addEventListener('keydown', onKeyDown, false);

  window.addEventListener("keypress", function(event) {
    if (event.key === " ") {
      camera.position.y += 0.25;
    }

  });
  /*function playerControls() {
    // Are the controls enabled from the browser ?
    if (controls.enabled) {
      const time = performance.now();
      let delta = (time - prevTime) / 1000;
      // Set velocity.x and .z using the calculated time delta
      velocity.x -= velocity.x * 10 * delta;
      velocity.z -= velocity.z * 10 * delta;
      // As velocity.y is gravity
      velocity.y -= 9.81 * 100 * delta; // 100 = mass

      if (controls.moveForward) {
        velocity.z -= 100 * delta
      }

      if (controls.moveBackward) {
        velocity.z += 100 * delta;
      }

      if (controls.moveLeft) {
        velocity.x -= 100 * delta;
      }

      if (controls.moveRight) {
        velocity.x += 100 * delta;
      }

      //  Update the position using the changed delta
      controls.object.translateX(velocity.x * delta);
      controls.object.translateY(velocity.y * delta);
      controls.object.translateZ(velocity.z * delta);

      // Prevent the camera from falling out of the world
      if (controls.object.position.y < 10) {
        velocity.y = 0;
        controls.object.position.y = 1;
      }

      // Save the time for future delta calculations
      prevTime = time;
    }

  }*/

}

</script>

<style>
</style>
