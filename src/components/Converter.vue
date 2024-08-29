<template>
  <div class="container">
    <h1>Currency Converter</h1>
    <div class="form-group">
      <label for="amount">Amount:</label>
      <input type="number" id="amount" v-model.number="amount" />
    </div>
    <div class="form-group">
      <label for="fromCurrency">From:</label>
      <select id="fromCurrency" v-model="fromCurrency">
        <option
          v-for="currency in Object.keys(rates)"
          :key="currency"
          :value="currency"
        >
          {{ currency }}
        </option>
      </select>
    </div>
    <div class="form-group">
      <label for="toCurrency">To:</label>
      <select id="toCurrency" v-model="toCurrency">
        <option
          v-for="currency in Object.keys(rates)"
          :key="currency"
          :value="currency"
        >
          {{ currency }}
        </option>
      </select>
    </div>
    <h2>Converted Amount: {{ convertedAmount.toFixed(2) }} {{ toCurrency }}</h2>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import { fetchRates, getCurrencyFromLocale } from "../api";

const rates = ref({});
const amount = ref(0);
const fromCurrency = ref("USD");
const toCurrency = ref("EUR");
const convertedAmount = ref(0);

const calculateConversion = () => {
  if (rates.value[fromCurrency.value] && rates.value[toCurrency.value]) {
    const rate =
      rates.value[toCurrency.value] / rates.value[fromCurrency.value];
    convertedAmount.value = amount.value * rate;
  }
};

const setDefaultCurrency = () => {
  const userLocale = navigator.language || navigator.languages[0];
  const defaultCurrency = getCurrencyFromLocale(userLocale);
  if (defaultCurrency && rates.value[defaultCurrency]) {
    fromCurrency.value = defaultCurrency;
  }
};

onMounted(async () => {
  try {
    rates.value = await fetchRates();
    setDefaultCurrency();
  } catch (error) {
    console.error("Error fetching exchange rates:", error);
  }
});

watch([amount, fromCurrency, toCurrency, rates], calculateConversion);
</script>

<style>
/* Add your styles here */
</style>
