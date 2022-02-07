<script>
  import "../app.scss";
</script>

<video autoplay muted loop id="myVideo">
	<source src="/video/sonya-ninja-web-development-animation.mp4" type="video/mp4">
</video>
<div class="overlay"></div>

<slot />


<style lang="scss">
  video {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		object-fit: cover;
		z-index: 1;
	}
	.overlay {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 2;
		background-color: transparent;
		background-image: linear-gradient(180deg, rgba(237,70,48,0.63) 0%, #ed5ea7 48%);
		opacity: 1;
	}
</style>
