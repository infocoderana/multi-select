<script setup>
import { ref } from 'vue'
import SelectComponent from './components/SelectComponent.vue'

const options = ref([
  {
    name: 'Option 1',
    value: 1
  },
  {
    name: 'Option 2',
    value: 2
  },
  {
    name: 'Option 3',
    value: 3
  }
])

const selected = ref()
</script>

<template>
  parent{{ selected }}
  <div class="h-full w-1/2 flex items-center">
    <SelectComponent
      v-model="selected"
      :options="options"
      searchable
      value-key="name"
      label-key="value"
    >
      <template #option="{ option }"> {{ option.name }} - {{ option.value }} </template>
    </SelectComponent>
  </div>
</template>
