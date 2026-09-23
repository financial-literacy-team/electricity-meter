<script setup lang="ts">
const emit = defineEmits<{
  (e: 'input', digit: string): void
  (e: 'delete'): void
  (e: 'submit'): void
}>()

const keys = ['1', '2', '3', '4', '5', '6', '7', '8', '9', 'C', '0', '⌫']

const handleKey = (key: string) => {
  if (key === 'C') {
    emit('delete') // clear
  } else if (key === '⌫') {
    emit('delete')
  } else {
    emit('input', key)
  }
}
</script>

<template>
  <div class="numpad">
    <button
        v-for="k in keys"
        :key="k"
        class="key-button"
        :class="{ action: k === 'C' || k === '⌫' }"
        type="button"
        @click="handleKey(k)"
    >
      {{ k }}
    </button>
  </div>
</template>

<style scoped>
.numpad {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  width: 100%;
  max-width: 260px;
}

.key-button {
  height: 52px;
  background: #fff;
  border: 2px solid #5c3d2e;
  border-radius: 10px;
  font-size: 22px;
  font-weight: 800;
  color: #5c3d2e;
  cursor: pointer;
  box-shadow: 0 4px 0 #5c3d2e;
  transition: transform 0.08s ease, box-shadow 0.08s ease;
}

.key-button:hover {
  background: #fdf6e2;
}

.key-button:active {
  transform: translateY(3px);
  box-shadow: 0 1px 0 #5c3d2e;
}

.key-button.action {
  background: #fbe6e6;
  color: #c0392b;
  border-color: #c0392b;
  box-shadow: 0 4px 0 #c0392b;
}

.key-button.action:active {
  box-shadow: 0 1px 0 #c0392b;
}
</style>