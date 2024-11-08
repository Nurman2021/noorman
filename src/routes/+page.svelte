<script>
	import '../app.css';
	import Model from '$lib/components/Model.svelte';

	let slogan = `on <CANVAS/>`;
	
</script>


<Model/>
<div class="absolute top-0 z-20 w-full h-screen">
	<main class="mx-auto">
		<h2 class="text-center font-drukWide text-3xl font-medium text-gray-50 sm:text-6xl">Painting Anything <br><span class="animate-text-gradient inline-flex bg-gradient-to-r from-neutral-100 via-slate-400 to-neutral-400 bg-[200%_auto] bg-clip-text leading-tight text-transparent">{slogan}</span></h2>
	</main>	
</div>


<style>
	/* section{ */
		/* height: 100vh; */
		/* width: 10; */
	/* } */

	main{
		max-width: 64rem;
	}
</style>
