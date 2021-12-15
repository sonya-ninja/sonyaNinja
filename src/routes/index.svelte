<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { VRM, VRMSchema } from '@pixiv/three-vrm';
	import * as SC from 'svelte-cubed';

	let height = 2;
	let ninja;
	let mixer;
	let frame = 0;

	SC.onFrame(() => {
		frame += 0.01;
		// console.log(frame);
	});

	onMount(() => {
		console.clear();
		const loader = new GLTFLoader();

		// Using VRM library with three.js GLTF loader for little ninja model created in Vroid Studio
		// What is in the VRM schema?
		console.log('VRM SCHEMA: ', VRMSchema);

		loader.load(

			// URL to the vroid model (VRM)
			'/ninjav2.vrm',

			// called when the resource is loaded
			( gltf ) => {

				// generate a VRM instance from gltf
				VRM.from( gltf ).then( ( vrm ) => {

					// what's in the vrm (Vroid model)?
					console.log( 'vrm: ', vrm );

					// THREE.JS Animation class:
					// -AnimationClip : Has  animation timeline information.
					// -AnimationAction  : Execution and status management of individual Animation Clips.
					// -AnimationMixer : Manage and mix multiple Animation Actions.


					// TESTING CONTROL THE vrm
					// const head = vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.Head)
					// head.rotation.x = Math.PI /6
					// head.rotation.y = Math.PI /6
					// vrm.blendShapeProxy.setValue(VRMSchema.BlendShapePresetName.Angry, 1.0);
					// vrm.blendShapeProxy.setValue(VRMSchema.BlendShapePresetName.I, 1.0);
					// vrm.blendShapeProxy.update();


					// generate a list of bones for the animation
					const bones = [
						VRMSchema.HumanoidBoneName.Head
						].map((boneName) => {
						return vrm.humanoid.getBoneNode(boneName)
					})

					//  generate timeline information about what to specify for the bone "position", "rotation", and "scale" values ​​at the number of milliseconds after the start of the animation
					const clip = THREE.AnimationClip.parseAnimation({
						hierarchy: [
							{
							keys: [
								{
								rot: new THREE.Quaternion().setFromEuler(new THREE.Euler(0, 0, 0)).toArray(),
								time: 0                       
								},
								{
								rot: new THREE.Quaternion().setFromEuler(new THREE.Euler(-40*Math.PI/180, 0, 0)).toArray(),
								time: 1000                        
								},
								{
								rot: new THREE.Quaternion().setFromEuler(new THREE.Euler(0, 0, 0)).toArray(),
								time: 2000                        
								}
							]
							}
						]
					}, bones)

					// When using Animation Clip with VRM, you need to change the track name
					clip.tracks.some((track) => {
						track.name = track.name.replace(/^\.bones\[([^\]]+)\].(position|quaternion|scale)$/, '$1.$2')
					})

					mixer = new THREE.AnimationMixer(vrm.scene);
					if (mixer) {
						mixer.update(frame);
					};
					let action = mixer.clipAction(clip);
					action.play();

					// We're using svelte-cubed to put the vroid scene in the canvas
					// Let's call her Ninja, rotate her and add her shadow
					ninja = vrm.scene;
					ninja.rotation.y = Math.PI;
					ninja.traverse(c => {
						c.castShadow = true;
					})

				} );

			},

			// called while loading is progressing
			( progress ) => console.log( 'Loading model...', 100.0 * ( progress.loaded / progress.total ), '%' ),

			// called when loading has errors
			( error ) => console.error( error )

		);

	});
	
</script>

<SC.Canvas antialias background={new THREE.Color('papayawhip')} fog={new THREE.FogExp2('papayawhip', 0.1)} shadows>

	<!-- <SC.Mesh geometry={new THREE.BoxGeometry()} material={new THREE.MeshStandardMaterial({ color: 0xff3e00 })} castShadow /> -->
	<SC.Primitive object={ninja} position={[0, -height / 2, 0]} />

	<SC.PerspectiveCamera position={[1, 1, 2]} />
	<SC.OrbitControls enableZoom={true} maxPolarAngle={Math.PI * 0.51} />
	<SC.AmbientLight intensity={0.3} />
	<SC.DirectionalLight intensity={0.6} position={[2, 3, 2]} shadow={{ mapSize: [4048, 4048] }} />

	<SC.Group position={[0, -height / 2, 0]}>
		<SC.Mesh geometry={new THREE.PlaneGeometry(50, 50)} material={new THREE.MeshStandardMaterial({ color: 'pink' })} rotation={[-Math.PI / 2, 0, 0]} receiveShadow />
		<SC.Primitive object={new THREE.GridHelper(50, 50, 0x444444, 0x555555)} position={[0, 0.001, 0]} />
	</SC.Group>
</SC.Canvas>

<div class="pageWrap">
	<header>
		<h1>Sonya Ninja</h1>
		<p>Welcome!</p>
	</header>
</div>


<style>
	.pageWrap {
		position: fixed;
		top: 0;
		left: 0;
		width: 50vw;
		height: 50vh;
		z-index: 2;
	}
</style>