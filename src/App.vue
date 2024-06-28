<template>
  <Header></Header>
  <div class="container">
    <Balance :total="total"/>
    <IncomeExpenses :income="+income" :expense="+expense"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted($event)"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted($event)"/>
  </div>

</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { computed, ref ,onMounted} from 'vue';
import {useToast} from 'vue-toastification';

const toast=useToast();

const transactions=ref([]);

onMounted(()=>{
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if(savedTransactions){
    transactions.value=savedTransactions;
  }
});

        //get total which reflects in balance of expense tracker.
        const total=computed(() =>{
          return transactions.value.reduce((acc,curr) => {
           return  acc + curr.amount}, 0);
        });

        //get income
        const income=computed(() =>{
          return transactions.value
          .filter((transactions)=>transactions.amount>=0)
          .reduce((acc,curr) => {
           return  acc + curr.amount}, 0).toFixed(2);
        });

        //get expense
        const expense=computed(() =>{
          return transactions.value
          .filter((transactions)=>transactions.amount<0)
          .reduce((acc,curr) => {
           return  acc + curr.amount}, 0).toFixed(2);
        });
        
        //Add transactions
        const handleTransactionSubmitted=(transactionData)=>{
          transactions.value.push(
            {
              id:generateUniqueId(),
              text:transactionData.text,
              amount:transactionData.amount
            } );
            saveTransactionsToLocalStorage();

            toast.success('Added transaction Successfully')
        };


        //generate unique id

        const generateUniqueId=()=>{
          return Math.floor(Math.random() * 1000000);
        }
        
        //delete a transaction
      
  const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactionsToLocalStorage();
 toast.success('Transaction deleted successfully');


        };

        //save transactions to local storage
        const saveTransactionsToLocalStorage = ()=>{
          localStorage.setItem('transactions', JSON.stringify(transactions.value));
        }

   
</script>