<script setup lang="ts">
import {computed} from 'vue'

const props = defineProps<{
  value: string // e.g., "04218.5"
}>()

const digits = computed(() => {
  // Normalize to 5 main digits + 1 red decimal digit
  const [whole = '00000', decimal = '0'] = props.value.split('.')
  const paddedWhole = whole.padStart(5, '0').slice(-5)
  return {
    whole: paddedWhole.split(''),
    decimal: decimal.charAt(0) || '0'
  }
})
</script>

<template>
  <div class="meter-counter-box">
    <div class="meter-brand">
      <span class="model-text">РОСТОК СО-ЕА09М</span>
      <span class="voltage-text">220V 5(60)A 50Hz</span>
    </div>

    <div class="digits-frame">
      <div
          v-for="(digit, i) in digits.whole"
          :key="`whole-${i}`"
          class="digit-drum black"
      >
        <div class="digit-text">{{ digit }}</div>
      </div>

      <div class="digit-separator">,</div>

      <div class="digit-drum red">
        <div class="digit-text">{{ digits.decimal }}</div>
      </div>
      <span class="unit-label">кВт·год</span>
    </div>

    <div class="meter-disk-container">
      <div class="disk-groove">
        <div class="disk-indicator"></div>
      </div>
      <span class="pulse-led" title="A = 3200 imp/(kW·h)"></span>
    </div>
  </div>
</template>

<style scoped>
.meter-counter-box {
  background: #1c1c1f;
  border: 4px solid #2e2f33;
  border-radius: 12px;
  padding: 16px 20px;
  box-shadow: inset 0 4px 12px rgba(0, 0, 0, 0.7), 0 8px 24px rgba(0, 0, 0, 0.4);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  user-select: none;
}

.meter-brand {
  display: flex;
  justify-content: space-between;
  width: 100%;
  font-family: 'Courier New', monospace;
  font-size: 11px;
  color: #8c8f99;
  letter-spacing: 0.5px;
}

.digits-frame {
  display: flex;
  align-items: center;
  gap: 4px;
  background: #0d0d0e;
  padding: 6px 12px;
  border-radius: 6px;
  border: 2px solid #33343a;
}

.digit-drum {
  width: 32px;
  height: 48px;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Courier New', Courier, monospace;
  font-size: 32px;
  font-weight: 800;
  box-shadow: inset 0 6px 6px rgba(0, 0, 0, 0.6), inset 0 -6px 6px rgba(0, 0, 0, 0.6);
}

.digit-drum.black {
  background: #111;
  color: #f5f5f5;
  border: 1px solid #222;
}

.digit-drum.red {
  background: #a72323;
  color: #fff;
  border: 1px solid #781818;
}

.digit-separator {
  color: #fff;
  font-size: 24px;
  font-weight: 900;
  padding: 0 2px;
}

.unit-label {
  color: #cfd2dc;
  font-size: 12px;
  font-weight: 700;
  margin-left: 8px;
}

.meter-disk-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  padding-top: 4px;
}

.disk-groove {
  width: 80px;
  height: 10px;
  background: #09090a;
  border-radius: 5px;
  overflow: hidden;
  position: relative;
}

.disk-indicator {
  position: absolute;
  top: 0;
  left: 0;
  width: 25px;
  height: 100%;
  background: linear-gradient(90deg, #d32f2f, #ff7675);
  animation: rotate-disk 2.2s linear infinite;
}

@keyframes rotate-disk {
  0% {
    transform: translateX(-30px);
  }
  100% {
    transform: translateX(85px);
  }
}

.pulse-led {
  width: 10px;
  height: 10px;
  background-color: #ff3838;
  border-radius: 50%;
  box-shadow: 0 0 8px #ff3838;
  animation: blink-imp 1.4s ease-in-out infinite;
}

@keyframes blink-imp {
  0%, 85%, 100% {
    opacity: 0.2;
  }
  92% {
    opacity: 1;
  }
}
</style>