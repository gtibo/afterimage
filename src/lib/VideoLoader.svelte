<script>
  export let width,height,url;

  import {
    onMount
  } from "svelte";
  let video_el, loaded = false;
  onMount(() => {
    video_el.src = `https://api.gotibo.fr${url}`;
  });

</script>

<video
  bind:this={video_el}
  width={width}
  height={height}
  style="aspect-ratio:{width/height};"
  controls
  class:hide={!loaded}
  on:loadeddata={()=>{loaded = true;}}
  src={url}
  lazy
  />

<style style="post-css">
  video{
    @apply w-full transition-opacity ease-in-out duration-200 opacity-100;
  }
  video.hide{
    @apply opacity-0;
  }
</style>
