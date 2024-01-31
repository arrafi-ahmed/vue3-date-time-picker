<script setup>
import DateTimePicker from "@/components/DateTimePicker.vue";
import {reactive} from "vue";

const data = reactive({startDate: '2024-01-16', startTime: '22:40', endDate: '2024-01-27', endTime: '22:40'})
const handleEmitDate = (dates) => {
  Object.assign(data, {...dates})
}
</script>

<template>
  <main>
    {{ data }}
    <div class="wrapper">
      <date-time-picker :step="5" color="#d93c60" timeFormat="24" type="datetime" @emit-dates="handleEmitDate"/>
    </div>
  </main>
</template>

<style>
.wrapper {
  width: 448px;
  display: flex;
  justify-content: center;
}
</style>