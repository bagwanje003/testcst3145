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
          <button :disabled="lesson.spaces === 0" @click="addToCart(lesson)" class="btn btn-success btn-block">Add to
            Cart</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import LESSONS from './lessons.js';

export default {
  name: "ProductList",
  props: ['addProducts'],
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
    addToCart(lesson) {
      this.$emit('addProducts', lesson)
    },

    // Method to get lessons data
    getLessons() {
      this.lessons = LESSONS; // Assign imported LESSONS array to lessons
    },
    // Method to add lesson to cart

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
