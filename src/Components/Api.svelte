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
          </div>
        </div>
      </div>
    </li>
  {/each}
</ul>
