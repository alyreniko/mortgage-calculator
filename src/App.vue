<script setup>
import { ref, computed } from 'vue'
import InputSlider from './components/InputSlider.vue'

const money = ref(5_630_000)
const rate = ref(23)
const years = ref(15)
const downPayment = ref(1_000_000)

// сумма кредита
const loanAmount = computed(() => {
  const value = money.value - downPayment.value
  return value > 0 ? value : 0
})

// месячная ставка
const monthlyRate = computed(() => rate.value / 100 / 12)

// срок в месяцах
const months = computed(() => years.value * 12)

// ежемесячный платёж (аннуитет)
const monthlyPayment = computed(() => {
  const P = loanAmount.value
  const r = monthlyRate.value
  const n = months.value

  if (!P || !r || !n) return 0

  const k = (r * Math.pow(1 + r, n)) / (Math.pow(1 + r, n) - 1)
  return P * k
})

// общая сумма выплат по кредиту (только банку)
const totalPaid = computed(() => monthlyPayment.value * months.value)

// переплата банку (начисленные проценты в рублях)
const overpayment = computed(() => totalPaid.value - loanAmount.value)

// переплата в процентах
const overpaymentPercent = computed(() => {
  if (!loanAmount.value) return 0
  return (overpayment.value / loanAmount.value) * 100
})

// Долг + проценты (итоговая сумма к выплате)
const principalPlusInterest = computed(() => loanAmount.value + overpayment.value)

const formatCurrency = (value) =>
  Number(value).toLocaleString('ru-RU', {
    maximumFractionDigits: 2,
    minimumFractionDigits: 2,
  })

const formatPercent = (value) =>
  Number(value).toLocaleString('ru-RU', {
    maximumFractionDigits: 2,
    minimumFractionDigits: 2,
  })
</script>

<template>
  <div class="space-y-2 w-full max-w-xl mx-auto">
    <!-- Стоимость недвижимости -->
    <div class="rounded-2xl bg-slate-100 px-5 py-4 fit-content">
      <div class="flex items-start justify-between gap-4">
        <div class="text-xs font-semibold uppercase tracking-wide text-slate-500 mb-2">
          Стоимость недвижимости
        </div>
      </div>
      <InputSlider
        v-model="money"
        :min="300000"
        :max="50000000"
        :step="100000"
        number-input-id="money-input"
        :show-scale="true"
        left-label="300,0 тыс ₽"
        right-label="50,0 млн ₽"
      />
    </div>

    <!-- Первоначальный взнос -->
    <div class="rounded-2xl bg-slate-100 px-5 py-4 fit-content">
      <div class="flex items-start justify-between gap-4">
        <div class="text-xs font-semibold uppercase tracking-wide text-slate-500 mb-2">
          Первоначальный взнос
        </div>
      </div>
      <InputSlider
        v-model="downPayment"
        :min="0"
        :max="money"
        :step="50000"
        number-input-id="downpayment-input"
        :show-scale="true"
        left-label="0 ₽"
        :right-label="formatCurrency(money) + ' ₽'"
      />
      <div class="mt-2 text-xs text-slate-500">
        Сумма кредита: <span class="font-semibold">{{ formatCurrency(loanAmount) }} ₽</span>
      </div>
    </div>

    <!-- Процентная ставка -->
    <div class="rounded-2xl bg-slate-100 px-5 py-4 fit-content">
      <div class="flex items-start justify-between gap-4">
        <div class="text-xs font-semibold uppercase tracking-wide text-slate-500 mb-2">
          Процентная ставка
        </div>
      </div>
      <InputSlider
        v-model="rate"
        :min="5"
        :max="30"
        :step="0.1"
        number-input-id="rate-input"
        :show-scale="true"
        left-label="5 %"
        right-label="40 %"
      />
    </div>

    <!-- Срок кредита -->
    <div class="rounded-2xl bg-slate-100 px-5 py-4 fit-content">
      <div class="flex items-start justify-between gap-4">
        <div class="text-xs font-semibold uppercase tracking-wide text-slate-500 mb-2">
          Срок кредита
        </div>
      </div>
      <InputSlider
        v-model="years"
        :min="1"
        :max="30"
        :step="1"
        number-input-id="years-input"
        :show-scale="true"
        left-label="1 год"
        right-label="30 лет"
      />
    </div>

      <!-- Блок с результатами -->
<div class="rounded-2xl bg-slate-100 px-5 py-4 fit-content space-y-2">
  <div class="font-semibold uppercase tracking-wide text-neutral-900 mb-2">
    Результаты расчёта
  </div>

  <div class="flex flex-col sm:flex-row justify-between text-neutral-900">
    <span class="text-neutral-500 mb-1 sm:mb-0">Ежемесячный платёж:</span>
    <span class="font-semibold text-xl">{{ formatCurrency(monthlyPayment) }} ₽</span>
  </div>

  <div class="flex flex-col sm:flex-row justify-between text-neutral-900">
    <span class="text-neutral-500 mb-1 sm:mb-0">Начисленные проценты:</span>
    <span class="font-semibold text-xl">{{ formatCurrency(overpayment) }} ₽</span>
  </div>

  <div class="flex flex-col sm:flex-row justify-between text-neutral-900">
    <span class="text-neutral-500 mb-1 sm:mb-0">Долг + проценты:</span>
    <span class="font-semibold text-xl">{{ formatCurrency(principalPlusInterest) }} ₽</span>
  </div>

  <div class="flex flex-col sm:flex-row justify-between text-neutral-900">
    <span class="text-neutral-500 mb-1 sm:mb-0">Переплата:</span>
    <span class="font-semibold text-red-500 text-xl">{{ formatPercent(overpaymentPercent) }} %</span>
  </div>
</div>

    </div>
</template>

<style scoped>
</style>