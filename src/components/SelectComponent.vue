<template>
  <div
    tabindex="1"
    class="border border-solid border-gray-700 rounded-md w-full"
    @focus="activate"
    @blur="searchable ? false : deactivate()"
  >
    <input
      class="bg-transparent"
      :class="inputStyle"
      ref="search"
      v-if="searchable"
      @focus.prevent="activate"
      @blur.prevent="deactivate"
      @keyup.esc="deactivate"
    />
    <div v-show="isOpen">
      <ul>
        <li v-for="(option, index) in options" :key="index" @click.stop="select(option)">
          <span class="border-collapse">
            <slot name="option" :option="option">
              <span>{{ option }}</span>
            </slot>
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { computed, nextTick, ref } from 'vue'

const selected = defineModel()

const { searchable, multiple } = defineProps({
  options: {
    type: Array,
    required: true
  },
  searchable: {
    type: Boolean,
    default: false
  },
  multiple: {
    type: Boolean,
    default: false
  },
  valueKey: {
    type: String
  },
  labelKey: {
    type: String
  }
})

const isOpen = ref(false)
const search = ref(null)
const openDirection = ref()
const preferredOpenDirection = ref()
const maxHeight = 300
const optimizedHeight = ref(maxHeight)

const internalValue = computed(() => {
  const arrayValue = Array.isArray(selected.value) ? selected.value : [selected.value]
  return selected.value || selected.value === 0 ? arrayValue : []
})

const inputStyle = computed(() => {
  return isOpen.value ? 'w-full' : 'w-0'
})

function activate(e) {
  if (isOpen.value) return
  adjustPosition(e)

  if (searchable) nextTick(() => search.value.focus())

  isOpen.value = true
}

function deactivate() {
  if (!isOpen.value) return

  if (searchable) search.value.blur()

  isOpen.value = false
}

function adjustPosition(e) {
  if (typeof window === 'undefined') return

  const spaceAbove = e.target.getBoundingClientRect().top
  const spaceBelow = window.innerHeight - e.target.getBoundingClientRect().bottom
  const hasEnoughSpaceBelow = spaceBelow > maxHeight

  if (hasEnoughSpaceBelow || spaceBelow > spaceAbove || openDirection.value === 'below') {
    preferredOpenDirection.value = 'below'
    optimizedHeight.value = Math.min(spaceBelow - 40, maxHeight)
  } else {
    preferredOpenDirection.value = 'above'
    optimizedHeight.value = Math.min(spaceAbove - 40, maxHeight)
  }
}

function isSelected(option) {
  return internalValue.value.indexOf(option) > -1
}

function select(option) {
  if (isSelected(option)) return

  selected.value = multiple ? internalValue.value.concat([option]) : option
}
</script>
