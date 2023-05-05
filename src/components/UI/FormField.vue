<script lang="ts" setup>
import {ref} from 'vue'

const emit = defineEmits(['update:value'])
defineProps({
  value: null,
  label: {
    type: String,
    default: ''
  },
  type: {
    type: String,
    default: 'text'
  },
  desc: {
    type: String,
    default: ''
  },
  min: {
    type: Number,
    default: 0
  }
})
const id = ref<string>("input-" + Math.random())
const updateValue = (e: Event) => {
  emit('update:value', (e.target as HTMLInputElement).value)
};
</script>

<template>
  <div class="field">
    <label class="label" :for="id">{{ label }}</label>
    <input class="input" :id="id" :type="type" :value="value" :min="min" @input="updateValue">
    <small class="desc" v-if="desc">{{ desc }}</small>
  </div>
</template>

<style lang="scss" scoped>
@import "@/assets/_variables.scss";
.field {
  display: flex;
  flex-direction: column;
  &+& {
    margin-top: 2rem;
  }
}
.label {
  font-size: 24px;
  font-weight: 700;
  margin-bottom: 12px;
}
.input {
  width: 100%;
  color: $dark;
  padding: 12px 24px;
  border-radius: 12px;
  border: none;
  margin-bottom: 6px;
}
input[type="number"] {
  -moz-appearance: textfield;
  -webkit-appearance: textfield;
  appearance: textfield;
}

input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
  display: none;
}
</style>
