
<template>
  <div id="app">
    <div class="main">
      <span class="main__date-label">{{ labelSelectedDate }} </span>
      <Calendar
        :lang="selectedLang"
        primary-color="orange"
        @set-date="setDate" />
      <span class="main__label">{{ labelSelectedLang }}</span>
      <select
        v-model="selectedLang"
        class="main__lang-select">
        <option
          v-for="lang in appLangs"
          :key="lang.value"
          :value="lang.value">
          {{ lang.label }}
        </option>
      </select>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import localization from './localization/localization.json'
import Calendar from './components/Calendar.vue';

const selectedLang = ref<'en' | 'ch'>('en')
const selectedDate = ref<string>('');

const setDate = (value: any) => {
  selectedDate.value = value.format('YYYY/MM/DD')
}

const appLangs = computed(() => [
  {
    label: '繁體中文',
    value: 'ch',
  },
  {
    label: 'English',
    value: 'en',
  },
])

const labelSelectedDate = computed(() => {
  return selectedDate.value ? selectedDate.value : localization[selectedLang.value].dateLabel
});
const labelSelectedLang = computed(() => {
  return localization[selectedLang.value].langLabel
});

</script>

<style lang="scss">
.main {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background: #050505;
  color: #fff;
}

.main__lang-select {
  margin-top: 5px;
  width: 200px;
  height: 30px;
  border: none;
}

.main__date-label {
  text-transform: uppercase;
  font-size: 20px;
  margin-bottom: 20px;
}

.main__label {
  padding-top: 20px;
}

.main__input {
  width: 200px;
  height: 20px;
  margin-top: 5px;
  box-sizing: border-box;
  padding: 10px;
}

.main__input:focus {
  outline: none;
}

.main__input:valid:not(:placeholder-shown) {
  border: 2px solid green;
}

.main__input:invalid:not(:placeholder-shown) {
  border: 2px solid orangered;
}
</style>
