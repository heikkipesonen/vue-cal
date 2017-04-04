<template>
<table class="month">
  <tr class="week" v-for="week in calendar">
    <td class="day" v-for="day in week" v-bind:class="{active: date === day.valueOf()}" v-on:click="setDate(day)">
      {{day ? day.date() : ' '}}
    </td>
  </tr>
</table>
</template>
<script>
import moment from 'moment'

export default {
  props: {
    value: {
      type: Object,
      default: () => moment()
    }
  },

  computed: {
    date () {
      return moment(this.value).startOf('day').valueOf()
    },

    calendar () {
      let model = moment(this.value).startOf('month')
      let month = []
      let week = Array(7).fill(false)

      while (model.month() === this.value.month()) {
        let day = model.day()
        week[day] = moment(model)

        if (day === 6) {
          month.push(week)
          week = Array(7).fill(false)
        }

        model.add(1, 'day')
      }

      if (month.indexOf(week) === -1) {
        month.push(week)
      }

      return month
    }
  },

  methods: {
    setDate (day) {
      if (day) {
        this.$emit('input', day)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../styles/theme';

.month {
}

.day {
  text-align: center;
  padding: 1em;
  cursor: pointer;

  &:hover {
    background-color: fade_out($brand-primary, 0.6);
  }

  &.active {
    background-color: $brand-primary;
    color: white;
  }
}
</style>
