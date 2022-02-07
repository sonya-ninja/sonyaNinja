<script>
	import Logo from "$lib/svg/Logo.svelte";
</script>



<div class="pageWrap">
	<header>
		<Logo />
	</header>
	<section>
		<h1>Kick Ass Web Development</h1>
		<p class="contact">hello@sonya.ninja</p>
		<ul>
			<li>Web Apps</li>
			<li>Websites</li>
			<li>Hosting</li>
			<li>SEO</li>
			<li>UX and UI</li>
			<li>Graphic Design</li>
			<li>Social Media Management</li>
		</ul>
	</section>
</div>


<style lang="scss">
	.pageWrap {
		z-index: 10;
		position: relative;
		display: flex;
		flex-direction: column;
			header {
			width: 100vw;
			height: 100vh;
			display: grid;
			grid-template-columns: 1fr;
			grid-template-rows: 1fr;
			justify-items: center;
			align-items: center;
		}
		:global(header svg) {
			grid-column: 1;
			grid-row: 1;
			width: clamp(200px, 90%, 800px);
		}
		section {
			position: relative;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			margin: 6vw;
		}
		h1 {
			text-align: center;
			margin: 0 0 1rem 0;
			font-size: 3rem;
			color: rgba(0,0,0,0.9);
			font-weight: 500;
		}
		.contact {
			margin: 0 0 3rem 0;
			font-weight: 400;
			font-size: 2rem;
		}
		ul {
			font-size: 1.3rem;
			padding: 0;
			margin: 0;
			list-style: none;
			display: flex;
			flex-direction: column;
			li {
				text-align: center;
				padding: 0.3rem 0;
			}
		}
	}

	// @media (min-width: 600px) {
	// 	:global(header svg) {
	// 		width: 50vw;
	// 	}
	// }
	

</style>