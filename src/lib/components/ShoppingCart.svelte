<script lang="ts">
    
    import { createEventDispatcher, getContext } from 'svelte';

    // Icons
    import ShoppingBagIcon from '$lib/icons/ShoppingBagIcon.svelte';
    import CloseIcon from '$lib/icons/CloseIcon.svelte';
    import MinusIcon from '$lib/icons/MinusIcon.svelte';
    import PlusIcon from '$lib/icons/PlusIcon.svelte';

    // Components
    import Loader from '$lib/components/Loader.svelte';
  	import type { ShoppingCartItem } from '../types';
    import type { Writable } from 'svelte/store';

    const dispatch = createEventDispatcher();
    let isLoading = false;

    const shoppingCart = getContext<Writable<ShoppingCartItem[]>>("shoppingCart");

    $: total = $shoppingCart.reduce((total, item) => total += item.price * item.quantity, 0);

    async function checkout() { isLoading = true; setTimeout(() => isLoading = false, 2000) }

  </script>
  
  <div
    on:click|self
    class="absolute inset-0 z-50 flex h-full w-full justify-end overflow-hidden bg-black/50"
  >
    <div class="z-50 w-full h-screen fixed bg-black p-6 md:w-1/2 lg:w-1/3">
      <div class="mb-6 flex w-full items-center justify-between">
        <div class="text-2xl font-medium text-white">My Cart</div>
        <button on:click={ () => dispatch("closeCart") } class="text-sm uppercase opacity-80 hover:opacity-100 text-white">close</button>
      </div>
      {#if $shoppingCart.length === 0}
        <div class="mt-20 flex w-full flex-col items-center justify-center overflow-hidden">
          <div class="flex h-16 w-16 items-center justify-center rounded-full bg-white">
            <ShoppingBagIcon />
          </div>
          <div class="mt-6 text-center text-2xl font-bold text-white">Your cart is empty.</div>
        </div>
      {/if}
      <div class="overflow-y-auto" style="height: 80%;">
        {#each $shoppingCart as item, index}
          <div class="mb-2 flex w-full">
            <img
              alt={item.name}
              decoding="async"
              loading="lazy"
              class="w-20 h-20 flex-none bg-white"
              src={item.thumbnail}
            />
            <div class="ml-4 flex w-full flex-col justify-between">
              <div class="flex w-full justify-between">
                <div>
                  <p class="text-lg font-bold text-white">{item.name}</p>
                  {#each Object.entries(item.properties) as [name, value]}
                    <p class="text-sm font-light text-white capitalize text-gray-300/[0.6]">{value}</p>
                  {/each}
                </div>
                <p class="font-medium text-white">${item.price}</p>
              </div>
            </div>
          </div>
          <div class="mb-4 flex w-full">
            <button
              on:click={() => {
                shoppingCart.removeItem(index);
              }}
              class="mr-2 flex h-8 w-8 items-center justify-center border border-white/40 bg-white/0 hover:bg-white/10"
            >
              <CloseIcon color="white" />
            </button>
            <div class="flex h-8 w-full border border-white/40">
              <div class="flex h-full items-center px-2 text-white">
                {item.quantity}
              </div>
              <button
                on:click={() => {
                  shoppingCart.decreaseItemQuantity(index);
                }}
                class="ml-auto flex h-8 w-8 items-center justify-center border-l border-white/40 bg-white/0 hover:bg-white/10"
                disabled={item.quantity==0}
              >
                <MinusIcon color={ item.quantity == 0 ? "gray" : "white" }/>
              </button>
              <button
                on:click={() => {
                  shoppingCart.increaseItemQuantity(index);
                }}
                class="flex h-8 w-8 items-center justify-center border-l border-white/40 bg-white/0 hover:bg-white/10"
              >
                <PlusIcon color="white" />
              </button>
            </div>
          </div>
        {/each}
      </div>
      {#if $shoppingCart.length !== 0}
        <div class="text-white text-xl font-bold mt-3">Total : ${total}</div>
        <button
          on:click={checkout}
          class="mt-3 flex w-full items-center justify-center bg-white p-3 text-sm font-medium uppercase text-black opacity-90 hover:opacity-100"
        >
          <span>Proceed to Checkout</span>
          {#if isLoading}
            <Loader color="#aaa"/>
          {/if}
        </button>
      {/if}
    </div>
  </div>