<template>
  <Header/>
  <div class="container">
    <Balance :total="+total" :cashtotal="+cashtotal" />
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"    />  
     <Delete  />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
   
    
    
  </div>
</template>



<script setup>


// @allTransactionDeleted="handleAllTransactionDeleted"

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import Delete from './components/Delete.vue';
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




// get the over all total of cash on hand
let cashtotal = computed(() => {
  return cashtotal1.value + cashtotal2.value;
});




// Get cash on hand total    
const cashtotal1 = computed(() =>{
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount1;
  }, 0);
});



const cashtotal2 = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    if (transaction.amount > 0) {
      let adjustedAmount1;
      if (transaction.amount < 500) {
        // Deduct 10 if amount is less than 500
        adjustedAmount1 = transaction.amount - 10;
      } else {
        // Calculate deduction based on 1000 - 20 rule
        const units = Math.ceil(transaction.amount / 500);
        const deduction = units * 10;
        adjustedAmount1 = transaction.amount - deduction;
      }

      // Subtract adjustedAmount1 from acc
      return acc - adjustedAmount1;
    } else {
      // If transaction.amount is negative or zero, do not deduct from acc
      return acc;
    }
  }, 0);
});



function calculateDeduction(transactionAmount) {
  if (transactionAmount < 500) {
    return 10; // Deduct 10 if amount is less than 500
  } else {
    const units = Math.ceil(transactionAmount / 500);
    return units * 10; // Calculate deduction based on 1000 - 20 rule
  }
}

const income = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    if (transaction.amount > 0) {
      const deduction = calculateDeduction(transaction.amount);
      return acc + deduction;
    } else {
      // If transaction.amount is negative or zero, do not add to acc
      return acc;
    }
  }, 0);
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


//Delete All transacation

// const handleAllTransactionDeleted = () => {
//   transactions.value = []; // Remove all transactions

//   saveTransactionsToLocalStorage();

//   toast.success('All transactions deleted');
// };





// Save to localstorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>
