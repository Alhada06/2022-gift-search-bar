<script setup>
import { ref, watch } from 'vue'
import { debounce } from 'lodash'
const searchTerm = ref('')
const products = ref([]);
const isLoading = ref(false);

const findProducts = debounce(async term => {

  try {
    if (term.length === 0) { return products.value = []; }

    isLoading.value = true;

    const res = await fetch('https://dummyjson.com/products/search?q=' + term + '&limit=10')
      .then(response => response.json())
    products.value = res.products
  } catch (error) {
    console.error(error);
    alert('Ooops something whent wrong!')
  } finally {
    isLoading.value = false;
  }


}, 300);

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div v-if="isLoading"> Loading...</div>
    <ul v-else-if="!isLoading && products.length > 0" class=" list-disc">
      <li v-for="prod in products" :key="prod.id">{{ prod.title }} - {{ prod.price }} $</li>
    </ul>
    <div v-else-if="products.length === 0 && searchTerm.length > 0">Nothing found</div>
  </div>
</template>
