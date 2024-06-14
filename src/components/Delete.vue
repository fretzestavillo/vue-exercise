<template>
        <button class="btn1">Save</button>
        <button @click="deleteAll(transactions)" class="btn2">Delete</button>
</template>



<script setup>
import { useToast } from 'vue-toastification';
import { defineProps } from 'vue';
import { computed } from 'vue'; 
import { ref } from 'vue';


const props = defineProps({
   transactions :{
    type: Array,
    required: true,
   }
});

const emit = defineEmits(['allTransactionDeleted']);
const deleteAll = (transactions) => {
emit('allTransactionDeleted', transactions)
};




</script>