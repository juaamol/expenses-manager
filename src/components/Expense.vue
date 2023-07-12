<script setup>
import { formatQuantity, formatDate } from '../helpers/index';
import IconSavings from '../assets/img/icon_savings.svg';
import IconHome from '../assets/img/icon_home.svg';
import IconFood from '../assets/img/icon_food.svg';
import IconExpenses from '../assets/img/icon_expenses.svg';
import IconEntertainment from '../assets/img/icon_entertainment.svg';
import IconHealth from '../assets/img/icon_health.svg';
import IconSubscriptions from '../assets/img/icon_subscriptions.svg';

const availableIcons = {
  savings: IconSavings,
  food: IconFood,
  home: IconHome,
  expenses: IconExpenses,
  entertainment: IconEntertainment,
  health: IconHealth,
  subscriptions: IconSubscriptions,
};

const props = defineProps({
  expense: {
    type: Object,
    required: true,
  },
});

defineEmits(['select-expense']);
</script>

<template>
  <div class="expense shadow">
    <div class="content">
      <img
        :src="availableIcons[expense.category]"
        alt="Icon expense"
        class="icon"
      />
      <div class="details">
        <p class="category">{{ expense.category }}</p>
        <p class="name" @click="$emit('select-expense', expense.id)">
          {{ expense.name }}
        </p>
        <p class="date">
          Fecha:
          <span>{{ formatDate(expense.date) }}</span>
        </p>
      </div>
    </div>

    <p class="quantity">{{ formatQuantity(expense.quantity) }}</p>
  </div>
</template>

<style scoped>
.expense {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}
.content {
  display: flex;
  align-items: center;
  gap: 2rem;
}
.icon {
  width: 5rem;
}
.details p {
  margin: 0 0 1rem 0;
}
.category {
  color: var(--gray);
  font-size: 1.2rem;
  text-transform: uppercase;
  font-weight: 900;
}
.name {
  color: var(--dark-gray);
  font-size: 2.4rem;
  font-weight: 700;
  cursor: pointer;
}
.date {
  color: var(--dark-gray);
  font-size: 1.6rem;
  font-weight: 900;
}
.date span {
  font-weight: 400;
}
.quantity {
  font-size: 3rem;
  font-weight: 900;
  margin: 0;
}
</style>
