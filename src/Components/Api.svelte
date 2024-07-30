<script>
    import { onMount } from 'svelte';

    let data = {
      products: [],
      categories: []
    };

    let info = data.products;
    let loading = true;
    let showModal = false;
    let emptyProduct = null;
    let filterItem = 'All categories';
    let searchTerm = '';
    let sorting = '';
  
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
        console.log(products)
      } catch (error) {
        console.error('Error fetching products:', error); // Log any errors
      } finally {
        loading = false;
      }
    }
   
    onMount(() => {
      loadProducts();
    });

    export const productsData = data;
  </script>
