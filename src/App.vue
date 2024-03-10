<script setup>
import ProductList from "./components/ProductList.vue";
import Checkout from "./components/Checkout.vue";
import test_console from "./components/testconcole.vue";
</script>

<template>
  <div id="app">
    <header>
      <nav class="navbar d-flex">
        <div>
          <h1 id="t">Clasess</h1>
        </div>
        <div class="cart"> <button type="submit" @click="showCheckout" v-if="disableCart"><i
              class="fa-solid fa-cart-shopping fa-lg"></i><b>Basket {{ cartItemCount }}</b></button>
          <button disabled='disabled' v-else>
            <i class="fa-solid fa-cart-shopping fa-lg"></i><b>Basket {{ cartItemCount }}</b>
          </button>
        </div>
      </nav>
    </header>
    <main>
      <test-component v-if="currentView == ProductList" :lessons="lessons"></test-component>
      <component :is="currentView" :lessons="lessons" :cart="cart" @add-item="addItem" @rm-Item="rmItem">
      </component>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      currentView: ProductList,
      lessons: [],
      cart: [],
    }
  },
  components: {
    ProductList,
    Checkout,
    'test-component': test_console,
  },
  created: function () {
    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("service-worker.js");
    };
  },
  methods: {
    showCheckout() {
      if (this.currentView === Checkout)
        this.currentView = ProductList
      else this.currentView = Checkout;
    },
    addItem: function (lesson) {
      this.cart.push(lesson);
      this.lessons[lesson.id].availability -= 1;
    },
    rmItem: function (carts) {
      this.lessons[carts.id].availability += 1;
      // Find the index of the product in the cart array
      const index = this.cart.findIndex(item => item.id === carts.id);

      // Remove the product from the cart array using the index
      if (index !== -1) {
        this.cart.splice(index, 1);
      }
      if (this.cart.length === 0) {
        this.currentView = ProductList;
      }
    },
  },
  computed: {
    cartItemCount: function () { // the property name
      // its value is calculated when it is called
      return this.cart.length || 0;
    },
    disableCart: function () {
      return this.cartItemCount > 0;
    },

    // isValidName: function () {
    //   return /^[a-zA-Z]+(([',. -][a-zA-Z ])?[a-zA-Z]*)*$/.test(this.order.name);
    // },
    // isValidPhoneNumber: function () {
    //   return /^\d{10}$/.test(this.order.number);
    // },
    // isValidForm: function () {
    //   return this.isValidName && this.isValidPhoneNumber;
    // },
  },
    mounted() {
      // Fetch lessons data from an external JSON file using Fetch API
      //localhost:3000/lessons
      fetch('https://school-classes-env.eba-xbp2nmqm.eu-west-2.elasticbeanstalk.com/lessons')
        .then(response => {
          if (!response.ok) {
            throw new Error('Network response was not ok');
          }
          return response.json();
        })
        .then(data => {
          // Assign the fetched data to the Vue instance's data property
          this.lessons = data;
        })
        .catch(error => {
          console.error('Error fetching lessons:', error);
        });
  },
}
</script>

<style scoped></style>
