<script setup>
import { computed } from 'vue';
import CircleProgress from 'vue3-circle-progress';
import 'vue3-circle-progress/dist/circle-progress.css';
import { formatQuantity } from '../helpers';

defineEmits(['reset-app']);

const props = defineProps({
  budget: {
    type: Number,
    required: true,
  },
  available: {
    type: Number,
    required: true,
  },
  spent: {
    type: Number,
    required: true,
  },
});

const percent = computed(() => {
  return parseInt(((props.budget - props.available) / props.budget) * 100);
});
</script>

<template>
  <div class="columns-2">
    <div class="container-graphic">
      <p class="percent">{{ percent }}%</p>

      <CircleProgress
        :percent="percent"
        :size="250"
        :border-width="30"
        :border-bg-width="30"
        fill-color="#3b82f6"
        empty-color="#e1e1e1"
      />
    </div>

    <div class="container-budget">
      <button class="reset-app" type="button" @click="$emit('reset-app')">
        Reset App
      </button>

      <p>
        <span>Budget:</span>
        {{ formatQuantity(budget) }}
      </p>

      <p>
        <span>Available:</span>
        {{ formatQuantity(available) }}
      </p>

      <p>
        <span>Spent:</span>
        {{ formatQuantity(spent) }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.container-graphic {
  position: relative;
}
.percent {
  position: absolute;
  margin: auto;
  top: calc(50% - 1.5rem);
  left: 0;
  right: 0;
  text-align: center;
  z-index: 100;
  font-size: 3rem;
  font-weight: 900;
  color: var(--dark-gray);
}
.columns-2 {
  display: flex;
  flex-direction: column;
}
.columns-2 > :first-child {
  margin-bottom: 3rem;
}

@media (min-width: 768px) {
  .columns-2 {
    flex-direction: row;
    gap: 4rem;
    align-items: center;
  }
  .columns-2 > :first-child {
    margin-bottom: 0;
  }
}

.reset-app {
  background-color: #db2777;
  border: none;
  padding: 1rem;
  width: 100%;
  color: var(--white);
  font-weight: 900;
  text-transform: uppercase;
  border-radius: 1rem;
  transition-property: background-color;
  transition-duration: 300ms;
}
.reset-app:hover {
  cursor: pointer;
  background-color: #c11d67;
}
.container-budget {
  width: 100%;
}
.container-budget p {
  font-size: 2.4rem;
  text-align: center;
  color: var(--dark-gray);
}
@media (min-width: 768px) {
  .container-budget p {
    text-align: left;
  }
}
.container-budget span {
  font-weight: 900;
  color: var(--blue);
}
</style>
