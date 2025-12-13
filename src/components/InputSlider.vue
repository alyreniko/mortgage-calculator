<script setup>
import { computed } from 'vue'

const props = defineProps({
  modelValue: {
    type: Number,
    required: true,
  },
  min: {
    type: Number,
    default: 0,
  },
  max: {
    type: Number,
    default: 100,
  },
  step: {
    type: Number,
    default: 1,
  },
  numberInputId: {
    type: String,
    default: 'number-input',
  },
  showScale: {
    type: Boolean,
    default: true,
  },
  leftLabel: {
    type: String,
    default: '',
  },
  rightLabel: {
    type: String,
    default: '',
  },
})

const emit = defineEmits(['update:modelValue'])

const sliderStyle = computed(() => {
  const range = props.max - props.min
  const val = Math.min(Math.max(props.modelValue, props.min), props.max)
  const percentage = range > 0 ? ((val - props.min) / range) * 100 : 0
  return {
    background: `linear-gradient(to right, #22c55e ${percentage}%, #ffffff00 ${percentage}%)`,
  }
})

// на границе ли сейчас значение слайдера
const isAtEdge = computed(() => {
  return props.modelValue <= props.min || props.modelValue >= props.max
})

function onInputNumber(e) {
  const v = Number(e.target.value)
  emit('update:modelValue', v)
}

function onInputRange(e) {
  const v = Number(e.target.value)
  emit('update:modelValue', v)
}
</script>

<template>
  <input
    :id="numberInputId"
    type="number"
    :min="min"
    :max="max"
    :step="step"
    :value="modelValue"
    @input="onInputNumber"
    style="display: block;"
    class="w-full rounded-t-lg border-2 border-b-0 border-slate-300 bg-white px-4 py-2 text-2xl font-medium text-slate-900 shadow-sm outline-none transition focus:border-emerald-500"
  />

  <input
    type="range"
    :min="min"
    :max="max"
    :step="step"
    :value="modelValue"
    @input="onInputRange"
    style="display: block; margin-top: -0.12em;"
    class="range-slider w-full bg-emerald-500"
    :style="sliderStyle"
  />

  <div v-if="isAtEdge" class="max-w-full bg-emerald-100 rounded-lg px-4 py-2 mt-3">
    <p class="my-1 text-xs text-slate-500 break-words whitespace-normal">🛈 Если нужно значение меньше минимума или больше максимума - введите его вручную через поле выше с помощью клавиатуры.</p>
  </div>

  <div v-if="showScale" class="mt-2 flex justify-between text-slate-500">
    <span>{{ leftLabel }}</span>
    <span>{{ rightLabel }}</span>
  </div>
</template>

<style scoped>
/* убираем стрелки у number */
input[type='number']::-webkit-outer-spin-button,
input[type='number']::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type='number'] {
  -moz-appearance: textfield;
}

/* базовая стилизация range под tailwind‑палитру */
.range-slider {
  -webkit-appearance: none;
  appearance: none;
  height: 2px;
  border-radius: 9999px;
  /* background-color: #6ee7b7; */
  outline: none;
}
.range-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 9999px;
  background-color: #22c55e; /* emerald-500 */
  border: 2px solid white;
  box-shadow: 0 0 0 2px #22c55e;
  cursor: pointer;
}
.range-slider::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 9999px;
  background-color: #22c55e;
  border: 2px solid white;
  box-shadow: 0 0 0 2px #22c55e;
  cursor: pointer;
}
.range-slider::-moz-range-track {
  height: 4px;
  border-radius: 9999px;
  background-color: #e2e8f0;
}
</style>
