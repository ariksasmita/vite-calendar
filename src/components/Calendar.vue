<script setup>
import { reactive, computed } from 'vue'

// get current date
const currentDate = new Date('2022-08-01')
// set date to 1st
currentDate.setDate(1)

const dateStartEnd = computed(() => {
  // Start Date
  // If date 1 of the month not Sunday, get the offset date
  const curMonthStartOffset = 1 - currentDate.getDay()
  const firstDate = new Date(currentDate).setDate(curMonthStartOffset)

  // End Date
  // -- base on current date as start
  const curMonthEnd = new Date(currentDate)
  // -- shift to next month
  curMonthEnd.setMonth(curMonthEnd.getMonth() + 1)
  // -- set date to 0 (last day of prev month)
  curMonthEnd.setDate(0)
  // -- get day of week value to define offset to add later, so all over dates complete on Saturday
  // -- ex. last day of prev mont = Wed = 3, need to add (6 - 3) = 3 more days to complete it on Saturday
  const offset = 6 - curMonthEnd.getDay()
  // -- add
  curMonthEnd.setDate(curMonthEnd.getDate() + offset)

  return {
    start: new Date(firstDate),
    end: curMonthEnd
  }
})

const listOfDates = computed(() => {
  const dates = []
  let weeks = []

  const year = dateStartEnd.value.start.getFullYear()
  const month = dateStartEnd.value.start.getMonth()
  let day = dateStartEnd.value.start.getDate()

  while (dates.length < 6) {
    if (weeks.length === 7) {
      dates.push(weeks)
      weeks = []
    }
    const date = new Date(year, month, day)
    const classses = []
    if (date.getMonth() !== currentDate.getMonth()) classses.push('offset')
    weeks.push({
      date,
      classses,
    })
    day += 1
  }
  return dates
})

</script>

<template>
  <div>
    <p>New Component {{ dateStartEnd }}</p>
    <div v-for="weeks in listOfDates">
      <span v-for="day in weeks" :class="day.classses.join(' ')">{{ day.date.getDate() }} ; </span>
    </div>
  </div>
</template>

<style scoped>
.offset {
  color: gray;
}
</style>