<script>
  export let tagName = undefined;
  import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
  import MD from '$lib/MD.svelte';
  import ImageLoader from '$lib/ImageLoader.svelte';
  import VideoLoader from '$lib/VideoLoader.svelte';
  import {ticket_inverse_direction} from "$lib/order.js";
  let tickets = [];
  let promise;
  let limit = 8,
      current_page = 0;
  let can_load_more = true;

  onMount(async function(){
    loadMore();
  });
  function loadMore(){
    promise = fetchTickets().then((new_tickets)=>{
      console.log(new_tickets.length);
      if(new_tickets.length < limit || new_tickets.length == 0) can_load_more = false;
      if(new_tickets.length == 0) return;
      current_page += 1;
      tickets = [...tickets, ...new_tickets];
    });
  }
  async function fetchTickets(){
        let filter = "";
        if(tagName) filter = `, filters: { tags: { name: { eq: "${tagName}" } } }`;

        let fetch_start = current_page * limit,
            fetch_end = fetch_start + limit;

        let ticket_sort_direction = $ticket_inverse_direction?"ASC":"DESC"

        let truc = await fetch('https://api.gotibo.fr/graphql', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            query: `
            query {
              tickets(sort: "date:${ticket_sort_direction}", pagination:{limit:${limit}, start:${fetch_start}}, ${filter}) {
                data {
                  id
                  attributes {
                    name
                    date
                    tags {
                      data {
                        id
                        attributes {
                          name
                        }
                      }
                    }
                    body {
                      ... on ComponentBlocksMarkdowBlock {
                        __typename
                        MarkdownBlock
                      }
                      ... on ComponentBlocksVideo {
                        __typename
                        width
                        height
                        VideoBlock {
                          data {
                            attributes {
                              url
                            }
                          }
                        }
                      }
                      ... on ComponentBlocksGallery {
                        __typename
                        content {
                          data {
                            id
                            attributes {
                              url
                              width
                              height
                              alternativeText
                            }
                          }
                        }
                      }
                    }
                  }
                }
              }
            }

              `,
            variables: {
              now: new Date().toISOString(),
            },
          }),
        })
        let res = await truc.json();
        return res.data.tickets.data;

  }

</script>
<div>
  <div out:fade in:fade={{delay:200}} class="flex flex-col space-y-16">
    {#each tickets as ticket}
      <div class="bg-zinc-50 shadow-lg rounded-sm flex flex-col space-y-4">
      {#each ticket.attributes.body as element}
        {#if  element.__typename == "ComponentBlocksMarkdowBlock"}
          <MD content={element.MarkdownBlock}/>
        {/if}
        {#if  element.__typename == "ComponentBlocksGallery"}
          <div class="flex flex-col">
            {#each element.content.data as media}
              <ImageLoader {...media.attributes}/>
            {/each}
          </div>
        {/if}
        {#if  element.__typename == "ComponentBlocksVideo"}
          <div class="flex flex-col">
              <VideoLoader
              url={element.VideoBlock.data.attributes.url}
              width={element.width}
              height={element.height}
              />
          </div>
        {/if}
      {/each}
      <footer class="px-10 pb-8 pt-10 font-medium opacity-50">
        {ticket.attributes.date}
      </footer>
    </div>
  {/each}
  </div>

{#if promise != undefined}
  <footer class="flex flex-col space-y-4 items-center mt-36 mb-32 font-medium text-indigo-500/80">
    {#if can_load_more}
      {#await promise}
          <svg class="animate-spin stroke-indigo-500 h-8 w-8" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" fill="none">
      			<path d="M16 26.5C16.8284 26.5 17.5 25.8284 17.5 25C17.5 24.1716 16.8284 23.5 16 23.5V26.5ZM10.9999 23.4832L11.8332 22.236L11.8332 22.236L10.9999 23.4832ZM7.68509 19.4442L6.29927 20.0182L6.29927 20.0182L7.68509 19.4442ZM7.17294 14.2442L8.64411 14.5368L8.64411 14.5368L7.17294 14.2442ZM9.63604 9.63604L10.6967 10.6967L10.6967 10.6967L9.63604 9.63604ZM14.2442 7.17294L14.5368 8.64411L14.5368 8.64411L14.2442 7.17294ZM19.4442 7.68509L20.0182 6.29927L20.0182 6.29927L19.4442 7.68509ZM23.4832 10.9999L22.236 11.8332L22.236 11.8332L23.4832 10.9999ZM16 23.5C14.5166 23.5 13.0666 23.0601 11.8332 22.236L10.1665 24.7304C11.8932 25.8842 13.9233 26.5 16 26.5V23.5ZM11.8332 22.236C10.5999 21.4119 9.63856 20.2406 9.07091 18.8701L6.29927 20.0182C7.09399 21.9368 8.4398 23.5767 10.1665 24.7304L11.8332 22.236ZM9.07091 18.8701C8.50325 17.4997 8.35473 15.9917 8.64411 14.5368L5.70176 13.9516C5.29661 15.9884 5.50455 18.0996 6.29927 20.0182L9.07091 18.8701ZM8.64411 14.5368C8.9335 13.082 9.64781 11.7456 10.6967 10.6967L8.57538 8.57538C7.10693 10.0438 6.1069 11.9148 5.70176 13.9516L8.64411 14.5368ZM10.6967 10.6967C11.7456 9.64781 13.082 8.9335 14.5368 8.64411L13.9516 5.70176C11.9148 6.1069 10.0438 7.10693 8.57538 8.57538L10.6967 10.6967ZM14.5368 8.64411C15.9917 8.35473 17.4997 8.50325 18.8701 9.07091L20.0182 6.29927C18.0996 5.50455 15.9884 5.29661 13.9516 5.70176L14.5368 8.64411ZM18.8701 9.07091C20.2406 9.63856 21.4119 10.5999 22.236 11.8332L24.7304 10.1665C23.5767 8.4398 21.9368 7.09399 20.0182 6.29927L18.8701 9.07091ZM22.236 11.8332C23.0601 13.0666 23.5 14.5166 23.5 16H26.5C26.5 13.9233 25.8842 11.8932 24.7304 10.1665L22.236 11.8332ZM23.5 16V18.3793H26.5V16H23.5Z" fill="#8577EF"/>
      			<path d="M22 17L25 20L28 17" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
      		</svg>
          <div>Loading</div>
        {:then}

          <button
            on:click={loadMore}
            class="bg-indigo-500 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded"
          >
            Load more?
          </button>

        {:catch error}
          <div>Error with server :/</div>
      {/await}
    {:else}
      <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 stroke-indigo-500" fill="none" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
      </svg>
      <div>You’ve reached the end of the page.</div>
    {/if}
  </footer>
{/if}
</div>
