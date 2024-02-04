<script setup>
  import {ref, computed, onMounted} from 'vue'
  import Header from './components/Header.vue'
  import Balance from '.components/Balace.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import TransactionList from './components/TransactionList.vue';
  const transactions = ref([])
 
  const total = computed(() => {
    return transactions.value.reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
  })

  //get income
  const income = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
  })


  //get expense
  const expense = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
  })

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount,
    })
    // console.log(transactions)
    // console.log(JSON.stringify(transactions.value))

    saveTransactionsToLocalStorage()
  }


  const generateUniqueId = () => {
    return Math.floor(Math.random() * 10000000)
  }

  //delete transaction
  const handleTransactionsDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
    saveTransactionsToLocalStorage()
  }

  //save to local storage
  const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }

  //first loads
  onMounted( () => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if(savedTransactions)
    {
      transactions.value = savedTransactions
    }
  })
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="+total"> </Balance>
    <IncomeExpenses :income="+income" :expense="+expense"></IncomeExpenses>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionsDeleted"></TransactionList>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"></AddTransaction>
    <!-- {{ transactions }} -->
  </div>
</template>