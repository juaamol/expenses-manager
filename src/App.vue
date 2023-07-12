<script setup>
import { ref, reactive, watch, computed, onMounted } from 'vue';
import Budget from './components/Budget.vue';
import Filters from './components/Filters.vue';
import Modal from './components/Modal.vue';
import BudgetControl from './components/BudgetControl.vue';
import iconNewExpense from './assets/img/new-expense.svg';
import { generateId } from './helpers';

const modal = reactive({
  show: false,
  animate: false,
});
const budget = ref(0);
const spent = ref(0);
const available = ref(0);
const filter = ref('');
const expenses = ref([]);

const expense = reactive({
  name: '',
  quantity: '',
  category: '',
  id: null,
  date: Date.now(),
});

const defineBudget = (quantity) => {
  budget.value = quantity;
  available.value = quantity;
  console.log(quantity);
};

const resetApp = () => {
  if (confirm('Do you want to reset budget and expenses?')) {
    expenses.value = [];
    budget.value = 0;
  }
};

const showModal = () => {
  modal.show = true;
  setTimeout(() => {
    modal.animate = true;
  }, 300);
};

const hideModal = () => {
  modal.animate = false;
  setTimeout(() => {
    modal.show = false;
  }, 300);
};

watch(
  expenses,
  () => {
    const totalSpent = expenses.value.reduce(
      (total, expense) => expense.cantidad + total,
      0,
    );
    spent.value = totalSpent;
    available.value = budget.value - totalSpent;
    localStorage.setItem('expenses', JSON.stringify(expenses.value));
  },
  {
    deep: true,
  },
);

watch(
  modal,
  () => {
    if (!modal.show) {
      reinitializeStateExpenses();
    }
  },
  {
    deep: true,
  },
);

watch(budget, () => {
  localStorage.setItem('budget', budget.value);
});

onMounted(() => {
  const budgetStorage = localStorage.getItem('budget');
  if (budgetStorage) {
    budget.value = Number(budgetStorage);
    available.value = Number(budgetStorage);
  }

  const expensesStorage = localStorage.getItem('expenses');
  if (expensesStorage) {
    expenses.value = JSON.parse(expensesStorage);
  }
});

const saveExpense = () => {
  if (expense.id) {
    const { id } = expense;
    const i = expenses.value.findIndex((expense) => expense.id === id);
    expenses.value[i] = { ...expense };
  } else {
    expenses.value.push({
      ...expense,
      id: generateId(),
    });
  }

  hideModal();
  reinitializeStateExpenses();
};

const reinitializeStateExpenses = () => {
  Object.assign(expense, {
    nombre: '',
    cantidad: '',
    category: '',
    id: null,
    fecha: Date.now(),
  });
};

const selectExpense = (id) => {
  const expenseEdit = expenses.value.filter((expense) => expense.id === id)[0];
  Object.assign(expense, expenseEdit);
  showModal();
};

const deleteExpense = () => {
  if (confirm('Delete?')) {
    expenses.value = expenses.value.filter(
      (expenseState) => expenseState.id !== expense.id,
    );
    hideModal();
  }
};

const filteredExpenses = computed(() => {
  if (filter.value) {
    return expenses.value.filter(
      (expense) => expense.category === filter.value,
    );
  }
  return expenses.value;
});
</script>

<template>
  <div :class="{ fix: modal.show }">
    <header>
      <h1>Expenses Manager</h1>

      <div class="container-header container shadow">
        <Budget v-if="budget === 0" @define-budget="defineBudget" />
        <BudgetControl
          v-else
          :budget="budget"
          :available="available"
          :spent="spent"
          @reset-app="resetApp"
        />
      </div>
    </header>
    <main v-if="budget > 0">
      <Filters v-model:filter="filter" />
      <div class="create-expense">
        <img :src="iconNewExpense" alt="icon new expense" @click="showModal" />
      </div>
      <Modal
        v-if="modal.show"
        @hide-modal="hideModal"
        @save-expense="saveExpense"
        @delete-expense="deleteExpense"
        :modal="modal"
        :available="available"
        :id="expense.id"
        v-model:name="expense.name"
        v-model:quantity="expense.quantity"
        v-model:category="expense.category"
      />
    </main>
  </div>
</template>

<style>
:root {
  --blue: #3b82f6;
  --white: #fff;
  --light-gray: #f5f5f5;
  --gray: #94a3b8;
  --dark-gray: #64748b;
  --black: #000;
}

html {
  font-size: 62.5%;
  box-sizing: border-box;
}

*,
*::before,
*::after {
  box-sizing: inherit;
}

body {
  font-size: 1.6rem;
  font-family: 'Lato', sans-serif;
  background-color: var(--light-gray);
}

h1 {
  font-size: 4rem;
}

h2 {
  font-size: 3rem;
}

header {
  background-color: var(--blue);
}

header h1 {
  padding: 3rem 0;
  margin: 0;
  color: var(--white);
  text-align: center;
}

.container {
  width: 90%;
  max-width: 80rem;
  margin: 0 auto;
}
.container-header {
  margin-top: -5rem;
  transform: translateY(5rem);
  padding: 5rem;
}

.shadow {
  box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  background-color: var(--white);
  border-radius: 1.2rem;
  padding: 5rem;
}

.create-expense {
  position: fixed;
  bottom: 5rem;
  right: 5rem;
}
.create-expense img {
  width: 5rem;
  cursor: pointer;
}

.fix {
  overflow: hidden;
  height: 100vh;
}

.expenses-list {
  margin-top: 10rem;
}
.expenses-list h2 {
  font-weight: 900;
  color: var(--dark-gray);
}
</style>
