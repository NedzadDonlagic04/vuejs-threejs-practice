<script setup>
    import { MeshBasicMaterial, BoxGeometry, WebGLRenderer, PerspectiveCamera, Scene, Mesh, CameraHelper, AxesHelper } from 'three';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
    import { ref, onMounted } from 'vue';

    const myCanvas = ref(null);
    
    const scene = new Scene();

    const camera = new PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.z = 5;
    scene.add(camera);

    const cameraHelper = new CameraHelper(camera);
    scene.add(cameraHelper);

    const axesHelper = new AxesHelper(5);
    scene.add(axesHelper);

    const geometry = new BoxGeometry(1, 1, 1);
	const material = new MeshBasicMaterial({ color: 0x008080 });
	const cube = new Mesh(geometry, material);
	scene.add(cube);

    let renderer, controls;

    const updateOnResize = () => {
        camera.aspect = window.innerWidth / window.innerHeight;
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