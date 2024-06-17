<template>
    <div class="addItem">
        <input type="text" placeholder="Agregar una nueva tarea" v-model="item.title"/>
        <font-awesome-icon icon="plus-square" 
        @click="addItem"
        :class="[item.title ? 'active' : 'inactive', 'plus']"
        />
    </div>
</template>
<script>    
import axios from 'axios';
    export default {
        data() {
            return {
                item: {
                    title: '',
                    status: 'pending'
                }
            }
        },
        methods: {
                addItem() {
                        if (this.item.title == '') {
                            return;
                        }
                    
                        axios.post('/api/item/store', {
                            item : this.item
                        })

                        .then(response => {
                            if (response.status == 201) {
                             this.item.title = "";
                             this.$emit('reloadlist');
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
    .addItem {
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 20px; 

    }
    input {
        width: 80%;
        height: 30px;
        border: 1px solid #ccc;
        border-radius: 5px;
        padding: 5px;
    }

    .plus {
        color: #ccc;
        cursor: pointer;
        margin-left: 10px;
    }

    .active {
        color: green;
    }

    .inactive {
        color: rgb(87, 87, 87);
    }
</style>