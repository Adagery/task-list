<template>
    <div>
        <div v-for="(item, index) in items" :key="index">
            <list-item
                :item="item"
                class="item"
                v-on:itemChanged="$emit('reloadlist')" ></list-item>
        </div>
    </div>
</template>

<script>
import listItem from "./listItem.vue"

export default {
    props: ['items'],
    components: {
        'list-item': listItem
    }
}
</script>

<style scoped>
    .item {
        background-color: #ffffff;
        padding: 5px;
        margin-top: 5px;
    }
</style>