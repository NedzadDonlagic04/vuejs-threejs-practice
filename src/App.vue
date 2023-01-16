<script setup>
    import { MeshBasicMaterial, BoxGeometry, WebGLRenderer, PerspectiveCamera, Scene, Mesh, CameraHelper, AxesHelper, PointLight, MeshStandardMaterial } from 'three';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
    import { ref, onMounted } from 'vue';

    const myCanvas = ref(null);
    
    const scene = new Scene();

    const camera = new PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(5, 5, 2);
    scene.add(camera);

    // Used for testing
    /*  
    const cameraHelper = new CameraHelper(camera);
    scene.add(cameraHelper);

    const axesHelper = new AxesHelper(5);
    scene.add(axesHelper); 
    */

    const light = new PointLight(0xffffff, 1, 100);
    light.position.set(0, 5, 5);
    scene.add(light);

    const cubeGeometry = new BoxGeometry(1, 1, 1);
	const cubeMaterial = new MeshStandardMaterial({ color: 0x00ff83 });
	const cube = new Mesh(cubeGeometry, cubeMaterial);
	scene.add(cube);

    let renderer, controls;

    const updateOnResize = () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    const animateScene = () => {
        requestAnimationFrame(animateScene);

        controls.update();

        renderer.render(scene, camera);
    }

    onMounted(() => {
        renderer = new WebGLRenderer({
            canvas: myCanvas.value,
            antialias: true
        });
        renderer.setSize(window.innerWidth, window.innerHeight);

        controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.enablePan = false;
        controls.enableZoom = false;
        controls.autoRotate = true;
        controls.autoRotateSpeed = 5;

        animateScene();

        window.addEventListener('resize', updateOnResize);
    });
</script>

<template>
    <canvas id="myCanvas" ref="myCanvas"></canvas>
</template>

<style scoped>
    #myCanvas {
        position: fixed;
        inset: 0;
        height: 100%;
    }
</style>