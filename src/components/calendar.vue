<template>
  <div class="calendar">
    <div class="day-selector">
      <div class="day" v-for="(day, index) in days">
        <div class="day-content">
          <span class="date">{{day.start.date()}}</span>
          <span class="month">{{day.start.month() + 1}}</span>
          <span class="year">{{day.start.year()}}</span>
        </div>
        <day v-model="days[index]"></day>
      </div>
    </div>

  </div>
</template>
<script>
import moment from 'moment'
import day from './day'

export default {
  components: {
    day
  },

  props: {
    range: {
      type: Number,
      default: 2
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
      date: new Date()
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
      let date = moment(this.date.valueOf()).startOf('day').toDate()
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
          enabled: timetable
        }
      })
    }
  },

  methods: {
    selectDay (day) {
      console.log(day)
    }
  }
}
</script>
<style lang="scss" scoped>
.calendar {
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  display: flex;
  flex-direction: column;
}

.day-selector {
  display: flex;
  flex: 1;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.day {
  flex: 1;
  text-align: center;
  min-height: 64px;
  padding: 16px 0;
  max-width: 256px;
}

.date {
  font-size: 2em;
  display: block;
}

.month, .year {
  font-size: 1em;
  color: #aaa;
}
</style>
