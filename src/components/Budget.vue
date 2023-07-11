<script setup>
import { ref } from 'vue';

const budget = ref(0);
const error = ref('');

const emit = defineEmits(['define-budget']);

const defineBudget = () => {
  if (budget.value <= 0 || typeof budget.value !== 'number') {
  }
};
</script>

<template>
  <form class="budget" @submit.prevent="defineBudget">
    <div class="field">
      <label for="new-budget">Define budget</label>
      <input
        type="number"
        id="new-budget"
        class="new-budget"
        placeholder="Add your budget"
        min="0"
        v-model.number="budget"
      />
    </div>
    <button type="submit">Define your budget</button>
  </form>
</template>

<style scoped>
.budget {
  width: 100%;
}
.field {
  display: grid;
  gap: 2rem;
}
.budget label {
  font-size: 2.2rem;
  text-align: center;
  color: var(--blue);
}
.budget input[type='number'] {
  background-color: var(--light-gray);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
  text-align: center;
}
.budget button[type='submit'] {
  background-color: var(--blue);
  border: none;
  padding: 1rem;
  text-align: center;
  font-size: 2rem;
  margin-top: 2rem;
  color: var(--white);
  font-weight: 900;
  width: 100%;
  transition: background-color 300ms ease;
}
.budget button[type='submit']:hover {
  background-color: #1048a4;
  cursor: pointer;
}
</style>
