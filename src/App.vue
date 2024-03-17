<template>
    <Header />
    <div class="container">
        <Balance :total="+total" />
        <IncomeExpenses :income="+income" :expenses="+expenses" />
        <TransactionList @transactionDeleted="handleTransactionDeleted" :transactions="transactions" />
        <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
    </div>
</template> 

<script setup>
    import Header from "./components/Header.vue";
    import Balance from "./components/Balance.vue";
    import IncomeExpenses from "./components/IncomeExpenses.vue";
    import TransactionList from "./components/TransactionList.vue";
    import AddTransaction from "./components/AddTransaction.vue";

    import { useToast } from "vue-toastification";

    import { ref, computed, onMounted } from 'vue';

    const toast = useToast();

    // send transaction
    const transactions = ref([]);

    onMounted(() => {
        const savedTransaction = JSON.parse(localStorage.getItem('transactions'));

        if (savedTransaction) {
            transactions.value = savedTransaction;
        }
    });

    // get total
    const total = computed(() => {
        return transactions.value
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0);
    });

    // get income
    const income = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0);
    });

    // get expenses
    const expenses = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0);
    });

    // Add transaction
    const handleTransactionSubmitted = (transactionData) => {
        transactions.value.push({
            id: generateId,
            text: transactionData.text,
            amount: transactionData.amount
        });

        savedTransactionToLocalStorage();

        toast.success('Transaction added');
    };

    // generate unique id
    const generateId = () => {
        return Math.floor((Math.random) * 10000);
    };

    // Delete Transaction
    const handleTransactionDeleted = (id) => {
        transactions.value = transactions.value.filter((transactions) => transactions.id !== id);

        savedTransactionToLocalStorage();

        toast.success('Transaction Deleted');
    };

    // Save to local storage
    const savedTransactionToLocalStorage = () => {
        localStorage.setItem('transactions', JSON.stringify(transactions.value));
    }
</script>