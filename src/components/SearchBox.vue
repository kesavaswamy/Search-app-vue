<template>
  <div class="search-bar">
    <input v-model="inputValue" @keyup.enter="onSearch" type="text" placeholder="Enter search text..." />
    <button @click="onSearch" :disabled="loading">{{ loading ? 'Searching...' : 'Search' }}</button>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
const props = defineProps({
  modelValue: String,
  loading: Boolean
});
const emit = defineEmits(['update:modelValue', 'search']);
const inputValue = ref(props.modelValue || '');

watch(() => props.modelValue, (val) => {
  inputValue.value = val;
});

function onSearch() {
  emit('search', inputValue.value);
}

watch(inputValue, (val) => {
  emit('update:modelValue', val);
});
</script>
