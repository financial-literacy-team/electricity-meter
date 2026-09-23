<script setup lang="ts">
defineProps<{
  previousReading: number
  tariffPerKwh: number
}>()
</script>

<template>
  <div class="bill-paper">
    <div class="bill-header">
      <div class="stamp">КИЇВЕНЕРГО</div>
      <div class="bill-title">КВИТАНЦІЯ НА ОПЛАТУ</div>
    </div>

    <div class="bill-body">
      <div class="row">
        <span>Особовий рахунок:</span>
        <strong>2408-119-03</strong>
      </div>
      <div class="row">
        <span>Попередні показники:</span>
        <span class="badge">{{ previousReading }} кВт·год</span>
      </div>
      <div class="row">
        <span>Поточний тариф:</span>
        <span>{{ tariffPerKwh.toFixed(2) }} ₴ / кВт·год</span>
      </div>
      <div class="note-box">
        💡 <em>Правило: Вносити слід лише <strong>цілу частину (чорні цифри)</strong> без десяткових часток!</em>
      </div>
    </div>
  </div>
</template>

<style scoped>
.bill-paper {
  background: #fdfaf3;
  color: #3f2f23;
  border: 1px dashed #d1c4b2;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.12);
  font-family: 'NunitoSans', sans-serif;
  width: 100%;
  max-width: 320px;
}

.bill-header {
  border-bottom: 2px solid #5c3d2e;
  padding-bottom: 8px;
  margin-bottom: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stamp {
  font-size: 11px;
  font-weight: 800;
  border: 2px solid #d63031;
  color: #d63031;
  padding: 2px 6px;
  border-radius: 4px;
  transform: rotate(-4deg);
}

.bill-title {
  font-size: 13px;
  font-weight: 800;
  color: #5c3d2e;
}

.bill-body {
  display: flex;
  flex-direction: column;
  gap: 8px;
  font-size: 14px;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.badge {
  background: #efe3d3;
  padding: 2px 6px;
  border-radius: 4px;
  font-weight: 700;
}

.note-box {
  margin-top: 8px;
  font-size: 12px;
  background: #fff3cd;
  color: #856404;
  padding: 8px;
  border-radius: 6px;
  line-height: 1.35;
}
</style>