<template>
  <div>
    <h1>Checkout</h1>
    <div v-if="cartItems.length > 0">
      <div v-for="(item, index) in cartItems" :key="index" class="cart-item">
        <h2>{{ item.subject }}</h2>
        <p>Quantity: {{ item.quantity }}</p>
        <p>Price: ${{ item.price }}</p>
        <p>Total: ${{ item.price * item.quantity }}</p>
        <button @click="removeItem(item)">Remove</button>
      </div>
      <h3>Total: ${{ totalPrice }}</h3>

      <form @submit.prevent="handleSubmit">
        <div>
          <label for="name">Name:</label>
          <input type="text" v-model="name" id="name" required />
        </div>
        <div>
          <label for="phone">Phone:</label>
          <input type="text" v-model="phone" id="phone" required />
        </div>
        <button type="submit" :disabled="!validForm">Confirm Order</button>
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
      type: Object,
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
      // Convert cart object to an array of items for easier iteration
      return Object.values(this.cart);
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

      // Handle form submission logic here (as previously implemented)
      // Ensure it aligns with your application's requirements
      alert("Order successful!");
      this.$emit("clear-cart");
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
