<script setup>
    import { BoxGeometry, WebGLRenderer, PerspectiveCamera, Scene, Mesh, PointLight, MeshStandardMaterial } from 'three';
    import { ref, onMounted } from 'vue';

    const myCanvas = ref(null);
    
    const scene = new Scene();

    const camera = new PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(5, 5, 2);
    scene.add(camera);

    // Used for testing
    // IMPORTANT: Remember to import the components before using code below
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

    onMounted(async () => {
        renderer = new WebGLRenderer({
            canvas: myCanvas.value,
            antialias: true
        });
        renderer.setSize(window.innerWidth, window.innerHeight);

        // Dynamic import for the OrbitControls class
        // Since it should be used only after everything else has been loaded in
        const { OrbitControls } = await import('three/examples/jsm/controls/OrbitControls');

        controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.enablePan = false;
        controls.enableZoom = false;
        controls.autoRotate = true;
        controls.autoRotateSpeed = 5;

        animateScene();

        window.addEventListener('resize', updateOnResize);

        // Dynamic import for the gsap object
        // Used to animate parts of the page
        const { gsap } = await import('gsap');

        const tl = gsap.timeline({
            defaults: { duration: 1 }
        });

        tl.fromTo(cube.scale, { x: 0, y: 0, z: 0 }, { x: 1, y: 1, z: 1 });
        tl.fromTo('.navbar', { y: "-100%" }, { y:"0%" });
        tl.fromTo('.title', { opacity: 0 }, { opacity: 1 });
    });
</script>

<template>
    <canvas id="myCanvas" ref="myCanvas"></canvas>
    <nav class="navbar">
        <a href="#">Cube</a>
        <ul>
            <li>Explore</li>
            <li>Create</li>
        </ul>
    </nav>
    <h1 class="title">Give it a spin...</h1>
</template>

<style scoped>
    #myCanvas {
        position: fixed;
        inset: 0;
        height: 100%;
        z-index: 1;
    }

    .navbar {
        color: var(--text-color);
        z-index: 2;
        position: relative;
        padding: 4rem 8rem;
        display: flex;
        justify-content: space-between;
        transition: border padding .3s ease;
    }

    @media screen and (max-width: 720px) {
        .navbar {
            flex-direction: column;
            align-items: center;
            gap: .5rem
        }
    }

    .navbar > a {
        text-decoration: none;
        font-weight: bold;
        color: var(--text-color);
        font-size: 2.5rem;
    }

    .navbar > ul {
        list-style: none;
        display: flex;
        align-items: center;
        gap: 4rem;
    }

    .navbar > ul > li {
        cursor: pointer;
        font-size: 1.3rem;
    }

    .navbar > ul > li:hover {
        border-bottom: 1px solid var(--text-color);
        padding-bottom: .1rem;
    }

    .title {
        color: var(--text-color);
        font-size: 3rem;
        z-index: 2;
        position: absolute;
        top: 80%;
        left: 50%;
        transform: translate(-50%, -80%);
    }
</style>