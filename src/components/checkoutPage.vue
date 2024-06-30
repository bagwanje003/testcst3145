<template>
  <div>
    <h1>Checkout</h1>
    <div v-if="cart.length > 0">
      <div v-for="item in cart" :key="item.id" class="cart-item">
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
  props: ["cart"],
  data() {
    return {
      name: "",
      phone: "",
    };
  },
  computed: {
    totalPrice() {
      return this.cart.reduce(
        (total, item) => total + item.price * item.quantity,
        0
      );
    },
    validForm() {
      return /^[A-Za-z\s]+$/.test(this.name) && /^\d+$/.test(this.phone);
    },
  },
  methods: {
    async handleSubmit() {
      if (!this.validForm) {
        alert("Please provide valid details.");
        return;
      }

      try {
        for (const item of this.cart) {
          const response = await fetch(
            `http://localhost:8000/collection/lessons/${item.id}`,
            {
              method: "PUT",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify({
                spaces: item.spaces - item.quantity,
              }),
            }
          );
          if (!response.ok) {
            throw new Error("Failed to update lesson spaces");
          }
        }
        alert("Order successful!");
        this.$emit("clear-cart");
      } catch (error) {
        console.error("Checkout failed:", error);
      }
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
