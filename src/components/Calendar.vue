<template>
  <div class="calendar">
    <div class="calendar__header">
      <i
        id="arrow-dec"
        class="calendar__arrow"
        @click="changeMonth($event)"
      >&larr;</i>
      <span class="calendar__header-date">
        {{ nameOfCurrentMonth }}
      </span>
      <i
        id="arrow-inc"
        class="gg-arrow-right calendar__arrow"
        @click="changeMonth($event)"
      >&rarr;</i>
    </div>
    <div class="calendar__days-week">
      <div
        v-for="day in namesDaysOfWeek"
        :key="day"
        class="calendar__day-week"
      >
        {{ day }}
      </div>
    </div>
    <div class="calendar__days-month">
      <div
        v-for="(day, index) in canlanderDaysList"
        :key="index"
        class="calendar__day-month"
        :class="[
          day.disabled ? 'calendar__day-month_hidden' : '',
          day.active ? 'calendar__day-month_active' : '',
        ]"
        @click="setDate(day.value)"
      >
        {{ day.value }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import moment from 'moment';
import localization from '@/localization/localization.json';

/**
 * Props
 */
type PropsType = {
  lang: 'en' | 'ch',
  primaryColor?: string
  textColor?: string,
  backgroundColor?: string
}
const { lang, primaryColor = '#2ecce0', textColor = '#ffffff', backgroundColor = '#23232b' } = defineProps<PropsType>();

/**
 * State
 */

// css
const primary = ref(primaryColor);
const text = ref(textColor);
const background = ref(backgroundColor);

const date = ref<moment.Moment>(moment());
const canlanderDaysList = ref<{
  value: string;
  disabled: boolean;
  fulldate: string;
  active: boolean
}[]>([]);
const selectedDate = ref<string>('')

/**
 * Emits
 */
const emit = defineEmits(['setDate']);

/**
 * Methods
 */
const defaultSetDate = () => {
  date.value = moment();
  selectedDate.value = date.value.format('DD-MM-YYYY');
  emit('setDate', date.value)
}

const createCanlanderDaysList = () => {
  const hiddenDays = [];
  const daysOfMonth = [];

  const indexFirstDayInWeek = date.value.date(1).isoWeekday() - 1;
  for (let i = 0; i < indexFirstDayInWeek; i++) {
    const obj = {
      value: '',
      disabled: true,
      fulldate: '',
      active: false
    };
    hiddenDays.push(obj);
  }

  for (let i = 1; i <= date.value.daysInMonth(); i++) {
    const fulldate = moment(date.value).set('date', i).format('DD-MM-YYYY');
    const obj = {
      value: `${i}`,
      disabled: false,
      fulldate,
      active: fulldate === selectedDate.value,
    };
    daysOfMonth.push(obj);
  }

  canlanderDaysList.value = [...hiddenDays, ...daysOfMonth];
}

const setDate = (value: string) => {
  const repeatSelectedDate = selectedDate.value === date.value.set('date', parseInt(value)).format('DD-MM-YYYY');
  if (repeatSelectedDate) {
    canlanderDaysList.value.find((item) => item.fulldate === selectedDate.value)!.active = false;
    selectedDate.value = '';
    emit('setDate', selectedDate.value);
    return;
  }

  const oldDate = canlanderDaysList.value.find((item) => item.fulldate === selectedDate.value);
  if (oldDate) {
    oldDate.active = false;
  }

  date.value.set('date', parseInt(value));
  emit('setDate', date.value);
  selectedDate.value = date.value.format('DD-MM-YYYY');
  canlanderDaysList.value.find((item) => item.fulldate === selectedDate.value)!.active = true;
}

const changeMonth = (e: MouseEvent) => {
  date.value = (e.target as HTMLInputElement).id === 'arrow-inc'
    ? moment(date.value).add(1, 'M')
    : moment(date.value).subtract(1, 'M');
  createCanlanderDaysList();
}

/**
 * Compute
 */
const namesDaysOfWeek = computed(() => {
  return localization[lang].namesDaysOfWeek;
})

const nameOfCurrentMonth = computed(() => {
  const numberOfCurrentMonth: number = parseInt(moment(date.value).format('M')) - 1;
  return `${localization[lang].namesMonths[numberOfCurrentMonth].toUpperCase()} ${moment(date.value).format('YYYY')}`;
})

/**
 * Life cycle
 */
onMounted(() => {
  defaultSetDate();
  createCanlanderDaysList()
})

</script>

<style lang="scss">
.calendar {
  background: v-bind(background);
  border-radius: 5px;
  box-shadow: 0px 2px 7px 0px rgb(50 50 50 / 75%);
  padding: 30px 40px;
  color: v-bind(text);
}

.calendar__header {
  display: flex;
  justify-content: center;
  padding-bottom: 20px;
  gap: 16px;
  white-space: nowrap
}

.calendar__header-date {
  color: v-bind(primary);
  width: 150px;
  font-weight: 300;
  text-align: center;
}

.calendar__days-week {
  display: flex;
  align-items: center;
  width: 420px;
  margin-bottom: 8px;
}

.calendar__day-week {
  flex: 1 1 60px;
  color: v-bind(primary);
  font-weight: 300;
  text-align: center;
}

.calendar__days-month {
  display: flex;
  flex-wrap: wrap;
  width: 420px;
}

.calendar__day-month {
  font-weight: 300;
  width: 60px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-sizing: border-box;
  border: 1px solid transparent;
  transition: .2s;
}

.calendar__day-month:hover {
  border: 1px solid v-bind(primary);
}

.calendar__arrow {
  cursor: pointer;
  color: v-bind(primary);
}

.calendar__day-month_hidden {
  visibility: hidden;
}

.calendar__day-month_active {
  background: v-bind(primary);
}
</style>