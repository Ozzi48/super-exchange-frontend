<script setup>
import axios from "axios";
import AppThemeButton from "./components/AppThemeButton.vue";
import SelectCurrencyInput from "./components/SelectCurrencyInput.vue";
import { ArrowsRightLeftIcon } from "@heroicons/vue/24/solid";
import { onMounted, ref } from "vue";
import { useToast } from "vue-toast-notification";

axios.defaults.baseURL = import.meta.env.VITE_BACKEND_URL;

const $toast = useToast();

const initialValues = {
  amount: "",
  fromCurrency: "",
  toCurrency: "",
};

const formValues = ref(initialValues);
const availableCurrencies = ref([]);
const convertedResult = ref(null);

const fetchAvailableCurrencies = async () => {
  try {
    const url = "/api/currencies";
    const response = await axios.get(url);
    const data = response.data;
    availableCurrencies.value = data;
  } catch (error) {
    console.error("Error fetching currencies:", error.response.data.message);
    $toast.error("Error fetching currencies. Please visit website later", {
      position: "top",
    });
  }
};

onMounted(() => {
  fetchAvailableCurrencies();
});

const handleConvert = async () => {
  try {
    const url = `/api/currencies/convert?sourceCurrency=${formValues.value.fromCurrency}&targetCurrency=${formValues.value.toCurrency}&amount=${formValues.value.amount}`;
    const response = await axios.get(url);
    const data = response.data;
    convertedResult.value = data;
  } catch (error) {
    console.error("Error converting amount:", error);
    $toast.error(error.response.data.message || error.response.data, {
      position: "top",
    });
  }
};

const handleCurrencyChange = (data) => {
  formValues.value[data.key] = data.value;
};
</script>

<template>
  <div class="theme-button-wrapper">
    <AppThemeButton />
  </div>
  <div class="container">
    <div class="mb-24">
      <h1 class="text-4xl font-extrabold text-black dark:text-white mb-5">
        Convert currency
      </h1>
      <div class="flex-none lg:flex flex-row items-end justify-items-center">
        <label class="form-control w-full max-w-xs mr-10">
          <div class="label">
            <span class="label-text text-xl dark:text-white text-black"
              >Amount</span
            >
          </div>
          <input
            v-model="formValues.amount"
            type="number"
            placeholder="Type here"
            class="input input-bordered dark:bg-white w-full max-w-xs input-lg"
          />
        </label>
        <SelectCurrencyInput
          selectLabel="From"
          selectorKey="fromCurrency"
          :availableCurrencies="availableCurrencies"
          :selectedValue="formValues.fromCurrency"
          @update:selectedValue="handleCurrencyChange"
        />
        <div class="hidden lg:inline">
          <ArrowsRightLeftIcon
            class="w-10 h-10 dark:text-white text-black mx-10 mb-3"
          />
        </div>
        <SelectCurrencyInput
          selectLabel="To"
          selectorKey="toCurrency"
          :availableCurrencies="availableCurrencies"
          :selectedValue="formValues.toCurrency"
          @update:selectedValue="handleCurrencyChange"
        />
        <button
          class="btn btn-warning ml-0 lg:ml-10 btn-lg mt-10"
          @click="handleConvert"
        >
          Convert
        </button>
      </div>
      <div v-if="convertedResult">
        <p class="text-xl text-black dark:text-white mt-5">
          {{ formValues.amount }} {{ formValues.fromCurrency }} =
        </p>
        <p class="text-2xl font-extrabold text-black dark:text-white">
          {{ convertedResult }}
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.theme-button-wrapper {
  float: right;
  margin: 20px;
}
</style>
