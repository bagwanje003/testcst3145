<template>
  <div id="app" class="container">
    <h1 v-text="sitename" class="mt-5"></h1>
    <header>
      <title>After School</title>
    </header>
    <div class="row mb-4">
      <div class="col-md-6">
        <input type="text" v-model="searchQuery" class="form-control" placeholder="Search for lessons">
      </div>
      <div class="col-md-3">
        <select v-model="sortOption" class="form-control">
          <option value="">Sort by</option>
          <option value="subject">Subject</option>
          <option value="location">Location</option>
          <option value="price">Price</option>
          <option value="spaces">Spaces</option>
        </select>
      </div>
      <div class="col-md-3">
        <button @click="sortLessons" class="btn btn-success btn-block">Sort</button>
      </div>
    </div>
    <main>
      <ProductList @addProducts="addToCart" :lessons="filteredLessons" v-if="activeComponent === 'ProductList'" />
      <CheckoutPage :cart="cart" v-else-if="activeComponent === 'CheckoutPage'" @remove-from-cart="removeFromCart" @clear-cart="clearCart" />
      
      <button :disabled="cart.length == 0" @click="changeComponent" class="btn btn-success">
        <span class="badge badge-light">Cart {{ cart.length }}</span>
      </button>
    </main>
  </div>
</template>

<script>
import CheckoutPage from "./components/checkoutPage.vue";
import ProductList from "./components/productList.vue";

export default {
  name: "App",
  components: {
    ProductList,
    CheckoutPage,
  },
  data() {
    return {
      serverUrl: "http://localhost:8000/components/lessons",
      lessons: [], // Array to store lessons fetched from server
      cart: [], // Array to store cart items
      activeComponent: 'ProductList', // Default active component
      searchQuery: "", // Search query for filtering lessons
      sortOption: "", // Sort option for sorting lessons
      sitename: "After School", // Site name
    };
  },
  computed: {
    cartItemCount() {
      return this.cart.length;
    },
    filteredLessons() {
      let filtered = this.lessons;
      if (this.searchQuery) {
        filtered = filtered.filter(
          (lesson) =>
            lesson.subject.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            lesson.location.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return filtered;
    },
  },
  created() {
    this.getLessons();
  },
  methods: {
    changeComponent() {
      this.activeComponent = this.activeComponent === 'ProductList' ? 'CheckoutPage' : 'ProductList';
    },
    addToCart(lesson) {
      if (lesson.spaces > 0) {
        const itemInCart = this.cart.find(item => item.id === lesson.id);
        if (itemInCart) {
          itemInCart.quantity++;
        } else {
          this.cart.push({ ...lesson, quantity: 1 });
        }
        lesson.spaces--;
        console.log('Added to cart:', lesson);
      }
    },
    removeFromCart(item) {
      const index = this.cart.findIndex(cartItem => cartItem.id === item.id);
      if (index !== -1) {
        if (this.cart[index].quantity > 1) {
          this.cart[index].quantity--;
        } else {
          this.cart.splice(index, 1);
        }
      }
    },
    clearCart() {
      this.cart = [];
    },
    async getLessons() {
      try {
        const response = await fetch(this.serverUrl);
        if (!response.ok) {
          throw new Error('Failed to fetch lessons');
        }
        this.lessons = await response.json();
      } catch (error) {
        console.error(error.message);
      }
    },
    sortLessons() {
      if (this.sortOption) {
        this.lessons.sort((a, b) => {
          if (a[this.sortOption] < b[this.sortOption]) return -1;
          if (a[this.sortOption] > b[this.sortOption]) return 1;
          return 0;
        });
      }
    },
  },
};
</script>

<style scoped>
/* Add any scoped styles for your component here */
</style>
