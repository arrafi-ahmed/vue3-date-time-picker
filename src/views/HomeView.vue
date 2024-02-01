<script setup>
import DateTimePicker from "@/components/DateTimePicker.vue";
import {reactive} from "vue";

const data = reactive({startDate: null, startTime: null, endDate: null, endTime: null})
const handleEmitDate = (dates) => {
  Object.assign(data, {...dates})
}
</script>

<template>
  <main>
    <div class="wrapper">
      <date-time-picker :step="5" color="#b4ff62" timeFormat="24" type="datetime" @emit-dates="handleEmitDate"/>
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