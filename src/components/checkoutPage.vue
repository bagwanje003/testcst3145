<template>
  <div>
    <h1>Checkout</h1>
    <div v-if="cartItems.length > 0" class="container p-3 m-2">
      <div v-for="(item, index) in cartItems" :key="index" class="cart-item card">
        <div class="card-body">
          <h2 class="card-title">{{ item.subject }}</h2>
          <p class="card-text">Quantity: {{ item.quantity }}</p>
          <p class="card-text">Price: ${{ item.price }}</p>
          <p class="card-text">Total: ${{ item.price * item.quantity }}</p>
          <button @click="removeItem(item)" class="btn btn-outline-danger">Remove</button>
        </div>
      </div>
      <h3>Total: ${{ totalPrice }}</h3>
      <form @submit.prevent="handleSubmit" class="m-3">
        <div class="mx-4 mb-3">
          <label for="name">Name:</label>
          <input type="text" v-model="name" id="name" required class="form-control"/>
        </div>
        <div class="mx-4 mb-3">
          <label for="phone">Phone:</label>
          <input type="text" v-model="phone" id="phone" required class="form-control"/>
        </div>
        <button type="submit" :disabled="!validForm" class="btn btn-outline-success mx-4">Confirm Order</button>
      </form>
    </div>
    <div v-else>
      <p>Your cart is empty.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "CheckoutPage",
  props: {
    cart: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      name: "",
      phone: "",
    };
  },
  computed: {
    cartItems() {
      return this.cart;
    },
    totalPrice() {
      return this.cartItems.reduce(
        (total, item) => total + item.price * item.quantity,
        0
      );
    },
    validForm() {
      return /^[A-Za-z\s]+$/.test(this.name) && /^\d+$/.test(this.phone);
    },
  },
  methods: {
    handleSubmit() {
      if (!this.validForm) {
        alert("Please provide valid details.");
        return;
      }

      alert("Order successful!");
      this.$emit("clear-cart");
      this.name = "";
      this.phone = "";
    },
    removeItem(item) {
      this.$emit("remove-from-cart", item);
    },
  },
};
</script>

<style scoped>
.cart-item {
  margin-bottom: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
</style>
