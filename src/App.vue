<template>
  <Header/>
  <div class="container">
    <Balance :total="+total" :cashtotal="+cashtotal" />
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"    /> 
    <Save :exelList="exelList" @saveAndExport="handleSaveAndExport" />
    <Delete :transactions="transactions" @allTransactionDeleted="handleAllTransactionDeleted" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
   
    
    
  </div>
</template>



<script setup>

import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import Delete from './components/Delete.vue';
import Save from './components/Save.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';
import * as XLSX from 'xlsx';
import { invalidateTypeCache } from 'vue/compiler-sfc';




const toast = useToast();
const transactions = ref([]);
const exelList = ref([]);


onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions){
    transactions.value = savedTransactions;
  }

  const savedTransactions1 = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions1){
    exelList.value = savedTransactions1;
  }

})






// Add transaction


const handleTransactionSubmitted = (transactionData) => {
  const newTransaction = {
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount ? transactionData.amount : 0,   
    amount1: transactionData.amount1 ? transactionData.amount1 : 0,   
    date: transactionData.date
  };

   // Push newTransaction into transactions.value
  transactions.value.push(newTransaction);

  // Alternatively, you can also push into exelList.value if needed
   exelList.value.push(newTransaction);
  // exelList.value = [...exelList.value, newTransaction];

  saveTransactionsToLocalStorage();
  saveTransactionsToLocalStorage1();

  toast.success('Transaction added');

};















////////////this code is trying to insert object and push it into array exelList



// Get GCash total
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




// Get Income

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
















// // Get GCash total
// const total = computed(() =>{
//   return transactions.value.reduce((acc, transaction) => {
//     return acc + transaction.amount;
//   }, 0);
// });




// // get the over all total of cash on hand
// let cashtotal = computed(() => {
//   return cashtotal1.value + cashtotal2.value;
// });




// // Get cash on hand total    
// const cashtotal1 = computed(() =>{
//   return transactions.value.reduce((acc, transaction) => {
//     return acc + transaction.amount1;
//   }, 0);
// });



// const cashtotal2 = computed(() => {
//   return transactions.value.reduce((acc, transaction) => {
//     if (transaction.amount > 0) {
//       let adjustedAmount1;
//       if (transaction.amount < 500) {
//         // Deduct 10 if amount is less than 500
//         adjustedAmount1 = transaction.amount - 10;
//       } else {
//         // Calculate deduction based on 1000 - 20 rule
//         const units = Math.ceil(transaction.amount / 500);
//         const deduction = units * 10;
//         adjustedAmount1 = transaction.amount - deduction;
//       }

//       // Subtract adjustedAmount1 from acc
//       return acc - adjustedAmount1;
//     } else {
//       // If transaction.amount is negative or zero, do not deduct from acc
//       return acc;
//     }
//   }, 0);
// });



// function calculateDeduction(transactionAmount) {
//   if (transactionAmount < 500) {
//     return 10; // Deduct 10 if amount is less than 500
//   } else {
//     const units = Math.ceil(transactionAmount / 500);
//     return units * 10; // Calculate deduction based on 1000 - 20 rule
//   }
// }




// // Get Income

// const income = computed(() => {
//   return transactions.value.reduce((acc, transaction) => {
//     if (transaction.amount > 0) {
//       const deduction = calculateDeduction(transaction.amount);
//       return acc + deduction;
//     } else {
//       // If transaction.amount is negative or zero, do not add to acc
//       return acc;
//     }
//   }, 0);
// });

  


// // Get expenses

// const expenses = computed( () =>{
//   return transactions.value
//   .filter((transaction) => transaction.amount < 0)
//   .reduce((acc, transaction)=>{
//     return acc + transaction.amount;

//   }, 0)
//     .toFixed(2);
// });













// Generate unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
  
}; 

//Delete transacation

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  saveTransactionsToLocalStorage();
  saveTransactionsToLocalStorage1();

  toast.success('Transaction deleted');
};


//Delete All transacation

const handleAllTransactionDeleted = () => {

  transactions.value = []; // Remove all transactions

  saveTransactionsToLocalStorage();
  saveTransactionsToLocalStorage1();

  toast.warning('All transactions deleted');

};





// Save and export ////////////////////////

const data = ref([]);

const handleSaveAndExport = () => {

   data.value = [];

  exelList.value.forEach(i => {
    data.value.push({
      Name: i.text,
      GCashAmount: i.amount,
      CashOnhandAmount: i.amount1,
      total: i.total,
      cashtotal: i.cashtotal,
      income: i.income,
      expenses: i.expenses,
      total: total.value,
      cashtotal: cashtotal.value,
      income: income.value,
      expenses: expenses.value,
      Date: i.date,

    });
    
      

   
    
  });


  // Step 2: Prepare data for Excel export
  const ws = XLSX.utils.json_to_sheet(data.value);
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'Transactions');

  // Step 3: Generate and save Excel file
  XLSX.writeFile(wb, 'transactions.xlsx');
};









// // Save and export

// const data = ref([]);

// const handleSaveAndExport = () => {

//    data.value = [];

//   transactions.value.forEach(transaction => {
//     data.value.push({
//       Date: transaction.date,
//       Name: transaction.text,
//       GCashAmount: transaction.amount,
//       CashOnhandAmount: transaction.amount1,
//       GCashBalance: total,
//       CashOnHand: cashtotal,
//       Expenses: expenses,
//       Income: income,

//     });
//   });


//   // Step 2: Prepare data for Excel export
//   const ws = XLSX.utils.json_to_sheet(data.value);
//   const wb = XLSX.utils.book_new();
//   XLSX.utils.book_append_sheet(wb, ws, 'Transactions');

//   // Step 3: Generate and save Excel file
//   XLSX.writeFile(wb, 'transactions.xlsx');
// };





// Save to localstorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}


const saveTransactionsToLocalStorage1 = () => {
  localStorage.setItem('exelList', JSON.stringify(exelList.value));
}

</script>
