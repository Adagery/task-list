<template>
    <div class="item">
        <input type="checkbox" 
        @change="updateCheck()"
        v-model="item.completed"
        />
        <span :class="[item.completed ? 'completed' : '', 'itemText']"> {{ item.name }}</span>
        <button @click="removeItem()" class="trashcan">
            <font-awesome-icon icon="trash" />
        </button>
    </div>
</template>
<script>    
import axios from 'axios';
    export default {
        props: ['item'],
        methods: {
            updateCheck(){
                const previousState = this.item.completed;
                axios.put('api/item/' + this.item.id,
                {
                   item: this.item
                })
                .then(response => {
                   if (response.status == 200) {
                       this.$emit('itemChanged');
                   }
                })
                .catch(error => {
                    this.item.completed = previousState;
                    console.log(error);
                });
            },
            removeItem(){
                axios.delete('api/item/' + this.item.id)
                .then(response => {
                    if (response.status == 200) {
                        this.$emit('itemChanged');
                    }
                })
                .catch(error => {
                    console.log(error);
                });
            }
        }
    }
</script>

<style scoped>
    .item {
    background-color: #f9f9f9;
    padding: 10px;
    margin-top: 10px;
    border-radius: 3px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    }
    .itemText {
        font-size: 100%;
        margin-left: 20px;
    }
    .completed {
        text-decoration: line-through;
        color: gray;
    }

    .trashcan {
        background-color: rgba(199, 199, 199, 0);
        color: rgb(255, 10, 10);
        border: none;
        outline: none;
    }

</style>