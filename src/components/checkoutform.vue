<template>
    <div>
    <form @submit.prevent="submitForm">
        <h2>Checkout</h2>
        <label>
            <strong>First Name:</strong>
            <!-- This input field is bound to 'firstName' in the 'order' object -->
            <input v-model.trim="order.name" required />
        </label>
        <label>
            <strong>Phone Number:</strong>
            <!-- This input field is bound to 'lastName' in the 'order' object -->
            <input v-model="order.number" required />
        </label>
        <h2>Customer Information</h2>
        <p>Name: {{ order.name }}</p>
        <p>Number: {{ order.number }}</p>
        <button :disabled="!isValidForm">Place Order</button>
    </form>
    </div>
</template>

<script>
export default {
    name: "checkoutform",
    data() {
        return {
            order: {
                name: '',
                number: ''
            },
        }
    },
    methods: {
        submitForm: function () {
            this.$emit("submit-Form",this.order);
        },
    },
    computed: {

        isValidName: function () {
            return /^[a-zA-Z]+(([',. -][a-zA-Z ])?[a-zA-Z]*)*$/.test(this.order.name);
        },
        isValidPhoneNumber: function () {
            return /^\d{10}$/.test(this.order.number);
        },
        isValidForm: function () {
            return this.isValidName && this.isValidPhoneNumber;
        },
    }
}
</script>