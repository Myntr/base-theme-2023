<script>
import { useCartStore } from "@/vue/store/cart";
import CartItem from "./CartItem.vue";
import { formatProductPrice } from "@/lib/utilities";

export default {
  components: {
    CartItem,
  },
  setup() {
    const cart = useCartStore();
    const money = (priceValue) => formatProductPrice(priceValue);

    const headerCartIcon = document.getElementById("header-cart-icon");
    headerCartIcon.addEventListener("click", (event) => {
      event.preventDefault();
      cart.toggle();
    });

    return {
      cart,

      money,
    };
  },
};
</script>
<template>
  <!-- max-w-md md:max-w-lg -->
  <div
    class="fixed visible bottom-0 top-0 right-0 z-50 flex flex-col w-full max-w-lg lg:max-w-xl bg-white border-ink shadow-lg transition duration-300 ease-in-out"
    :class="{ 'translate-x-full': !cart.isOpen }"
    role="dialog"
    aria-modal="true"
    aria-labelledby="cartTitle"
  >
    <!-- Header -->
    <div class="flex items-center justify-between p-4 bg-white">
      <p class="text-xl font-medium text-ink" id="cartTitle">My Cart</p>
      <button
        type="button"
        id="close-mini-cart"
        class="text-ink focus-inset"
        aria-label="Close cart dialog"
        @click="cart.toggle"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-6 h-6"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>
    </div>

    <!-- Announcement Bar -->
    <div class="p-4 bg-accent2">
      <p class="text-sm text-white" role="alert">
        Free shipping on orders over $50
      </p>
    </div>

    <!-- Cart Items -->
    <div
      v-if="cart.isEmpty"
      class="p-4 text-center flex-1 flex w-full items-center justify-center text-ink"
    >
      <p v-if="cart.isLoading" class="text-xl text-center">Loading...</p>
      <p v-else class="text-xl text-center">Your cart is empty.</p>
    </div>
    <div class="flex-1 overflow-auto" v-else>
      <cart-item
        v-for="item in cart.items"
        :key="item.id"
        :line="item"
        class="flex p-4 border-b border-gray-200 last:border-b-0"
      ></cart-item>
    </div>

    <!-- Footer -->
    <div
      class="flex items-center self-end w-full justify-between p-4 text-lg font-medium text-gray-800 bg-cream border-t border-ink/20"
    >
      <div class="w-full flex flex-col px-6 text-ink">
        <div class="mb-8">
          <label class="block text-gray-800 font-medium mb-2" for="notes"
            >Order Notes:</label
          >
          <textarea
            class="w-full border-ink/50 border py-2 px-3 m-0 focus:border-accent2 focus:ring-2 focus:ring-accent2 focus:ring-opacity-50 placeholder:text-sm"
            name="notes"
            rows="2"
            placeholder="Add notes about your order (optional)"
            aria-label="Order notes"
          ></textarea>
        </div>

        <div class="flex items-center justify-between mb-2">
          <span class="text-lg font-medium">Subtotal:</span>
          <span class="text-lg font-medium text-ink" aria-live="polite">{{
            money(cart.subtotal)
          }}</span>
        </div>
        <div class="flex items-center justify-between mb-2">
          <span class="text-lg font-medium text-ink">Taxes:</span>
          <span class="text-lg font-medium text-ink">{{
            money(cart.taxes)
          }}</span>
        </div>
        <div class="flex items-center justify-between mb-2">
          <span class="text-lg font-medium text-ink">Shipping:</span>
          <span class="text-lg font-medium text-ink">{{
            money(cart.shipping)
          }}</span>
        </div>
        <div class="flex items-center justify-between mb-2">
          <span class="text-xl font-medium text-ink">Total:</span>
          <span class="text-xl font-medium text-ink">{{
            money(cart.total)
          }}</span>
        </div>

        <button
          @click="cart.checkout"
          type="button"
          class="w-full px-4 py-2 text-white bg-accent2 hover:bg-yellow-600 focus:outline-none focus:bg-yellow-600 transition duration-150 ease-in-out"
          aria-label="Go to Checkout"
        >
          Go to Checkout
        </button>
      </div>
    </div>
  </div>
  <div
    aria-hidden="true"
    @click="cart.toggle"
    class="fixed inset-0 z-40 bg-black/50 opacity-0 transition duration-300 ease-in-out"
    :class="[
      { 'opacity-100': cart.isOpen },
      cart.isOpen ? 'pointer-events-auto' : 'pointer-events-none',
    ]"
  ></div>
</template>

<!--
  <template>
  <div class="relative">
    <button
      @click="isCartOpen = !isCartOpen"
      class="p-2 text-ink hover:text-gray-700 focus:outline-none focus:text-gray-700"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
        class="w-6 h-6"
      >
        <path
          fill="currentColor"
          d="M6 18a2 2 0 11-4 0 2 2 0 014 0zm12 0a2 2 0 11-4 0 2 2 0 014 0zm0-10v6a2 2 0 01-2 2H4a2 2 0 01-2-2V8h16z"
        />
      </svg>

      <span class="ml-2 text-sm font-medium">
        {{ cart.totalItems }}
      </span>
    </button>

    <div
      v-if="isCartOpen"
      class="absolute right-0 z-10 w-64 mt-2 bg-white rounded-lg shadow-lg"
    >
      <div v-if="cart.isEmpty" class="p-4 text-center">Your cart is empty.</div>

      <div v-else>
        <div
          v-for="item in cart.items"
          :key="item.key"
          class="flex p-4 border-b border-gray-200"
        >
          Item {{ item }}
          <CartItem :item="item" />
        </div>

        <div class="flex p-4 bg-gray-100">
          <div class="flex-1 font-medium text-gray-900">Subtotal</div>
          <div class="font-medium text-gray-900">
            ${{ cart.subtotal.toFixed(2) }}
          </div>
        </div>

        <div class="flex justify-center p-4 pt-0">
          <button
            class="inline-flex items-center justify-center w-full px-4 py-2 text-base font-medium text-white bg-indigo-600 border border-transparent rounded-md shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            @click="cart.checkout"
          >
            Checkout
          </button>
        </div>
      </div>
    </div>
  </div>
</template> -->