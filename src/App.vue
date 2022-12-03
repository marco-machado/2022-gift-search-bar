<script setup>
import axios from 'axios'
import { ref, watch } from 'vue'
import { debounce } from 'lodash'

const searchTerm = ref('')
let foundProducts = ref([])
let isLoading = ref(false)

const findProducts = async term => {
  if (!term) {
    foundProducts.value = []
    return
  }

  isLoading.value = true

  try {
    const {
      data: { products },
    } = await axios.get('https://dummyjson.com/products/search', { params: { q: searchTerm.value } })

    foundProducts.value = products
  } catch (e) {
    alert('Error fetching products.')
  } finally {
    isLoading.value = false
  }
}

watch(
  searchTerm,
  debounce(newTerm => findProducts(newTerm), 300)
)
</script>

<template>
  <div class="flex h-full w-full flex-col items-center justify-center gap-5">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="border-2 border-gray-dark p-2" v-model="searchTerm" placeholder="Start typing..." />
    <span v-if="isLoading">Searching...</span>
    <ul v-if="foundProducts.length && !isLoading" class="list-disc">
      <li v-for="product in foundProducts" :key="product.id">{{ product.title }} [${{ product.price }}]</li>
    </ul>
  </div>
</template>
