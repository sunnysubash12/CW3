<template>
    <div>
        <h2>Your current items in the cart are ({{ totalItemsInTheCart }} in total): </h2>
        <div>
            <div class="card" v-for="product in cart">
                <div class="info">
                    <p><b>Subject: </b>{{ product.subject }}</p>
                    <p><b>Location: </b>{{ product.location }}</p>
                    <p><b>Price: </b>£{{ product.price }}</p>
                </div>
                <i :class="product.icon"></i>
                <button @click="rmItem(product)"><b>Remove</b></button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Checkout",
    props: [
        "cart",
    ],
    methods: {
        rmItem: function (lesson) {
            this.$emit("rm-Item", lesson);
        }

    },
    computed: {
        totalItemsInTheCart: function () {
            return this.cart.length || "";
        },
    }
}
</script>
