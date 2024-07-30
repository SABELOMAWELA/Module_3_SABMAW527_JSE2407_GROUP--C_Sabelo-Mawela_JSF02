<script>
  import { onMount } from 'svelte';
  import { Link } from 'svelte-routing';

  let data = {
    products: [],
    categories: []
  };
  let loading = true;
  let filterItem = 'All categories';
  let searchTerm = '';
  let sorting = '';
  let dropdownOpen = false;

  async function loadProducts() {
    loading = true;
    let url = 'https://fakestoreapi.com/products';
    if (filterItem !== 'All categories') {
      url = `${url}/category/${filterItem}`;
    }

    try {
      const response = await fetch(url);
      let products = await response.json();

      if (searchTerm) {
        products = products.filter(product =>
          product.title.toLowerCase().includes(searchTerm.toLowerCase())
        );
      }

      if (sorting === 'Price: low to high') {
        products = products.sort((a, b) => a.price - b.price);
      } else if (sorting === 'Price: high to low') {
        products = products.sort((a, b) => b.price - a.price);
      }

      data.products = products;
    } catch (error) {
      console.error('Error fetching products:', error);
    } finally {
      loading = false;
    }
  }

  async function loadCategories() {
    try {
      const response = await fetch('https://fakestoreapi.com/products/categories');
      const categories = await response.json();
      data.categories = ['All categories', ...categories];
    } catch (error) {
      console.error('Error fetching categories:', error);
    }
  }

  function handleSearch(event) {
    searchTerm = event.target.value;
    loadProducts();
  }

  function handleSort(event) {
    sorting = event.target.value;
    loadProducts();
  }

  function handleFilter(category) {
    filterItem = category;
    dropdownOpen = false;
    loadProducts();
  }

  function toggleDropdown() {
    dropdownOpen = !dropdownOpen;
  }

  function getStarClass(star, rating) {
    return star <= Math.round(rating) ? 'text-yellow-500' : 'text-gray-300';
  }

  onMount(() => {
    loadProducts();
    loadCategories();
  });
</script>

<div class="grid lg:flex gap-y-4 gap-x-48 lg:items-start mt-3 mx-auto justify-center">
  <form>
    <div class="flex lg:w-[31.25rem] sm:w-[95%] md:w-full relative">
      <button 
        id="dropdown-button" 
        class="flex-shrink-0 z-10 inline-flex items-center py-2.5 px-4 text-sm font-medium text-center text-gray-900 bg-gray-100 border border-gray-300 rounded-s-lg hover:bg-gray-200" 
        type="button"
        on:click={toggleDropdown}
      >
        <span>{filterItem}</span>
        <svg class="w-2.5 h-2.5 ms-2.5" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 10 6">
          <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m1 1 4 4 4-4"/>
        </svg>
      </button>
      {#if dropdownOpen}
        <div id="dropdown" class="z-10 absolute top-[100%] bg-white divide-y divide-gray-100 rounded-lg shadow w-44">
          <ul class="py-2 text-sm text-gray-700" aria-labelledby="dropdown-button">
            {#each data.categories as category}
              <li class="inline-flex w-full px-4 py-2 hover:bg-gray-100" on:click={() => handleFilter(category)}>{category}</li>
            {/each}
          </ul>
        </div>
      {/if}
      <div class="relative w-full">
        <input 
          type="search" 
          id="search-dropdown" 
          name="searchInput" 
          bind:value={searchTerm} 
          class="p-2.5 w-full z-20 text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500" 
          placeholder="Search products..."
          on:input={handleSearch}
        />
        <button type="submit" class="absolute top-0 end-0 p-2.5 text-sm font-medium h-full text-white bg-blue-700 rounded-e-lg border border-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300">
          <svg class="w-4 h-4" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">
            <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>
          </svg>
          <span class="sr-only">Search</span>
        </button>
      </div>
    </div>
  </form> 
  <div class="flex sm:w-[95%] max-w-[21rem] md:w-full">
    <label for="sort" class="w-20 my-auto font-semibold text-black">Sort by:</label>
    <select 
      id="sort" 
      bind:value={sorting} 
      on:change={handleSort} 
      class="p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
    >
      <option value="">Default</option>
      <option value="Price: low to high">Price: low to high</option>
      <option value="Price: high to low">Price: high to low</option>
    </select>
  </div>
</div>

<div class="grid justify-center">
  <ul class="lg:max-h-[130rem] max-w-xl mx-auto grid gap-4 grid-cols-1 lg:grid-cols-4 md:grid-cols-2 items-center lg:max-w-none my-4">
    {#each data.products as product}
      <li>
        <div class="flex flex-col max-h-[130rem] cursor-pointer max-w-80 hover:-translate-y-1 hover:scale-105 duration-300 bg-white border border-slate-200 shadow shadow-slate-950/5 rounded-2xl overflow-hidden">
          <Link to={`/product/${product.id}`}>
            <img src="{product.image}" alt="{product.title}" class="object-contain h-48 mt-3" />
          </Link>
          <div class="flex-1 flex flex-col p-6">
            <div class="flex-1">
              <header class="mb-2 flex-2">
                <h2 class="text-lg line-clamp-2 font-extrabold leading-snug">
                  <div class="text-slate-600 focus-visible:outline-none focus-visible:ring focus-visible:ring-indigo-300">
                    <p>{product.title}</p>
                  </div>
                </h2>
              </header>
              <div class="flex items-center">
                {#each Array(5).fill(0).map((_, i) => i + 1) as star}
                  <svg class="w-4 h-4 fill-current {getStarClass(star, product.rating.rate)}" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                    <path d="M12 .587l3.668 7.571 8.332 1.151-6.063 5.852 1.428 8.287L12 18.897l-7.365 3.851 1.428-8.287-6.063-5.852 8.332-1.151z"/>
                  </svg>
                {/each}
              </div>
              <div class="text-base line-clamp-2 font-extrabold text-slate-500 leading-snug">
                <h2>R{product.price}</h2>
              </div>
            </div>
            <div class="flex mt-1 space-x-2">
              <div class="justify-start flex-1">
                <span class="inline-flex items-center rounded-md bg-blue-50 px-2 py-1 text-xs font-medium text-blue-700 ring-1 ring-inset ring-blue-700/10">
                  {product.category}
                </span>
              </div>
              <div class="justify-end space-x-2">
                <button >
                    <svg class="me-1.5 h-5 w-5 hover:fill-red-500" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24" transform="scale(1.6)">
                        <path stroke="currentColor" strokelinecap="round" strokelinejoin="round" strokewidth="2" d="M12.01 6.001C6.5 1 1 8 5.782 13.001L12.011 20l6.23-7C23 8 17.5 1 12.01 6.002Z"></path>
                    </svg>
                </button>
                <button class="inline-flex justify-center whitespace-nowrap rounded-lg bg-cyan-700 px-3 py-2 text-sm font-medium text-white hover:bg-cyan-900 focus-visible:outline-none focus-visible:ring focus-visible:ring-indigo-300 transition-colors">
                    Add To Cart
                </button>
            </div>
            </div>
          </div>
        </div>
      </li>
    {/each}
  </ul>
</div>

{#if loading}
  <p>Loading...</p>
{/if}
