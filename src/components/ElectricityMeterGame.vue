<script setup lang="ts">
import {ref, computed} from 'vue'
import MeterCounter from './MeterCounter.vue'
import UtilityBillCard from './UtilityBillCard.vue'
import Keypad from './Keypad.vue'

export interface MeterGameResult {
  enteredReading: number
  previousReading: number
  consumedKwh: number
  tariff: number
  totalCost: number
  mistakesCount: number
}

const props = withDefaults(
    defineProps<{
      targetReadingWhole?: number
      targetReadingDecimal?: number
      previousReading?: number
      tariff?: number
      maxAttempts?: number
    }>(),
    {
      targetReadingWhole: 4821,
      targetReadingDecimal: 6,
      previousReading: 4671,
      tariff: 4.32, // standard UAH/kWh tariff
      maxAttempts: 3
    }
)

const emit = defineEmits<{
  (e: 'complete', result: MeterGameResult): void
  (e: 'cancel'): void
}>()

const userInput = ref('')
const attempts = ref(0)
const feedbackMessage = ref<string | null>(null)
const isSuccess = ref(false)

const meterDisplayValue = computed(() => {
  return `${props.targetReadingWhole}.${props.targetReadingDecimal}`
})

const handleDigit = (digit: string) => {
  if (userInput.value.length >= 6) return
  userInput.value += digit
  feedbackMessage.value = null
}

const handleDelete = () => {
  userInput.value = userInput.value.slice(0, -1)
  feedbackMessage.value = null
}

const handleSubmit = () => {
  const numericVal = parseInt(userInput.value, 10)

  if (isNaN(numericVal)) {
    feedbackMessage.value = 'Введіть числові показники лічильника!'
    return
  }

  // Common user mistake: they included the red decimal number!
  const targetWithDecimal = parseInt(`${props.targetReadingWhole}${props.targetReadingDecimal}`, 10)
  if (numericVal === targetWithDecimal) {
    attempts.value++
    feedbackMessage.value = 'Увага! Червону цифру (десяті частки) записувати не потрібно!'
    return
  }

  if (numericVal < props.previousReading) {
    attempts.value++
    feedbackMessage.value = 'Поточні показники не можуть бути меншими за попередні!'
    return
  }

  if (numericVal === props.targetReadingWhole) {
    isSuccess.value = true
    const consumed = numericVal - props.previousReading
    const total = Math.round(consumed * props.tariff * 100) / 100

    setTimeout(() => {
      emit('complete', {
        enteredReading: numericVal,
        previousReading: props.previousReading,
        consumedKwh: consumed,
        tariff: props.tariff,
        totalCost: total,
        mistakesCount: attempts.value
      })
    }, 1100)
  } else {
    attempts.value++
    feedbackMessage.value = 'Помилка! Перевірте показники лічильника уважніше.'
  }
}
</script>

<template>
  <div class="meter-game-overlay">
    <div class="meter-game-modal">
      <header class="modal-top">
        <h2 class="title">Показники електролічильника</h2>
        <button class="btn-close" type="button" @click="emit('cancel')">✕</button>
      </header>

      <div class="game-stage">
        <!-- Physical Meter & Real-world Bill -->
        <div class="reference-column">
          <MeterCounter :value="meterDisplayValue"/>
          <UtilityBillCard
              :previous-reading="previousReading"
              :tariff-per-kwh="tariff"
          />
        </div>

        <!-- Input Terminal -->
        <div class="input-column">
          <div class="lcd-input-display" :class="{ success: isSuccess }">
            <span class="label">ВВЕДЕННЯ:</span>
            <span class="digits">{{ userInput || '_____' }}</span>
            <span class="unit">кВт·год</span>
          </div>

          <p v-if="feedbackMessage" class="feedback-banner">
            {{ feedbackMessage }}
          </p>

          <Keypad
              @input="handleDigit"
              @delete="handleDelete"
          />

          <button
              class="submit-button"
              :disabled="!userInput || isSuccess"
              @click="handleSubmit"
          >
            {{ isSuccess ? 'УСПІШНО! ✓' : 'ПІДТВЕРДИТИ ПОКАЗНИКИ ➔' }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.meter-game-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.75);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.meter-game-modal {
  background: #fbf5e8;
  border: 4px solid #5c3d2e;
  border-radius: 18px;
  box-shadow: 0 16px 40px rgba(0, 0, 0, 0.5);
  padding: 24px;
  width: 90vw;
  max-width: 760px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.modal-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 2px dashed #b38b59;
  padding-bottom: 12px;
}

.modal-top .title {
  margin: 0;
  font-size: 24px;
  font-weight: 900;
  color: #5c3d2e;
}

.modal-top .btn-close {
  background: none;
  border: none;
  font-size: 24px;
  color: #5c3d2e;
  cursor: pointer;
  font-weight: bold;
}

.modal-top .btn-close:hover {
  color: #d63031;
}

.game-stage {
  display: flex;
  gap: 24px;
  align-items: flex-start;
  justify-content: center;
  flex-wrap: wrap;
}

.reference-column {
  flex: 1;
  min-width: 280px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  align-items: center;
}

.input-column {
  flex: 1;
  min-width: 280px;
  display: flex;
  flex-direction: column;
  gap: 14px;
  align-items: center;
}

.lcd-input-display {
  width: 100%;
  max-width: 260px;
  height: 60px;
  background: #2b3a2b;
  border: 3px solid #1e261e;
  border-radius: 8px;
  padding: 8px 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: #79d879;
  font-family: 'Courier New', Courier, monospace;
  box-shadow: inset 0 2px 8px rgba(0, 0, 0, 0.6);
}

.lcd-input-display .label {
  font-size: 11px;
  color: #538853;
}

.lcd-input-display .digits {
  font-size: 28px;
  font-weight: 900;
  letter-spacing: 2px;
}

.lcd-input-display .unit {
  font-size: 12px;
}

.lcd-input-display.success {
  background: #184b23;
  color: #6eff89;
  border-color: #27ae60;
}

.feedback-banner {
  margin: 0;
  font-size: 13px;
  font-weight: 700;
  color: #d63031;
  text-align: center;
  max-width: 260px;
}

.submit-button {
  width: 100%;
  max-width: 260px;
  padding: 14px;
  border-radius: 10px;
  border: 2px solid #1e4d2b;
  background: #27ae60;
  color: white;
  font-weight: 800;
  font-size: 15px;
  cursor: pointer;
  box-shadow: 0 4px 0 #1e4d2b;
  transition: transform 0.1s ease;
}

.submit-button:hover:not(:disabled) {
  background: #2ecc71;
}

.submit-button:active:not(:disabled) {
  transform: translateY(2px);
  box-shadow: 0 2px 0 #1e4d2b;
}

.submit-button:disabled {
  filter: grayscale(0.8);
  opacity: 0.6;
  cursor: not-allowed;
}
</style>