<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { VRM } from '@pixiv/three-vrm';
	import * as SC from 'svelte-cubed';

	let height = 2;
	let ninja;

	onMount(() => {
		const loader = new GLTFLoader();


		// loader.load('/naked_anime_female_character_3/scene.gltf', (gltf) => {
		// 	ninja = gltf.scene;
		// 	console.log(ninja);
		// })


		loader.load(

			// URL of the VRM you want to load
			'/Sonya-Ninja.vrm',

			// called when the resource is loaded
			( gltf ) => {

				// generate a VRM instance from gltf
				VRM.from( gltf ).then( ( vrm ) => {

					// add the loaded vrm to the scene
					// scene.add( vrm.scene );
					ninja = vrm.scene;
					ninja.rotation.y = Math.PI;
					ninja.traverse(c => {
						c.castShadow = true;
					})

					// deal with vrm features
					console.log( vrm );

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

	<SC.PerspectiveCamera position={[1, 1, 4]} />
	<SC.OrbitControls enableZoom={true} maxPolarAngle={Math.PI * 0.51} />
	<SC.AmbientLight intensity={0.6} />
	<SC.DirectionalLight intensity={0.5} position={[2, 3, 2]} shadow={{ mapSize: [4048, 4048] }} />

	<SC.Group position={[0, -height / 2, 0]}>
		<SC.Mesh geometry={new THREE.PlaneGeometry(50, 50)} material={new THREE.MeshStandardMaterial({ color: 'pink' })} rotation={[-Math.PI / 2, 0, 0]} receiveShadow />
		<SC.Primitive object={new THREE.GridHelper(50, 50, 0x444444, 0x555555)} position={[0, 0.001, 0]} />
	</SC.Group>
</SC.Canvas>