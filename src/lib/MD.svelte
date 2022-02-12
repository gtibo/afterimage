<script>
  export let content;
  import { marked } from 'marked';
</script>

<div class="markdownBlock px-10">
  {@html marked.parse(content)}
</div>

<style style="post-css">
.markdownBlock :global(h1){
  @apply text-5xl mb-6 mt-16;
}
.markdownBlock :global(h2){
  @apply text-3xl mb-2 mt-8;
}
  .markdownBlock :global(p){
    @apply text-xl my-3;
  }
  .markdownBlock :global(blockquote>p){
    @apply opacity-80 my-12 pl-6 border-l border-l-4;
  }
  .markdownBlock :global(blockquote>p){
    @apply text-2xl;
  }
</style>
