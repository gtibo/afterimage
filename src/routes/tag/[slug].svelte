<script context="module">
	export async function load({
	  params
	}) {
    let {slug} = params;
	  return {
	    props: {
	      slug
	    }
	  }
	}
</script>

<script>
  export let slug;
  import TicketViewer from "$lib/ticketViewer.svelte";
</script>
{#key slug}
<TicketViewer tagName={slug} />
{/key}
