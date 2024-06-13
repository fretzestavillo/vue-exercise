<template>
  <h3>History</h3>
      <ul id="list" class="list">
      <li v-for="transaction in filteredTransactions" :key="transaction.id" :class="transaction.amount < 0 ? 'minus' : 'plus'">
            <span v-if="transaction.text">{{ transaction.text }}</span>
            <span v-if="transaction.date">{{ transaction.date }}</span>
            <span v-if="transaction.amount !== undefined">{{ transaction.amount }}</span>
            <span v-if="transaction.amount1 !== undefined">{{ transaction.amount1 }}</span>                                   
           <button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
      </li> 
      </ul>
</template>


<script setup>
import { defineProps } from 'vue';
import { computed } from 'vue'; ///////////////////////////////////////


const emit = defineEmits(['transactionDeleted'])

const props = defineProps({
   transactions :{
    type: Array,
    required: true,
   }
});

const filteredTransactions = computed(() => {
  return props.transactions.map(transaction => {
    return Object.fromEntries(
      Object.entries(transaction).filter(([key, value]) => value !== 0)
    );
  });
});







const deleteTransaction = (id) => {
   emit('transactionDeleted', id );
}
</script>