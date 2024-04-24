<script>
export default {
    props: {
        books: { type: Array, default: [] },
        activeIndex: { type: Number, default: -1 },
    },
    emits: ["update:activeIndex"],
    methods: {
        updateActiveIndex(index) {
            this.$emit("update:activeIndex", index);
        }
    }
};
</script>

<template>
    <ul class="list-group pt-3">
        <li
            class="list-group-item d-flex align-items-center"
            v-for="(book, index) in books"
            :key="book._id"
            :class="{ active: index === activeIndex}"
            @click="updateActiveIndex(index)"
        >
            
            <div>
                <h5 class="mb-1">{{ book.TENSACH }}</h5>
                <p class="mb-1">Mã sách: {{ book.MASACH }}</p>
            </div>
        </li>
    </ul>
</template>
