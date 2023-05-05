<script setup lang="ts">
import {onMounted, ref} from 'vue'
import Btn from "@/components/UI/Btn.vue"
import Form from "@/components/UI/Form.vue"
import FormField from "@/components/UI/FormField.vue"

const API_ID: string = 'b48244e753f14733859621c7e2cd2e99'
const API_URL: string = 'https://openexchangerates.org/api/latest.json'
const errors = ref<string>('')
const conversion = ref<string>('')
const cacheTime = ref<number>(0)
const currentExchangeRate = ref<string | null>(null)

const convertCurrency = async () => {
  const conversionCheck: string[] = conversion.value.trim().toLowerCase().split(' ')
  if (conversionCheck.length !== 4 || isNaN(conversionCheck[0])) {
    errors.value = 'Ошибка при вводе'
    return
  }
  errors.value = ''
  const [amount, from, , to]: string[] = conversionCheck
  const fromUpper: string = from.toUpperCase()
  const toUpper: string = to.toUpperCase()

  let data: any
  const cacheKey: string = `${fromUpper}-${toUpper}`
  if (cacheTime.value > 0) {
    const cachedData: string | null = localStorage.getItem(cacheKey)
    if (cachedData) {
      const {timestamp, data: cachedDataValue}: {timestamp: number, data: {}} = JSON.parse(cachedData)
      const diffInMinutes: number = (new Date().getTime() - timestamp) / (1000 * 60)
      if (diffInMinutes < cacheTime.value) {
        data = cachedDataValue
      }
    }
  }
  if (!data) {
    const response = await fetch(`${API_URL}?app_id=${API_ID}&symbols=${fromUpper},${toUpper}`)
    data = await response.json()
    if (cacheTime.value > 0) {
      localStorage.setItem(cacheKey, JSON.stringify({
        timestamp: new Date().getTime(),
        data
      }))
    }
  }

  if (data.rates[fromUpper] && data.rates[toUpper]) {
    let convertedAmount: string = (amount * data.rates[toUpper] / data.rates[fromUpper]).toFixed(2)
    currentExchangeRate.value = `${amount} ${fromUpper} = ${convertedAmount} ${toUpper}`
  } else {
    currentExchangeRate.value = null
    errors.value = `Конвертация из ${fromUpper} в ${toUpper} не поддерживается данным API`
  }
}
const convertClear = (): void => {
  conversion.value = ''
  currentExchangeRate.value = null
}
</script>

<template>
  <div class="converter">
    <div class="converter-container">
      <div class="converter-errors" :class="{show: errors}">{{ errors }}</div>
      <h1 class="converter-title">Конвертер валют</h1>
      <div class="converter-description">Онлайн конвертер валют, использующий API openexchangerates.org.
        Сайт предоставляет актуальные курсы обмена валют, для быстрой конвертации между валютами.
      </div>
      <div class="converter-rate" v-if="currentExchangeRate">
        Текущий курс по данным openexchangerates.org<br>
        <span>{{ currentExchangeRate }}</span>
        <Btn class="converter-clear danger" @click="convertClear">очистить</Btn>
      </div>
      <Form class="converter-form">
        <FormField
            label="Введите данные для конвертации"
            type="text"
            v-model:value="conversion"
            desc="Например: 2 btc in usd или любой подобный запрос"
        />
        <FormField
            label="Кешировать запрос на время"
            type="number"
            v-model:value="cacheTime"
            desc="Вводится в минутах при необходимости"
            :min="0"
        />
        <Btn class="converter-btn" @click="convertCurrency">Конвертировать</Btn>
      </Form>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import "@/assets/_variables.scss";

.converter {
  &-errors {
    position: fixed;
    top: 0;
    left: 50%;
    background-color: $danger;
    visibility: hidden;
    transform: translate(-50%, -50%);
    opacity: 0;
    transition: all $transition;
    padding: 12px 24px;
    border-radius: $radius;
    min-width: 280px;
    text-align: center;

    &.show {
      visibility: visible;
      transform: translate(-50%, 24px);
      opacity: 1;
    }
  }

  &-container {
    min-height: 100vh;
    max-width: 780px;
    margin: 0 auto;
    padding: 24px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  &-title {
    font-size: 3.5rem;
    font-weight: 700;
    margin-bottom: 4rem;
    text-align: center;
  }

  &-description {
    margin-bottom: 2rem;
    text-indent: 2rem;
    text-align: justify;
  }

  &-rate {
    margin-bottom: 2rem;
    text-align: center;
    font-size: 18px;

    span {
      font-size: 24px;
      font-weight: 700;
    }
  }

  &-clear {
    display: block;
    margin: 12px auto 0;
    background: $danger;
  }

  &-btn {
    display: block;
    margin: 2rem auto 0;
  }
}
</style>
