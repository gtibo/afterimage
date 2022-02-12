<script context="module">
	export async function load({
	  url,
	  fetch
	}) {
	  let tags_list = await fetch("https://api.gotibo.fr/api/tags"),
	    tags = await tags_list.json();
	  return {
	    props: {
	      tags: tags.data,
	      current: url.pathname
	    }
	  }
	}
</script>

<script>
	import '../app.css';
	import {ticket_inverse_direction} from "$lib/order.js";
	export let current, tags;
	function toggle_direction(){
		ticket_inverse_direction.update(n => !n);
	}
</script>
<main class="container mx-auto max-w-screen-lg my-8">
<header class="p-6 flex flex-col space-y-6 mb-20">
	<div class="flex flex-col space-y-3">
		<h1 class="text-4xl font-bold flex space-x-2">
			<svg xmlns="http://www.w3.org/2000/svg"
			class="w-10 h-10 stroke-indigo-500"
			viewBox="0 0 43 42" fill="none">
			<rect x="2" y="2" width="28" height="28"  stroke-width="4"/>
			<rect x="13" y="12" width="28" height="28" stroke-width="4"/>
			</svg>
			<span>AfterImage</span>
		</h1>
		<p class="text-xl text-zinc-500 font-medium">Online research space where I share inspiration, work in progress and experimentations.</p>
	</div>
	<nav class="flex flex-col gap-y-4">
	<div class="flex flex-wrap gap-x-2 gap-y-2 flex-col md:flex-row">
		<a class:current={current == "/" } class="nav-link" href="/">All</a>
		{#each tags as tag}
			<a class:current={current == `/tag/${tag.attributes.name}` } class="nav-link" href="/tag/{tag.attributes.name}">{tag.attributes.name}</a>
		{/each}
	</div>
	<button class="font-normal flex gap-x-2 items-center" on:click={toggle_direction}>
		<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 fill-indigo-500 transition duration-200" class:rotate-180={$ticket_inverse_direction} viewBox="0 0 20 20" fill="currentColor">
		<path d="M5 12a1 1 0 102 0V6.414l1.293 1.293a1 1 0 001.414-1.414l-3-3a1 1 0 00-1.414 0l-3 3a1 1 0 001.414 1.414L5 6.414V12zM15 8a1 1 0 10-2 0v5.586l-1.293-1.293a1 1 0 00-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L15 13.586V8z" />
		</svg>
		<h4 class="opacity-60">
		{#if $ticket_inverse_direction}
			From <b>oldest</b> to <b>newest</b>
		{:else}
			From <b>newest</b> to <b>oldest</b>
		{/if}
		</h4>
	</button>
	</nav>
</header>
	{#key $ticket_inverse_direction}
	<slot />
	{/key}
</main>
<style style="post-css">
	.nav-link{
		@apply border-b-2 border-zinc-300 font-semibold uppercase text-zinc-800;
	}
	.nav-link.current,.nav-link:hover{
		@apply border-b-2 border-indigo-500 text-indigo-500;
	}
</style>
