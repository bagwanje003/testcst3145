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
      <ProductList @addProducts="addToCart" :lessons="filteredLessons" :serverUrl="serverUrl" v-if="activeComponent === 'ProductList'" />
      <CheckoutPage :cart="cart" v-if="activeComponent === 'CheckoutPage'" @remove-from-cart="removeFromCart" @clear-cart="clearCart" />
      <button v-on:click="changeComponent" class="btn btn-primary m-3">
        {{ activeComponent === 'ProductList' ? 'Go to Checkout' : 'Back to Products' }}
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
      serverUrl: "http://localhost:8000/collection/lessons",
      lessons: [],
      cart: JSON.parse(localStorage.getItem('cart')) || {},
      activeComponent: 'ProductList',
      searchQuery: "",
      sortOption: "",
      sitename: "After School"
    };
  },
  computed: {
    cartItemCount() {
      return Object.values(this.cart).reduce((total, item) => total + item.quantity, 0);
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
        if (!this.cart[lesson._id]) {
          this.cart[lesson._id] = { ...lesson, quantity: 0 };
        }
        this.cart[lesson._id].quantity++;
        lesson.spaces--;
        localStorage.setItem("cart", JSON.stringify(this.cart));
      }
    },
    removeFromCart(item) {
      if (this.cart[item._id].quantity > 1) {
        this.cart[item._id].quantity--;
      } else {
        delete this.cart[item._id];
      }
      localStorage.setItem('cart', JSON.stringify(this.cart));
    },
    clearCart() {
      this.cart = {};
      localStorage.setItem('cart', JSON.stringify(this.cart));
    },
    async getLessons() {
      try {
        const response = await fetch(this.serverUrl);
        if (!response.ok) {
          throw new Error(`Failed to fetch lessons: ${response.statusText}`);
        }
        this.lessons = await response.json();
      } catch (error) {
        console.error("Failed to fetch lessons:", error);
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
