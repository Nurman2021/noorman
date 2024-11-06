<script>
	import '../app.css';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
	import { RoomEnvironment } from 'three/examples/jsm/environments/RoomEnvironment';
	import { onMount } from 'svelte';

	let mixer;
	let animations = [];
	let activeAnimationIndex = 0;
	let controls;
	let clock;

    function playWithTransition(index, nextAnimationIndex = null, transitionDuration = 0.5) {
        // Cek jika mixer belum ada, mulai animasi default (index 0)
        if (!mixer) {
            console.warn("Mixer undefined, playing default animation at index 0.");
            index = 0;
        }

        if (mixer && animations.length > index) {
            const currentAction = mixer.clipAction(animations[index]);
            const nextAction = nextAnimationIndex !== null ? mixer.clipAction(animations[nextAnimationIndex]) : null;

            mixer.stopAllAction();
            currentAction.reset().play();

            if (nextAction) {
                currentAction.crossFadeTo(nextAction, transitionDuration, false);
                setTimeout(() => {
                    nextAction.reset().play();
                }, currentAction.getClip().duration * 1000 - transitionDuration * 1000);
            }
        } else {
            console.warn(`Animation index ${index} is out of range or mixer is undefined.`);
        }
    }

	// function playAnimation(index) {
	// 	if (mixer && animations.length > index) {
	// 		mixer.stopAllAction();
	// 		mixer.clipAction(animations[index]).play();
	// 	}
	// }

  function handleSectionChange(sectionIndex) {
        activeAnimationIndex = sectionIndex % animations.length;

        // Jika animasi adalah "attack" (misalnya index 1), transisi kembali ke "idle" setelahnya
        // if (activeAnimationIndex === 1) {
            // playWithTransition(activeAnimationIndex, 0); // Transisi ke "idle" setelah "attack"
        // } else {
            playWithTransition(activeAnimationIndex);
        // }
        console.log('active index =>', activeAnimationIndex);
    }

	onMount(() => {
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(
			75,
			window.innerWidth / window.innerHeight,
			0.1,
			1000
		);
		camera.position.z = 5;
		camera.lookAt(60, 3.5, 0);

		clock = new THREE.Clock();

		const renderer = new THREE.WebGLRenderer({
			alpha: true,
			canvas: document.querySelector('#bg')
		});
		renderer.outputEncoding = THREE.sRGBEncoding;
		renderer.toneMapping = THREE.ACESFilmicToneMapping;
		renderer.toneMappingExposure = 1.5;
		renderer.setSize(window.innerWidth, window.innerHeight);

		const pmremGenerator = new THREE.PMREMGenerator(renderer);
		scene.environment = pmremGenerator.fromScene(new RoomEnvironment(), 0.04).texture;

		const axesHelper = new THREE.AxesHelper(5);
		scene.add(axesHelper);

		// Load the GLB model
		const loader = new GLTFLoader();
		loader.load('/model/lord.glb', (gltf) => {
			const model = gltf.scene;
			model.position.set(0.3, -1.3, 0);

			scene.add(model);
			console.log(gltf);

			animations = gltf.animations;
			if (animations.length > 0) {
				mixer = new THREE.AnimationMixer(gltf.scene);

				playWithTransition(0);
			}

			animate();
		});

		// orbitControls
		controls = new OrbitControls(camera, renderer.domElement);
		controls.enableDamping = true;
		controls.dampingFactor = 0.25;

		// lighting
		const ambientLight = new THREE.AmbientLight(0xffffff, 0.3);
		scene.add(ambientLight);

		const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
		directionalLight.position.set(5, 10, 7.5);
		directionalLight.castShadow = true;
		directionalLight.shadow.mapSize.width = 1024;
		directionalLight.shadow.mapSize.height = 1024;
		directionalLight.shadow.camera.near = 0.5;
		directionalLight.shadow.camera.far = 50;
		scene.add(directionalLight);

		const pointlight = new THREE.PointLight(0xffffff, 1, 50);
		pointlight.position.set(0, 10, 10);
		scene.add(pointlight);

		const hemiLight = new THREE.HemisphereLight(0xaaaaaa, 0x444444, 0.6);
		hemiLight.position.set(0, 10, 0);
		scene.add(hemiLight);

		// Update animation frame
		function animate() {
			requestAnimationFrame(animate);
			if (mixer) mixer.update(clock.getDelta());

			controls.update();
			renderer.render(scene, camera);
		}

		return () => {
			window.removeEventListener('resize', onresize);
			renderer.dispose();
			controls.dispose();
		};
	});
</script>




<canvas id="bg" class="fixed z-10 h-screen"></canvas>
<main class="absolute top-0 z-20">

<span>animasi yang bisa ==> 2, 0,3(sedikit), 4(sedikit), 5(skill), 6,8,9,10,11<span/> 

	<h1 class=" text-xl text-red-500">ini bagian content</h1>
	<section
		on:click={() => handleSectionChange(0)}
		role="button"
		tabindex="0"
		on:keydown={() => handleSectionChange(0)}
	>
		About
	</section>
	<section
		on:click={() => handleSectionChange(1)}
		role="button"
		tabindex="0"
		on:keydown={() => handleSectionChange(1)}
	>
		Experience
	</section>
	<section
		on:click={() => handleSectionChange(6)}
		role="button"
		tabindex="0"
		on:keydown={() => handleSectionChange(6)}
	>
		Projects
	</section>
	<section
		on:click={() => handleSectionChange(12)}
		role="button"
		tabindex="0"
		on:keydown={() => handleSectionChange(12)}
	>
		Contact
	</section>
</main>

<style>
</style>
