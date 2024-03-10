<template>
    <div>
        <div class="card" v-for="lesson in lessons">
            <div class="info">
                <p><b>Subject: </b>{{ lesson.subject }}</p>
                <p><b>Location: </b>{{ lesson.location }}</p>
                <p><b>Price: </b>£{{ lesson.price }}</p>
                <p><b>Availability: </b>{{ lesson.availability }}</p>
            </div>
            <i :class="lesson.icon"></i>
            <button disabled='disabled' v-if="lesson.availability === 0"><b>Add to Cart</b></button>
            <button @click="addItem(lesson)" v-if="lesson.availability !== 0"><b>Add to Cart</b></button>
            <span v-if='lesson.availability === 0'>All out!</span>
            <span v-else-if="lesson.availability < 5">
                Only {{ lesson.availability }} left!
            </span>
            <span v-else>Buy now!</span>
        </div>
    </div>
</template>

<script>
export default {
    name: "ProductList",
    props: [
        "lessons",
    ],
    methods: {
        addItem: function (lesson) {
            this.$emit("add-item", lesson);
        },
    },
}
</script>
