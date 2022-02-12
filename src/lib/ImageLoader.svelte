<script>
  export let width,height,url,alternativeText;

  import {
    onMount
  } from "svelte";
  let image_el, loaded = false;
  onMount(() => {
    image_el.src = `https://api.gotibo.fr${url}`;
  });

</script>
  <div class="image_container">
      <img
      bind:this={image_el}
      width={width}
      height={height}
      style="aspect-ratio:{width/height};"
      alt={alternativeText}
      class:hide={!loaded}
      on:load={()=>{loaded = true;}}
      loading="lazy"
      />
  </div>

<style style="post-css">
  .image_container{
    @apply inline-block w-full;
  }
  img{
    @apply w-full transition-opacity ease-in-out duration-200 opacity-100;
  }
  img.hide{
    @apply opacity-0;
  }
</style>
