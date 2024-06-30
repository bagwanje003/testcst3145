<template>
  <div>
    <div class="row">
      <div class="col-md-4" v-for="lesson in filteredLessons" :key="lesson.id">
        <div class="card mb-3">
          <div class="card-header">
            <img :src="lesson.image" alt="" class="img-fluid" />
          </div>
          <div class="card-body">
            <h3 class="card-title">{{ lesson.subject }}</h3>
            <p class="card-text">Location: {{ lesson.location }}</p>
            <p class="card-text">Price: ${{ lesson.price }}</p>
            <p class="card-text">Spaces: {{ lesson.spaces }}</p>
            <button
              :disabled="lesson.spaces === 0"
              @click="addToCart(lesson)"
              class="btn btn-success btn-block"
            >
              Add to Cart
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="d-flex justify-content-end">
      <button
        :disabled="cartItemCount === 0"
        @click="toggleCart"
        class="btn btn-success"
      >
        <span class="badge badge-light">Cart {{ cartItemCount }}</span>
      </button>
    </div>
  </div>
</template>

<script>
import LESSONS from './lessons.js';

export default {
  name: 'ProductList',
  data() {
    return {
      lessons: LESSONS,
      cart: [],
      showCart: false,
      sortOption: '',
      searchQuery: '',
      name: '',
      phone: '',
    };
  },
  computed: {
    filteredLessons() {
      let filtered = this.lessons;
      if (this.searchQuery) {
        filtered = filtered.filter(
          (lesson) =>
            lesson.subject
              .toLowerCase()
              .includes(this.searchQuery.toLowerCase()) ||
            lesson.location
              .toLowerCase()
              .includes(this.searchQuery.toLowerCase())
        );
      }
      return filtered;
    },
    validForm() {
      return /^[A-Za-z]+$/.test(this.name) && /^\d+$/.test(this.phone);
    },
    cartItemCount() {
      return this.cart.length;
    },
  },
  methods: {
    addToCart(lesson) {
      console.log('addToCart called with:', lesson); // Debugging line
      if (lesson && lesson.spaces > 0) {
        const item = { ...lesson, spaces: 1 };
        this.cart.push(item);
        lesson.spaces--;
      } else {
        console.error('Invalid lesson or no spaces available:', lesson); // Debugging line
      }
    },
    toggleCart() {
      this.showCart = !this.showCart;
    },
    removeFromCart(item) {
      const index = this.cart.indexOf(item);
      if (index !== -1) {
        this.cart.splice(index, 1);
        const lesson = this.lessons.find((l) => l.id === item.id);
        if (lesson) {
          lesson.spaces++;
        }
      }
    },
    sortLessons() {
      if (this.sortOption) {
        this.lessons.sort((a, b) => {
          const order = this.sortOption === 'price' ? 1 : -1;
          if (a[this.sortOption] < b[this.sortOption]) return -1 * order;
          if (a[this.sortOption] > b[this.sortOption]) return 1 * order;
          return 0;
        });
      }
    },
    async checkout() {
      if (this.validForm) {
        const order = {
          lessons: this.cart.map((item) => ({
            id: item.id,
            spaces: item.spaces,
          })),
          username: this.name,
          phonenumber: this.phone,
        };

        try {
          const response = await fetch(
            'http://localhost:8000/collection/orders',
            {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify(order),
            }
          );

          if (!response.ok) {
            throw new Error(`Error creating order: ${response.statusText}`);
          }

          alert(
            `Order created successfully for ${this.name} (phone: ${this.phone})!`
          );
          this.cart = [];
          this.name = '';
          this.phone = '';
        } catch (error) {
          console.error('Error:', error.message);
          // Handle checkout errors (e.g., display error message to user)
        }
      }
    },
  },
};
</script>
