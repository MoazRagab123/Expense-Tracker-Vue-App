<template>
  
 <Header/>
 <div class=".container">
   <Balance :total="+total"/>
   <IncomeExpense :income="+income" :expenses="+expenses"/>
   <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
   <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
 </div>

</template>
<script setup>
 import Header from './components/Header.vue';
 import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue'
import { reactive,computed,onUnmounted, onMounted } from 'vue';
import {  toast } from 'vue3-toastify';

const transactions=reactive([]) ;

onMounted(()=>{
  const savedTransactions = JSON.parse(localStorage.getItem
  ('transactions'));

  if(savedTransactions){
    // Reassign the value of `transactions` using Vue's reactive method
    transactions.splice(0, transactions.length, ...savedTransactions);
  }
})
//Get total
const total = computed(()=>
{
  return transactions.reduce((acc,transaction)=>{
    return acc+transaction.amount
  },0)
}
);

//Get income
const income = computed(()=>
{
  return transactions.filter((transaction)=>transaction.amount>0).reduce((acc,transaction)=>{
    return acc+transaction.amount
  },0).toFixed(2);
}
)

//Get expenses
const expenses = computed(()=>
{
  return transactions.filter((transaction)=>transaction.amount<0).reduce((acc,transaction)=>{
    return acc+transaction.amount
  },0).toFixed(2);
}
);
const handleTransactionSubmitted = (transactionData)=>{
 transactions.push({
  id:generateUniqueId(),
  text:transactionData.text,
  amount:transactionData.amount,
 });

 toast.success('Transaction added');
 savedTransactionsToLocalStorage();

}

//Generate unique id
const generateUniqueId = ()=>{
  return Math.floor(Math.random()*10000000);
};

//Delete transaction
const handleTransactionDeleted = (id)=>{
  // Find the index of the transaction to delete
  const index = transactions.findIndex((transaction) => transaction.id === id);

  // If the transaction exists, remove it using splice
  if (index !== -1) {
    transactions.splice(index, 1); // Mutates the array by removing the item at the index
    toast.success('Transaction deleted');
  };

  savedTransactionsToLocalStorage();
}

//Save to localStorage
const savedTransactionsToLocalStorage = ()=>{
  localStorage.setItem('transactions',JSON.stringify(transactions))
}
</script>

