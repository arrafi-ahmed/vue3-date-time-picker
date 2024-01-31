<script setup>
import {computed, onMounted, reactive, ref, watch} from "vue";

const {data, step, type, color, timeFormat} = defineProps({
  data: {
    type: Object,
    default: () => ({}),
  },
  step: {
    type: Number,
    default: () => ({}),
  },
  type: {
    type: String,
    default: () => ({}),
  },
  color: {
    type: String,
    default: () => ({})
  },
  timeFormat: {
    type: String,
    default: () => ({})
  }
});
const inputValue = reactive({
  startDate: null,
  startTime: null,
  endDate: null,
  endTime: null,
});
const outputValue = reactive({
  startDate: null,
  startTime: null,
  endDate: null,
  endTime: null,
})

const format = ref('24');

const showStartPicker = ref(false);
const showEndPicker = ref(false);

const startDateRef = ref(null);
const startTimeRef = ref(null);
const endDateRef = ref(null);
const endTimeRef = ref(null);

const error = ref('');

const validate = (value) => {
  if (!value) {
    return "Please enter a value";
  }
  const parseDateTime = (date, time) => {
    return new Date(date + ' ' + time);
  };
  const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
  const timeRegex = /^\d?\d:\d{2}( (AM|PM))?$/;

  const startDateTime = (value.startDate && value.startTime) ? parseDateTime(value.startDate, value.startTime) : null;
  const endDateTime = (value.endDate && value.endTime) ? parseDateTime(value.endDate, value.endTime) : null;

  const isValidDateTime = (dateTime) => !isNaN(dateTime.getTime());
  const isValidDate = (date) => dateRegex.test(date);
  const isValidTime = (time) => timeRegex.test(time);

  if (type === "datetime") {
    if (startDateTime && !isValidDateTime(startDateTime)) {
      return "Please enter a valid start date-time";
    } else if (endDateTime && !isValidDateTime(endDateTime)) {
      return "Please enter a valid end date-time";
    } else if ((startDateTime && endDateTime) && (startDateTime > endDateTime)) {
      return "Start date-time can't be after End date-time";
    }
  } else if (type === "date") {
    if (value.startDate && !isValidDate(value.startDate)) {
      return "Please enter a valid start date";
    } else if (value.endDate && !isValidDate(value.endDate)) {
      return "Please enter a valid end date";
    } else if ((value.startDate && value.endDate) && (value.startDate > value.endDate)) {
      return "Start date can't be after End date";
    }
  } else if (type === "time") {
    if (value.startTime && !isValidTime(value.startTime)) {
      return "Please enter a valid start time";
    } else if (value.endTime && !isValidTime(value.endTime)) {
      return "Please enter a valid end time";
    } else if ((value.startTime && value.endTime) && (value.startTime > value.endTime)) {
      return "Start time can't be after End time";
    }
  }
  return null;
};
const submitValue = (datetimeType) => {
  if (datetimeType === "start") {
    if (type === 'datetime') {
      const splittedDate = inputValue.startDate.split('T')
      if ((!splittedDate[0] || !splittedDate[1])) {
        error.value = 'Please select start date-time!'
        return;
      }
      outputValue.startDate =
          splittedDate[0];
      outputValue.startTime =
          (format.value === "12"
              ? convertTo12Hour(roundMinutes(splittedDate[1]))
              : roundMinutes(splittedDate[1]));
    } else if (type === 'date') {
      if (!inputValue.startDate) {
        error.value = 'Please select start date!'
        return;
      }
      outputValue.startDate = inputValue.startDate;
    } else if (type === 'time') {
      if (!inputValue.startTime) {
        error.value = 'Please select start time!'
        return;
      }
      outputValue.startTime =
          format.value === "12"
              ? convertTo12Hour(roundMinutes(inputValue.startTime))
              : roundMinutes(inputValue.startTime);
    }
    error.value = validate(outputValue);
    showStartPicker.value = false;
  } else if (datetimeType === "end") {
    if (type === 'datetime') {
      const splittedDate = inputValue.endDate.split('T')
      if ((!splittedDate[0] || !splittedDate[1])) {
        error.value = 'Please select end date-time!'
        return;
      }
      outputValue.endDate =
          splittedDate[0];
      outputValue.endTime =
          (format.value === "12"
              ? convertTo12Hour(roundMinutes(splittedDate[1]))
              : roundMinutes(splittedDate[1]));
    } else if (type === 'date') {
      if (!inputValue.endDate) {
        error.value = 'Please select end date!'
        return;
      }
      outputValue.endDate = inputValue.endDate;
    } else if (type === 'time') {
      if (!inputValue.endTime) {
        error.value = 'Please select end time!'
        return;
      }
      outputValue.endTime =
          format.value === "12"
              ? convertTo12Hour(roundMinutes(inputValue.endTime))
              : roundMinutes(inputValue.endTime);
    }
    error.value = validate(outputValue);
    showEndPicker.value = false
  }
  emit("emitDates", outputValue)
};
const roundMinutes = (time) => {
  const [hour, minute] = time.split(":").map(Number);
  const nearestValidMinute = Math.round(minute / step) * step;
  if (nearestValidMinute === 60) {
    if (hour === 23) {
      return `00:00`;
    } else {
      return `${(hour + 1).toString().padStart(2, "0")}:00`;
    }
  } else {
    return `${hour.toString().padStart(2, "0")}:${nearestValidMinute.toString().padStart(2, "0")}`;
  }
};

const clearValue = (datetimeType) => {
  if (datetimeType === 'start') {
    inputValue.startDate = inputValue.startTime = null;
    outputValue.startDate = outputValue.startTime = null;
  } else if (datetimeType === 'end') {
    inputValue.endDate = inputValue.endTime = null;
    outputValue.endDate = outputValue.endTime = null;
  }
  emit("emitDates", outputValue)
  error.value = null;
};

const convertTo12Hour = (time) => {
  const hour = Number(time.split(":")[0]);
  const minute = Number(time.split(":")[1]);
  const period = hour < 12 ? "AM" : "PM";
  const hour12 = hour % 12 || 12;
  return hour12.toString().padStart(2, '0') + ":" + minute.toString().padStart(2, '0') + " " + period;
};

const emit = defineEmits(['emitDates'])

const startLabel = computed(() =>
    type === "datetime"
        ? "Start Date-Time"
        : type === "date"
            ? "Start Date"
            : type === "time"
                ? "Start Time"
                : null
);
const endLabel = computed(() =>
    type === "datetime"
        ? "End Date-Time"
        : type === "date"
            ? "End Date"
            : type === "time"
                ? "End Time"
                : null
);
const placeholder = computed(() =>
    type === "datetime"
        ? "yyyy-mm-dd hh:mm"
        : type === "date"
            ? "yyyy-mm-dd"
            : type === "time"
                ? "hh:mm"
                : null)

const formattedStartValue = computed(() => {
  const startDate = outputValue.startDate;
  const startTime = outputValue.startTime;

  let result = null;
  if (startDate) {
    result = startDate + ' '
  }
  if (startTime) {
    const [hours, minutes] = startTime.split(':');
    const paddedHours = hours ? hours.padStart(2, '0') : '';
    const paddedMinutes = minutes ? minutes.padStart(2, '0') : '';

    result = `${result ? result : ''}${paddedHours}:${paddedMinutes}`;
  }
  return result;
});

const formattedEndValue = computed(() => {
  const endDate = outputValue.endDate || '';
  const endTime = outputValue.endTime || null;
  let result = null;
  if (endDate) {
    result = endDate + ' '
  }
  if (endTime) {
    const [hours, minutes] = endTime.split(':');
    const paddedHours = hours ? hours.padStart(2, '0') : '';
    const paddedMinutes = minutes ? minutes.padStart(2, '0') : '';

    result = `${result ? result : ''}${paddedHours}:${paddedMinutes}`;
  }
  return result;
});
watch(
    () => data,
    async (newVal) => {
      Object.assign(inputValue, {...newVal})
    }, {deep: true}
);
onMounted(() => {
  Object.assign(outputValue, {...data})
  format.value = timeFormat
})
</script>
<template>
  <div class="date-time-container w-100">
    <div v-if="type === 'datetime' || type === 'time'" class="format">
      Time Format:
      <label><input v-model="format" :style="{ accentColor: color }" type="radio" value="24"/>24-hour </label>
      <label><input v-model="format" :style="{ accentColor: color }" type="radio" value="12"/>12-hour</label>
    </div>
    <!--  start date-time-->
    <div class="d-flex">
      <div class="date-time-component">
        <label for="start"><small>{{ startLabel }}</small></label> <br/>
        <div class="text-box">
          <input id="start"
                 v-model="formattedStartValue"
                 :placeholder="placeholder"
                 :style="{ borderColor: color }"
                 class="inputbox"
                 type="text"
                 @click="showStartPicker = true"/>
          <div class="buttons">
            <button v-if="formattedStartValue" :style="{ backgroundColor: color }" class="ml-4"
                    @click="clearValue('start')
            ">x
            </button>
          </div>
        </div>
        <div v-if="showStartPicker" class="picker">
          <div v-if="type === 'datetime'" class="calendar mb-8">
            <input
                ref="startDateRef"
                v-model="inputValue.startDate"
                :step="step * 60 || 1"
                :style="{ borderColor: color }"
                class="inputbox"
                type="datetime-local"
            />
          </div>
          <div v-else-if="type === 'date'" class="calendar mb-8">
            <input
                ref="startDateRef"
                v-model="inputValue.startDate"
                class="inputbox"
                type="date"
            />
          </div>
          <div v-else-if="type === 'time'" class="clock mb-8">
            <input
                ref="startTimeRef"
                v-model="inputValue.startTime"
                :step="step * 60 || 1"
                class="inputbox"
                type="time"
            />
          </div>
          <div class="buttons">
            <button :style="{ backgroundColor: color }" @click.stop="showStartPicker = false">Cancel</button>
            <button :style="{ backgroundColor: color }" class="submit-btn" @click.stop="submitValue('start')">OK
            </button>
          </div>
        </div>
      </div>

      <!--  end date-time-->
      <div class="date-time-component ml-10">
        <label for="end"><small>{{ endLabel }}</small></label> <br/>
        <div class="text-box">
          <input
              id="end"
              v-model="formattedEndValue"
              :placeholder="placeholder"
              :style="{ borderColor: color }"
              class="inputbox"
              type="text"
              @click="showEndPicker = true"
          />
          <div class="buttons">
            <button v-if="formattedEndValue" :style="{ backgroundColor: color }" class="ml-4"
                    @click="clearValue('end')">x
            </button>
          </div>
        </div>
        <div v-if="showEndPicker" class="picker">
          <div v-if="type === 'datetime'" class="calendar mb-8">
            <input
                ref="endDateRef"
                v-model="inputValue.endDate"
                :style="{ borderColor: color }"
                class="inputbox"
                type="datetime-local"
            />
          </div>
          <div v-if="type === 'date'" class="calendar mb-8">
            <input
                ref="endDateRef"
                v-model="inputValue.endDate"
                :style="{ borderColor: color }"
                class="inputbox"
                type="date"
            />
          </div>
          <div v-else-if="type === 'time'" class="clock mb-8">
            <input
                ref="endTimeRef"
                v-model="inputValue.endTime"
                :step="step * 60 || 1"
                class="inputbox"
                type="time"
            />
          </div>
          <div class="buttons">
            <button :style="{ backgroundColor: color }" @click="showEndPicker = false">Cancel</button>
            <button :style="{ backgroundColor: color }" class="submit-btn" @click="submitValue('end')">OK</button>
          </div>
        </div>

      </div>
    </div>
    <div v-if="error" class="error"><small>{{ error }}</small></div>
  </div>
</template>

<style scoped>
input[readonly]::placeholder {
  color: rgb(128, 128, 128);
}

input[type='radio'] {
  accent-color: green;
}

.d-flex {
  display: flex;
}

.w-100 {
  width: 100%;
}

.text-box .buttons button {
  margin-left: 4px;
}

input[class=inputbox] {
  border-radius: 5px;
  height: 30px;
  padding-inline-start: 10px;
  padding-inline-end: 10px;
}

.date-time-component {
  flex-grow: 1;
  border: 1px solid #ddd;
  border-radius: 5px;
  padding: 10px;
}

.text-box {
  margin-bottom: 5px;
  display: flex;
}

.ml-10 {
  margin-left: 10px;
}

.ml-4 {
  margin-left: 4px;
}

.mb-8 {
  margin-bottom: 8px;
}

.picker {
  display: flex;
  flex-direction: column;
}

.calendar {
  margin-bottom: 8px;
}

.clock {
}

.format {
  margin-bottom: 5px;
}

.buttons {
  display: flex;
}

.buttons button {
  border: none;
  border-radius: 4px;
  padding: 4px 8px;
  cursor: pointer;
}

.submit-btn {
  margin-left: 8px;
}

.error {
  color: red;
  font-weight: bold;
}

.border-none {
  border: none;
}
</style>
