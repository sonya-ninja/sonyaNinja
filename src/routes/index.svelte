<script>
	// NOTES:
	// PATCHING TOGETHER VARIOUS BITS FROM:
	// https://note.com/npaka/n/naf87662867d1#Rggli
	// https://svelte-cubed.vercel.app/docs/getting-started
	// Nice walk cylce for ninja: https://tenor.com/view/ninja-girl-warrior-sexy-girl-sexy-ninja-gif-13464310

	// IMPORTS
	import { onMount } from "svelte";
	import * as THREE from "three";
	import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
	import { VRM, VRMSchema } from "@pixiv/three-vrm";
	import * as SC from "svelte-cubed";

	// SET UP OUR VARS
	let height = 2;
	let ninja;
	let mixer;
	let frame = 0;

	// Set up the animation
	const setupAnimation = (vrm) => {
		// List of vrm bones
		const bones = [VRMSchema.HumanoidBoneName.Head].map((boneName) => {
			return vrm.humanoid.getBoneNode(boneName);
		});
		// AnimationClip
		const clip = THREE.AnimationClip.parseAnimation(
			{
				hierarchy: [
					{
						keys: [
							{
								rot: new THREE.Quaternion()
									.setFromEuler(new THREE.Euler(0, 0, 0))
									.toArray(),
								time: 0,
							},
							{
								rot: new THREE.Quaternion()
									.setFromEuler(
										new THREE.Euler(
											(-40 * Math.PI) / 180,
											0,
											0
										)
									)
									.toArray(),
								time: 1000,
							},
							{
								rot: new THREE.Quaternion()
									.setFromEuler(new THREE.Euler(0, 0, 0))
									.toArray(),
								time: 2000,
							},
						],
					},
				],
			},
			bones
		);
		// When using Animation Clip with VRM, you need to change the track name
		clip.tracks.some((track) => {
			track.name = track.name.replace(
				/^\.bones\[([^\]]+)\].(position|quaternion|scale)$/,
				"$1.$2"
			);
		});
		// AnimationMixer
		mixer = new THREE.AnimationMixer(vrm.scene);
		// AnimationAction
		let action = mixer.clipAction(clip);
		action.play();
	};

	// Run the animation frames
	// SC.onFrame(() => {
	// 	frame += 0.05;
	// 	// console.log(frame);
	// 	if (mixer) {
	// 		mixer.update(frame);
	// 	}
	// });

	// Transparent material that receives shadows
	const shadowMaterial = new THREE.ShadowMaterial();
	shadowMaterial.opacity = 0.2;

	onMount(() => {
		// Clear the console
		console.clear();
		// Load the model and run the animation function
		const loader = new GLTFLoader();
		loader.load(
			// URL to the vroid model (VRM)
			"/Sonya-Ninja-v4.vrm",

			// called when the resource is loaded
			(gltf) => {
				// generate a VRM instance from gltf
				VRM.from(gltf).then((vrm) => {
					// We're using svelte-cubed to put the vrm scene in the canvas
					// Let's call her Ninja, rotate her and add her shadow
					ninja = vrm.scene;
					ninja.rotation.y = Math.PI;
					ninja.traverse((c) => {
						c.castShadow = true;
					});

					// Run the animation setup
					setupAnimation(vrm);
				});
			},

			// called while loading is progressing
			(progress) =>
				console.log(
					"Loading model...",
					100.0 * (progress.loaded / progress.total),
					"%"
				),

			// called when loading has errors
			(error) => console.error(error)
		);
	});
</script>

<!-- HTML ELEMENTS AND SVELTE-CUBED COMPONENTS -->
<SC.Canvas antialias shadows alpha>
	<!-- <SC.Mesh geometry={new THREE.BoxGeometry()} material={new THREE.MeshStandardMaterial({ color: 0xff3e00 })} castShadow /> -->
	<SC.Primitive object={ninja} position={[0, -height / 2, 0]} />
	<SC.PerspectiveCamera position={[0, 1, 2]} />
	<SC.OrbitControls enableZoom={true} />
	<SC.AmbientLight intensity={0.3} />
	<SC.DirectionalLight
		intensity={0.7}
		position={[-1, 2, 1]}
		shadow={{ mapSize: [4048, 4048] }}
	/>
	<SC.Group position={[0, -height / 2, 0]}>
		<SC.Mesh
			geometry={new THREE.PlaneGeometry(5, 5)}
			material={shadowMaterial}
			rotation={[-Math.PI / 2, 0, 0]}
			receiveShadow
		/>
		<!-- <SC.Primitive
			object={new THREE.GridHelper(50, 50, 0x444444, 0x555555)}
			position={[0, 0.001, 0]}
		/> -->
	</SC.Group>
</SC.Canvas>

<div class="pageContainer">
	<div class="pageWrap">
		<header>HEADER</header>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
		<section>Section</section>
	</div>
</div>

<!-- STYLES -->
<style>
	:global(.container) {
		z-index: 2;
		pointer-events: none;
	}
	:global(canvas) {
		border: 1px solid red;
		position: fixed;
	}
	.pageContainer {
		position: fixed;
		z-index: 1;
		border: 10px solid blue;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		overflow-y: scroll;
	}
	.pageWrap {
		padding: 1rem;
		display: grid;
		grid-template-columns: 1fr 1fr 1fr;
		grid-auto-rows: 300px;
		grid-gap: 1rem;
	}
	section {
		border: 3px solid black;
	}
	header {
		grid-column: 1 / span 3;
		grid-row: 1 / span 2;
	}
</style>
