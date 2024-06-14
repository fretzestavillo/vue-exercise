<template>
   <h3>Add new transaction</h3>
      <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
          <label for="text">Text</label>
          <input type="text" id="text" v-model="text" placeholder="Enter text..." />
        </div>
        <div class="form-control">
          <label for="amount"
            >Amount</label>
          <input type="text" id="amount" v-model="amount" placeholder="Enter amount..." />
        </div>
        <button class="btn">Add transaction</button>
      </form>
</template>

<script setup>

import { ref } from 'vue';
import { useToast } from 'vue-toastification';

const text = ref('');
const amount = ref('');
const amount1 = ref('');                       
const toast = useToast();
const emit = defineEmits(['transactionSubmitted']);








const onSubmit = () => {
  if(!text.value || !amount.value){
    toast.error('Both fiels must be filled');
    return;
  }
  if (!text.value || !amount.value) {
    toast.error('Both fields must be filled');
    return;
  }

  let transactionData;

  if (text.value === "cash on hand" || text.value === "CASH ON HAND") {
    transactionData = {
      text: text.value.toLowerCase(),
      amount1: parseFloat(amount.value),
      date: new Date().toLocaleString('en-US', {
        hour12: true,
        year: 'numeric',
        month: '2-digit',
        day: '2-digit',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
      })
    };
  } else {
    

    transactionData = {
      text: text.value.toLowerCase(),
      amount: parseFloat(amount.value),
      date: new Date().toLocaleString('en-US', {
        hour12: true,
        year: 'numeric',
        month: '2-digit',
        day: '2-digit',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
      })
    };
  }

  emit('transactionSubmitted', transactionData);

  text.value = '';
  amount.value = '';



};
</script>
