<template>
  <div>
    <AddTransaction @add-transaction="addTransaction" />
    <TransactionList :transactions="transactions" @delete-transaction="deleteTransaction" />
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import AddTransaction from "./components/AddTransaction.vue";
import TransactionList from "./components/TransactionList.vue";

export default {
  components: {
    AddTransaction,
    TransactionList
  },
  setup() {
    const transactions = ref([]);

    const loadTransactions = () => {
      const savedTransactions = localStorage.getItem("transactions");
      if (savedTransactions) {
        transactions.value = JSON.parse(savedTransactions);
      }
    };

    const saveTransactions = () => {
      localStorage.setItem("transactions", JSON.stringify(transactions.value));
    };

    const addTransaction = (newTransaction) => {
      transactions.value.push(newTransaction);
      saveTransactions();
    };

    const deleteTransaction = (index) => {
      transactions.value.splice(index, 1);
      saveTransactions();
    };

    onMounted(() => {
      loadTransactions();
    });

    return {
      transactions,
      addTransaction,
      deleteTransaction
    };
  }
};
</script>