<script>
  import Navbar from './Components/navbar.svelte';
  import SearchSort from './Components/Search&sort.svelte';
  import { Router, Route } from 'svelte-routing';
  import ProductList from './Components/Api.svelte';
  import ProductDetail from './Components/ProductDetail.svelte';
</script>

<main class="bg-cyan-100 w-full h-full">
  <Router>
    <Navbar title="SwiftCart" />
    <!-- <SearchSort /> -->
    <Route path="/" component={ProductList} />
    <Route path="/product/:id" component={ProductDetail} />
  </Router>
</main>
