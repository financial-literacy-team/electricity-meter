<script setup lang="ts">
import {ref} from 'vue'
import ElectricityMeterGame, {type MeterGameResult} from './components/ElectricityMeterGame.vue'

const showGame = ref(true)
const log = ref<any>(null)

const onComplete = (res: MeterGameResult) => {
  log.value = res
  alert(`Успіх! Спожито ${res.consumedKwh} кВт·год. До сплати: ${res.totalCost} ₴`)
}

const onCancel = () => {
  showGame.value = false
}
</script>

<template>
  <div style="padding: 24px; font-family: sans-serif;">
    <h1>Тестовий стенд: Міні-гра з лічильником</h1>
    <button @click="showGame = true">Відкрити лічильник</button>

    <ElectricityMeterGame
        v-if="showGame"
        :target-reading-whole="5184"
        :target-reading-decimal="3"
        :previous-reading="4920"
        :tariff="4.32"
        @complete="onComplete"
        @cancel="onCancel"
    />

    <pre v-if="log" style="margin-top: 20px; background: #eee; padding: 12px;">{{ log }}</pre>
  </div>
</template>