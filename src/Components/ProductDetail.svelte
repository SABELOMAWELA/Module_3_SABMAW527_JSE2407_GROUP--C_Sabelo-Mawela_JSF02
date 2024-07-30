<script>
  import { onMount } from 'svelte';
  import { Link } from 'svelte-routing';
  
  export let id;
  let product = null;
  let loading = true;
/**
   * Fetches the product details from the API based on the product ID.
   * @async
   * @function
   */
  onMount(async () => {
    try {
      const response = await fetch(`https://fakestoreapi.com/products/${id}`);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      product = await response.json();
    } catch (error) {
      console.error('There has been a problem with your fetch operation:', error);
    } finally {
      loading = false;
    }
  });
</script>

{#if loading}
  <p>Loading...</p>
{:else}
  <div class="max-w-2xl mx-auto p-6 bg-white rounded-lg shadow-lg mt-6">
    <div class="flex justify-between items-center">
      <img src={product.image} alt={product.title} class="object-contain h-48 mt-3 mx-auto" />
      <Link to="/" class="text-gray-600 hover:text-gray-900">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </Link>
    </div>
    <h1 class="text-2xl font-bold mt-4">{product.title}</h1>
    <div class="flex items-center mt-2">
      {#each Array(5).fill(0).map((_, i) => i + 1) as star}
        <svg class="w-4 h-4 fill-current {star <= Math.round(product.rating.rate) ? 'text-yellow-500' : 'text-gray-300'}" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
          <path d="M12 .587l3.668 7.571 8.332 1.151-6.063 5.852 1.428 8.287L12 18.897l-7.365 3.851 1.428-8.287-6.063-5.852 8.332-1.151z" />
        </svg>
      {/each}
    </div>
    <span class="inline-flex items-center rounded-md bg-gray-50 px-2 py-1 text-xs font-medium text-gray-600 ring-1 ring-inset ring-gray-500/10 mt-2">
      {product.category}
    </span>
    <h3 class="text-xl font-bold mt-4">${product.price}</h3>
    <button class="bg-cyan-700 hover:bg-cyan-900 w-full text-white font-bold py-2 px-4 rounded mt-4">
      Add To Cart
    </button>
    <h2 class="text-lg font-bold mt-4">Description</h2>
    <p class="mt-2">{product.description}</p>
  </div>
{/if}
