<script setup>
import ProductList from "./components/ProductList.vue";
import Checkout from "./components/Checkout.vue";
import test_console from "./components/testconcole.vue";
import checkoutform from "./components/checkoutform.vue";
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
      <div class="chform" v-if="currentView == Checkout">
        <checkoutform @submit-Form="submitForm"></checkoutform>
      </div>
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
    "checkoutform": checkoutform,
  },
  created: function () {
    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("serviceworker.js");
    };
  },
  methods: {
    setLessonAvailability(lessonID, availability) {
      //Assuming you have a route on the server to handle updates, replace '/lessons' with the actual route
      fetch(`https://school-classes-env.eba-xbp2nmqm.eu-west-2.elasticbeanstalk.com/lessons/${lessonID}`, {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ availability: availability }),
      }).then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      }).then(json => {
        console.log("Lesson availability updated:", json);
      }).catch(error => {
        console.error('Error updating lesson availability:', error);
      })
        .catch(error => {
          console.error('Error submitting order:', error);
        });

    },
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
    submitForm(order) {

      const newOrder = {
        full_name: order.name,
        phone_number: order.number,
        lessons: this.cart.map(item => item._id),
        number_of_items: this.cart.length
      };

      // Identify the lessons in the submitted order
      const ids = this.cart.map(item => item._id);

      // Decrease the availability count for each ordered lesson
      ids.forEach(lessonId => {
        const orderedLesson = this.lessons.find(lesson => lesson._id === lessonId);
        console.log(orderedLesson);
        if (orderedLesson) {
          // Decrease the availability count based on the number of items ordered
          this.setLessonAvailability(lessonId, orderedLesson.availability);
        }
      });


      console.log(ids);

      fetch("https://school-classes-env.eba-xbp2nmqm.eu-west-2.elasticbeanstalk.com/orders", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(newOrder),
      }).then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      }).then(json => {
        console.log("Success: " + json.acknowledged);
      }).catch(error => {
        console.error('Error submitting order:', error);
      });
      alert('Order submitted!')
      // Remove all elements from the cart array
      this.cart.splice(0, this.cart.length);
      this.currentView = ProductList;
      order.name = '';
      order.number = '';
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
