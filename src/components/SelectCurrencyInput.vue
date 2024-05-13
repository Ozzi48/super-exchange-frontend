<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  selectLabel: String,
  selectorKey: String,
  selectedValue: String,
  availableCurrencies: Array,
});

const emit = defineEmits(["update:selectedValue"]);

const handleChange = (event) => {
  emit("update:selectedValue", {
    key: props.selectorKey,
    value: event.target.value,
  });
};
</script>

<template>
  <label class="form-control w-full max-w-xs">
    <div class="label">
      <span class="label-text text-xl dark:text-white text-black">
        {{ selectLabel }}
      </span>
    </div>
    <select
      class="select select-bordered select-lg dark:bg-white"
      @change="handleChange"
    >
      <option disabled selected>Pick one</option>
      <option v-for="currency in availableCurrencies" :key="currency">
        {{ currency }}
      </option>
    </select>
  </label>
</template>
