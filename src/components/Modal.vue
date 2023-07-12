<script setup>
import { ref, computed } from 'vue';
import Alert from './Alert.vue';
import closeModal from '../assets/img/close.svg';

const error = ref('');

const emit = defineEmits([
  'hide-modal',
  'save-expense',
  'update:name',
  'update:quantity',
  'update:category',
  'delete-expense',
]);

const props = defineProps({
  modal: {
    type: Object,
    required: true,
  },
  name: {
    type: String,
    required: true,
  },
  quantity: {
    type: [String, Number],
    required: true,
  },
  category: {
    type: String,
    required: true,
  },
  available: {
    type: Number,
    required: true,
  },
  id: {
    type: [String, null],
    required: true,
  },
});

const old = props.quantity;

const addExpense = () => {
  const { name, quantity, category, available, id } = props;
  if ([name, quantity, category].includes('')) {
    error.value = 'All fields are required';
    setTimeout(() => {
      error.value = '';
    }, 3000);
    return;
  }

  if (quantity <= 0) {
    error.value = 'Invalid quantity';
    setTimeout(() => {
      error.value = '';
    }, 3000);
    return;
  }

  if (id) {
    if (quantity > old + available) {
      error.value = 'You have reached the limit';
      setTimeout(() => {
        error.value = '';
      }, 3000);
      return;
    }
  } else {
    if (quantity > available) {
      error.value = 'You have reached the limit';
      setTimeout(() => {
        error.value = '';
      }, 3000);
      return;
    }
  }

  emit('save-expense');
};

const isEditing = computed(() => {
  return props.id;
});
</script>

<template>
  <div class="modal">
    <div class="close-modal">
      <img :src="closeModal" opacity @click="$emit('hide-modal')" />
    </div>
    <div
      class="container container-form"
      :class="[modal.animate ? 'animate' : 'close']"
    >
      <form class="new-expense" @submit.prevent="addExpense">
        <legend>{{ isEditing ? 'Save changes' : 'Add expense' }}</legend>

        <Alert v-if="error">{{ error }}</Alert>

        <div class="field">
          <label for="name">Expense name:</label>
          <input
            type="text"
            id="name"
            placeholder="Add a name for the expense"
            :value="name"
            @input="$emit('update:name', $event.target.value)"
          />
        </div>

        <div class="field">
          <label for="quantity">Quantity:</label>
          <input
            type="number"
            id="quantity"
            placeholder="Add the spent quantity, for instance 300"
            :value="quantity"
            @input="$emit('update:quantity', +$event.target.value)"
          />
        </div>

        <div class="field">
          <label for="category">Category:</label>
          <select
            id="category"
            :value="category"
            @input="$emit('update:category', $event.target.value)"
          >
            <option value="">-- Select --</option>
            <option value="savings">Savings</option>
            <option value="food">Food</option>
            <option value="home">Home</option>
            <option value="expenses">Other expenses</option>
            <option value="entertainment">Entertainment</option>
            <option value="health">Health</option>
            <option value="suscriptions">Suscriptions</option>
          </select>
        </div>

        <input
          type="submit"
          :value="[isEditing ? 'Save changes' : 'Add expense']"
        />
      </form>

      <button
        type="button"
        class="btn-delete"
        v-if="isEditing"
        @click="$emit('delete-expense')"
      >
        Delete expense
      </button>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: absolute;
  background-color: rgb(0 0 0 / 0.9);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.close-modal {
  position: absolute;
  right: 3rem;
  top: 3rem;
}
.close-modal img {
  width: 3rem;
  cursor: pointer;
}

.container-form {
  transition-property: all;
  transition-duration: 300ms;
  transition-timing-function: ease-in;
  opacity: 0;
}
.container-form.animate {
  opacity: 1;
}

.container-form.close {
  opacity: 0;
}

.new-expense {
  margin: 10rem auto 0 auto;
  display: grid;
  gap: 2rem;
}
.new-expense legend {
  text-align: center;
  color: var(--white);
  font-size: 3rem;
  font-weight: 700;
}
.field {
  display: grid;
  gap: 2rem;
}
.new-expense input,
.new-expense select {
  background-color: var(--light-gray);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
}
.new-expense label {
  color: var(--white);
  font-size: 3rem;
}
.new-expense input[type='submit'] {
  background-color: var(--blue);
  color: var(--white);
  font-weight: 700;
  cursor: pointer;
}

.btn-delete {
  border: none;
  padding: 1rem;
  width: 100%;
  background-color: #ef4444;
  font-weight: 700;
  font-size: 1.2rem;
  color: var(--white);
  margin-top: 10rem;
  cursor: pointer;
}
</style>
