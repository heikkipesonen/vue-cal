<template>
<div class="day">
  <div class="day-slot" v-for="slot in range" v-on:click="selectSlot(slot)" v-bind:class="{enabled: slot.enabled, disabled: !slot.enabled}">
    <span class="start">{{slot.start.format('HH:mm')}}</span>
    -
    <span class="end">{{slot.end.format('HH:mm')}}</span>
  </div>
</div>
</template>
<script>
import moment from 'moment'

export default {
  props: {
    value: {
      type: Object,
      required: true,
      default: () => {
        return {
          start: moment().startOf('day'),
          end: moment().endOf('day'),
          enabled: {
            start: 0,
            end: 24
          }
        }
      }
    },

    step: {
      type: Number,
      default: 60 * 1000 * 60
    }
  },

  computed: {
    range () {
      let values = []
      let start = moment(this.value.start)
      let end = moment(this.value.end)

      while (start.valueOf() < end.valueOf()) {
        let startTime = moment(start)
        let endTime = moment(start.add(this.step))

        let es = moment(startTime).hours(this.value.enabled.start)
        let ee = moment(startTime).hours(this.value.enabled.end)

        let enabled = startTime.valueOf() >= es.valueOf() && endTime.valueOf() <= ee.valueOf()

        values.push({
          start: startTime,
          end: endTime,
          enabled
        })
      }
      return values
    }
  },

  methods: {
    selectSlot (slot) {
      console.log(slot)
    }
  }
}
</script>
<style lang="scss" scoped>
.day {
  display: flex;
  flex-direction: column;
}

.day-slot {
  padding: 16px;

  &.enabled {
    background-color: #efefef;
    &:hover {
      background-color: #ddd;
    }
  }

  &.disabled {
    opacity: 0.3;
  }
}

.start {

}

.end {
  font-size: 0.7em;
  color: #aaa;
}
</style>
