<script>
  import { Canvas } from '@threlte/core'
  import { Environment } from '@threlte/extras'
  import Scene from './Scene.svelte'
  import {moveCamera} from './Scene.svelte'
  import { onMount } from 'svelte';
  import gsap from 'gsap';

  onMount(()=>{
  	const ctx = gsap.context((self)=>{
  		gsap.to(moveCamera,{
  			x:20, y:5, z:0,
  		})
  	})
  		return () => ctx.revert(); // <- Cleanup!
  })
</script>

<Canvas>
  <Scene moveCamera={{x:10, y:20, z:8}} rotateCamera={{x:0,y:1,z:0}} />
</Canvas>

<section>
	<h1>this is sparta</h1>
	
</section>

<style>
	section{
		position: absolute;
		top: 0;
		width: 100vw;
		height: 100vh;
		z-index: 100;
		/*background-color: red; */
	}

	h1{
		color: white;
	}

	Canvas{

	}
</style>
