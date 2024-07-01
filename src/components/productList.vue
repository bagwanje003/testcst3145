<template>
  <div class="row">
    <div class="col-md-4" v-for="lesson in filteredLessons" :key="lesson.id">
      <div class="card mb-3">
        <div class="card-header">
          <img :src="lesson.image" alt="" class="img-fluid">
        </div>
        <div class="card-body">
          <h3 class="card-title">{{ lesson.subject }}</h3>
          <p class="card-text">Location: {{ lesson.location }}</p>
          <p class="card-text">Price: ${{ lesson.price }}</p>
          <p class="card-text">Spaces: {{ lesson.spaces }}</p>
          <button :disabled="lesson.spaces === 0" @click="addToCart(lesson)" class="btn btn-success btn-block">Add to Cart</button>
        </div>
      </div>
    </div>
  </div>
  <div class="d-flex justify-content-end">
     <button :disabled="cartItemCount === 0" @click="changeComponent" class="btn btn-success">
      <span class="badge badge-light">Cart {{ cartItemCount }}</span>
    </button> 
  </div>
</template>
<script>
import LESSONS from './lessons.js';

export default {
  name: "ProductList",
  data() {
    return {
      lessons: [], // Array to hold lesson data
      cart: [], // Array to hold cart items
      showCart: false, // Boolean to control cart visibility
      sortOption: "", // String to store selected sort option
      searchQuery: "", // String to store search query
    };
  },
  computed: {
    // Filtered lessons based on search query
    filteredLessons() {
      let filtered = this.lessons;
      if (this.searchQuery) {
        filtered = filtered.filter(
          lesson => 
            lesson.subject.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            lesson.location.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return filtered;
    },
    // Count of items in the cart
    cartItemCount() {
      return this.cart.length;
    },
  },
  created() {
    // Load lessons data when component is created
    this.getLessons();
  },
  methods: {
    // Method to get lessons data
    getLessons() {
      this.lessons = LESSONS; // Assign imported LESSONS array to lessons
    },
    // Method to add lesson to cart
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
    // Method to toggle cart visibility
    toggleCart() {
      this.showCart = !this.showCart;
      
    },
    // Method to remove lesson from cart
    removeFromCart(item) {
      const index = this.cart.indexOf(item);
      if (index !== -1) {
        this.cart.splice(index, 1);
        const lesson = this.lessons.find(l => l.id === item.id);
        lesson.spaces += item.quantity;
        console.log('Removed from cart:', item);
      }
    },
  },
};
</script>
