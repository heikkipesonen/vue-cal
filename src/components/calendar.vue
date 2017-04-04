<template>
  <div class="calendar">
    <div class="content-wrapper">
      <month v-model="date"></month>

      <div class="day-selector">
        <div class="day" v-for="(day, index) in days" :class="{active: day.active}">
          <div class="day-content" v-on:click="selectDay(day.start)">
            <span class="date">{{day.start.date()}}</span>
            <span class="month">{{day.start.month() + 1}}</span>
            <span class="year">{{day.start.year()}}</span>
          </div>
          <day v-model="days[index]"></day>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import moment from 'moment'
import day from './day'
import month from './month'

export default {
  components: {
    day,
    month
  },
  props: {
    range: {
      type: Number,
      default: 5
    },
    timetable: {
      type: Array,
      default: () => [
        {
          start: 10,
          end: 20
        },
        {
          start: 10,
          end: 22
        },
        {
          start: 8,
          end: 16
        },
        {
          start: 8,
          end: 16
        },
        {
          start: 10,
          end: 20
        },
        {
          start: 8,
          end: 16
        },
        {
          start: 8,
          end: 16
        }
      ]
    },

    reserveSize: {
      type: Number,
      default: 2 * 60 * 60 * 1000
    }
  },

  data () {
    return {
      date: moment()
    }
  },

  computed: {
    startTime () {
      return this.timetable.reduce((value, timetable) => {
        return timetable.start < value ? timetable.start : value
      }, 24)
    },

    endTime () {
      return this.timetable.reduce((value, timetable) => {
        return timetable.end > value ? timetable.end : value
      }, 0)
    },

    days () {
      let date = moment(this.date).startOf('day')
      let day = 24 * 60 * 60 * 1000
      let loopDay = date.valueOf() - day * (this.range + 1)
      return Array(this.range * 2 + 1).fill(false).map((value, index) => {
        let startTime = moment(loopDay += day).startOf('day')
        startTime.hours(this.startTime)
        let endTime = moment(startTime).hours(this.endTime)
        let timetable = this.timetable[startTime.day()]

        return {
          start: startTime,
          end: endTime,
          enabled: timetable,
          active: date.month() === startTime.month() && date.date() === startTime.date() && date.year() === startTime.year()
        }
      })
    }
  },

  methods: {
    selectDay (day) {
      this.date = moment(day)
      console.log(day)
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../styles/theme';

.calendar {
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
}

.content-wrapper {
  position: relative;
}

.day-selector {
  text-align: center;
}

.day {
  display: inline-block;
  text-align: center;
  min-height: 64px;
  max-width: 256px;
  cursor: pointer;

  .day-content {
    padding: 16px 0;
  }

  &:hover {
    background-color: fade_out($brand-secondary, 0.8)
  }

  &.active {
    background-color: $brand-primary;
  }
}

.date {
  font-size: 2em;
  display: block;
  font-weight: 800;
}

.month, .year {
  font-size: 1em;
  color: $brand-secondary;
}
</style>
