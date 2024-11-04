<script>
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
	import { RoomEnvironment } from 'three/examples/jsm/environments/RoomEnvironment';
	import { onMount } from 'svelte';

	let mixer;
	let animations = [];
	let activeAnimationIndex = 0;
	let controls;

	function playAnimation(index) {
		if (mixer && animations.length > index) {
			mixer.stopAllAction();
			mixer.clipAction(animations[index]).play();
		}
	}

	function handleSectionChange(sectionIndex) {
		activeAnimationIndex = sectionIndex % animations.length;
		playAnimation(activeAnimationIndex);
		console.log('active index=>', activeAnimationIndex);
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

		const renderer = new THREE.WebGLRenderer({ alpha: true });
		renderer.outputEncoding = THREE.sRGBEncoding;
		renderer.toneMapping = THREE.ACESFilmicToneMapping;
		renderer.toneMappingExposure = 1.5;
		renderer.setSize(window.innerWidth, window.innerHeight);
		document.body.appendChild(renderer.domElement);

		const pmremGenerator = new THREE.PMREMGenerator(renderer);
		scene.environment = pmremGenerator.fromScene(new RoomEnvironment(), 0.04).texture;

		// Load the GLB model
		const loader = new GLTFLoader();
		loader.load('/model/scene.glb', (gltf) => {
			scene.add(gltf.scene);
			console.log(gltf);

			animations = gltf.animations;
			if (animations.length > 0) {
				mixer = new THREE.AnimationMixer(gltf.scene);
				playAnimation(0); // Play the first animation initially
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
			if (mixer) mixer.update(0.01);
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
	on:click={() => handleSectionChange(2)}
	role="button"
	tabindex="0"
	on:keydown={() => handleSectionChange(2)}
>
	Projects
</section>
<section
	on:click={() => handleSectionChange(10)}
	role="button"
	tabindex="0"
	on:keydown={() => handleSectionChange(10)}
>
	Contact
</section>

<style>
</style>
