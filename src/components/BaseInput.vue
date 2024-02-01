<script setup>
import {computed} from "vue";

const {modelValue} = defineProps({
  modelValue: String
});
const emit = defineEmits(['update:modelValue']);
const formattedVal = computed(() => modelValue.toLowerCase())

const update = (val) => {
  console.log(val)
  emit('update:modelValue', val)
}
</script>

<template>
  <input
      :value="modelValue"
      @input="update($event.target.value)"
  />
</template>