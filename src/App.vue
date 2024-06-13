<template>
  <Header/>
  <div class="container">
    <Balance :total="+total" :cashtotal="+cashtotal" />
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <TransactionList :transactions="transactions"
    @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"  />
    
  </div>
</template>



<script setup>

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
// import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { ref, computed, onMounted } from 'vue';

import { useToast } from 'vue-toastification';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions){
    transactions.value = savedTransactions;
  }
})



// Get total

const total = computed(() =>{
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});






/////////////////////////////
   




// Get cash on hand total    
const cashtotal1 = computed(() =>{
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount1;
  }, 0);
});



// get cash on hand total when adding gcash balance and the cash on hand total would be decrease by 2%    
const cashtotal2 = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    if (transaction.amount > 0) {
      // Calculate the adjusted amount to subtract from transaction.amount1
      const adjustedAmount1 = transaction.amount - (transaction.amount * 0.02); // Assuming 2% deduction

      // Subtract adjustedAmount1 from acc
      return acc - adjustedAmount1;
    } else {
      // If transaction.amount is negative or zero, do not deduct from acc
      return acc;
    }
  }, 0);
});

// get the over all total of cash on hand
let cashtotal = computed(() => {
  return cashtotal1.value + cashtotal2.value;
});




//////////////////////////////////

// Get income

const income = computed( () => {
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction)=>{
    return acc + transaction.amount;
  }, 0)
    .toFixed(2);
});


// Get expenses

const expenses = computed( () =>{
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction)=>{
    return acc + transaction.amount;
  }, 0)
    .toFixed(2);
});

// Add transaction

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount ? transactionData.amount : 0,   
    amount1: transactionData.amount1 ? transactionData.amount1 : 0,   
    date: transactionData.date
  });

    saveTransactionsToLocalStorage();

    toast.success('Transaction added')
    
};



// Generate unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
  
}; 

//Delete transacation

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  saveTransactionsToLocalStorage();

  toast.success('Transaction deleted');
};


// Save to localstorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>
