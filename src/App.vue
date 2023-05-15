<script setup>
import { reactive, ref } from "vue";

const isInputValid = ref(true);
const isFormSubmitted = ref(false);
const endYears = ref(null);
const endMonths = ref(null);
const endDays = ref(null);
const currentYear = new Date().getFullYear();

const dobData = reactive({
  days: {
    val: null,
    isValid: true,
  },
  months: {
    val: null,
    isValid: true,
  },
  years: {
    val: null,
    isValid: true,
  },
});

const age = reactive({
  years: "--",
  months: "--",
  days: "--",
});

function calculateAge(day, month, year) {
  if (day > 31 || month > 31 || year > currentYear) return;
  const today = new Date();
  const birthDate = new Date(year, month - 1, day);

  const isValidDate = birthDate.getDate() === day;
  if (!isValidDate) {
    isInputValid.value = false;
    return;
  }

  let ageYears = today.getFullYear() - birthDate.getFullYear();
  let ageMonths = today.getMonth() - birthDate.getMonth();
  let ageDays = today.getDate() - birthDate.getDate();

  if (ageMonths < 0 || (ageMonths === 0 && ageDays < 0)) {
    ageYears--;
    ageMonths += 12;
  }

  if (ageDays < 0) {
    const lastMonthDate = new Date(
      today.getFullYear(),
      today.getMonth(),
      0
    ).getDate();
    ageDays += lastMonthDate;
    ageMonths--;
  }

  endYears.value = ageYears;
  endMonths.value = ageMonths;
  endDays.value = ageDays;

  animateAgeDisplay();
}

function animateAgeDisplay() {
  age.years = 0;
  age.months = 0;
  age.days = 0;
  const incrementYear = Math.ceil((endYears.value - age.years) / 50);
  const incrementMonths = Math.ceil((endMonths.value - age.months) / 50);
  const incrementDays = Math.ceil((endDays.value - age.days) / 50);

  const intervalYears = setInterval(() => {
    age.years += incrementYear;
    if (age.years >= endYears.value) clearInterval(intervalYears);
  }, 20);
  const intervalMonths = setInterval(() => {
    age.months += incrementMonths;
    if (age.months >= endMonths.value) clearInterval(intervalMonths);
  }, 20);
  const intervalDays = setInterval(() => {
    age.days += incrementDays;
    if (age.days >= endDays.value) clearInterval(intervalDays);
  }, 20);
}

function validateForm() {
  isInputValid.value = true;
  if (dobData.days.val > 31) {
    dobData.days.isValid = false;
    isInputValid.value = false;
  }
  if (dobData.months.val > 12) {
    dobData.months.isValid = false;
    isInputValid.value = false;
  }
  if (dobData.years.val > currentYear) {
    dobData.years.isValid = false;
    isInputValid.value = false;
  }
}
function required() {
  if (isFormSubmitted.value === false) return;
  if (
    dobData.days.val === null ||
    dobData.months.val === null ||
    dobData.years.val === null
  )
    return true;
  else return false;
}
function submitForm() {
  isFormSubmitted.value = true;
  isInputValid.value = true;
  validateForm();
  calculateAge(dobData.days.val, dobData.months.val, dobData.years.val);
}
</script>

<template>
  <h1 class="sr-only">Age Calculator App</h1>
  <section>
    <form @submit.prevent="submitForm">
      <div class="date__picker">
        <div class="input__fields">
          <label :class="{ invalid: !isInputValid }" for="day">Day</label>
          <input
            :class="{ invalid__border: !isInputValid }"
            placeholder="DD"
            type="number"
            id="day"
            v-model="dobData.days.val"
          />
          <p v-if="!dobData.days.isValid" class="invalid">
            Must be a valid day
          </p>
          <p v-else-if="required()" class="invalid">This field is required</p>
          <p v-else-if="!isInputValid" class="invalid">Must be a valid date</p>
        </div>
        <div class="input__fields">
          <label :class="{ invalid: !isInputValid }" for="month">Month</label>
          <input
            :class="{ invalid__border: !isInputValid }"
            placeholder="MM"
            type="number"
            id="month"
            v-model="dobData.months.val"
          />
          <p v-if="required()" class="invalid">This field is required</p>
          <p v-if="!dobData.months.isValid" class="invalid">
            Must be a valid month
          </p>
        </div>
        <div class="input__fields">
          <label :class="{ invalid: !isInputValid }" for="year">Year</label>
          <input
            :class="{ invalid__border: !isInputValid }"
            placeholder="YYYY"
            type="number"
            id="year"
            v-model="dobData.years.val"
          />
          <p v-if="required()" class="invalid">This field is required</p>
          <p v-if="!dobData.years.isValid" class="invalid">
            Must be a valid year
          </p>
        </div>
      </div>
      <div class="enter">
        <div class="enter__button">
          <div class="line"></div>
          <button>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="46"
              height="44"
              viewBox="0 0 46 44"
            >
              <g fill="none" stroke="#FFF" stroke-width="3">
                <path
                  d="M1 22.019C8.333 21.686 23 25.616 23 44M23 44V0M45 22.019C37.667 21.686 23 25.616 23 44"
                />
              </g>
            </svg>
          </button>
        </div>
      </div>
    </form>
    <div>
      <h2>
        <span id="ageCountUp">{{ age.years }}</span
        >years
      </h2>
      <h2>
        <span id="ageCountUp">{{ age.months }}</span
        >months
      </h2>
      <h2>
        <span id="ageCountUp">{{ age.days }}</span
        >days
      </h2>
    </div>
  </section>
</template>

<style>
body {
  display: flex;
  justify-content: center;
  align-items: center;
}
section {
  width: 343px;
  height: 485px;
  padding: 2rem 1.5rem;

  color: var(--neutral-900);
  background-color: var(--neutral-100);
  border-radius: 5% 5% 30% 5%;
}

h2 {
  font-size: 3rem;
  font-style: italic;
  font-weight: 800;
  line-height: 1.1;
}

p {
  padding-top: 5px;
  font-style: italic;
  font-size: 12px;
}

.date__picker {
  display: flex;
  gap: 15px;
}
.input__fields {
  display: flex;
  flex-direction: column;
}

input {
  width: 85px;
  height: 52px;
  border: 1px solid var(--neutral-500);
  border-radius: 10px;
  caret-color: var(--primary-color-100);
  font-size: 1.2rem;
  padding-left: 15px;
  cursor: pointer;
}

input:is(:active, :focus) {
  outline: none;
  border-color: var(--primary-color-100);
}

label {
  font-size: 12px;
  letter-spacing: 2px;
  color: var(--neutral-700);
  text-transform: uppercase;
  padding-bottom: 5px;
}

span {
  color: var(--primary-color-100);
  padding-right: 5px;
}

button {
  height: 65px;

  aspect-ratio: 1;
  background-color: var(--primary-color-100);
  border: none;
  border-radius: 50%;
  cursor: pointer;
  position: relative;
}

svg {
  display: block;
  left: 25%;
  width: 50%;
  height: 50%;
  position: relative;
}

button:is(:hover) {
  background-color: var(--neutral-900);
}

.enter {
  padding: 2.2rem 0;
}

.enter__button {
  position: relative;
  display: flex;
  justify-content: center;
}

.line {
  position: absolute;
  top: 46%;
  height: 1px;
  width: 100%;
  background-color: var(--neutral-500);
}

@media (min-width: 1024px) {
  .enter__button {
    position: relative;
    display: flex;
    justify-content: end;
  }
  section {
    width: 840px;
    height: 651px;
    padding: 4.5rem 3.5rem;
  }
  h2 {
    font-size: 6.4rem;
  }
  input {
    width: 158px;
    height: 70px;
    font-size: 2rem;
  }
  button {
    height: 95px;
  }
  .enter {
    padding: 0;
  }
  label {
    font-size: 1rem;
  }
}

.invalid {
  color: var(--primary-color-200);
}

.invalid__border {
  border-color: var(--primary-color-200);
}

.sr-only:not(:focus):not(:active) {
  clip: rect(0 0 0 0);
  clip-path: inset(50%);
  height: 1px;
  overflow: hidden;
  position: absolute;
  white-space: nowrap;
  width: 1px;
}

#ageCountUp {
  animation: countUp 1s ease-in-out;
}

@keyframes countUp {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
