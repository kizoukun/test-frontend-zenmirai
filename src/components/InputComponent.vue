<script setup lang="ts">
defineProps<{
  placeholder: string
  type?: string
  required?: boolean
  id?: string
}>()

const model = defineModel()
</script>

<template>
  <input
    :type="type || 'text'"
    :id="id"
    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
    :placeholder="placeholder"
    v-model="model"
    :required="required || false"
  />
</template>
