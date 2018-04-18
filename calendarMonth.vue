<template>
  <div class="mu-calendar-monthday-content">
    <div class="mu-calendar-monthday-row" :key="i"  v-for="(week, i) in weeksArray">
      <button
        v-for="(date, j) in week"
        :disabled="isDisableDate(date)"
        :key="'dayButton' + i + '' + j"
        :selected="equalsDate(date)"
        :class="dayButtonClass(date)"
        @click="handleClick(date)"
        v-if="date">
        <div class="mu-day-button-bg"></div>
        <span class="mu-day-button-text" v-text="date.getDate()"></span>
      </button>
      <span v-else class="mu-day-empty"></span>
    </div>
  </div>
</template>

<script>
import * as dateUtils from '../../../es/dateUtils';

export default {
  data() {
    return {
      weeksArray: dateUtils.getWeekArray(this.displayDate || new Date(), this.firstDayOfWeek),
    };
  },
  props: {
    displayDate: {
      type: Date,
    },
    firstDayOfWeek: {
      type: Number,
      default: 1,
    },
    maxDate: {
      type: Date,
    },
    minDate: {
      type: Date,
    },
    startDate: {
      type: Date,
    },
    endDate: {
      type: Date,
    },
    shouldDisableDate: {
      type: Function,
    },
  },
  computed: {},
  methods: {
    equalsDate(date) {
      return dateUtils.isEqualDate(date, this.startDate) || dateUtils.isEqualDate(date, this.endDate);
    },
    isStartDate(date) {
      return dateUtils.isEqualDate(date, this.startDate);
    },
    isEndDate(date) {
      return dateUtils.isEqualDate(date, this.endDate);
    },
    range(date) {
      return dateUtils.isBetweenDates(date, this.startDate, this.endDate);
    },
    isDisableDate(day) {
      if (day === null) return false;
      let disabled = false;
      if (this.maxDate && this.minDate) disabled = !dateUtils.isBetweenDates(day, this.minDate, this.maxDate);
      if (!disabled && this.shouldDisableDate) disabled = this.shouldDisableDate(day, this.startDate, this.endDate);
      return disabled;
    },
    handleClick(date) {
      if (date) this.$emit('selected', date);
    },
    isNow(date) {
      const now = new Date();
      return date && date.getYear() === now.getYear() && date.getMonth() === now.getMonth() && date.getDate() === now.getDate();
    },
    dayButtonClass(date) {
      return {
        selected: this.equalsDate(date),
        selectedRange: this.range(date),
        'mu-day-button': true,
        disabled: this.isDisableDate(date),
        now: this.isNow(date),
        startDate: this.isStartDate(date),
        endDate: this.isEndDate(date),
      };
    },
  },
  watch: {
    displayDate(val) {
      return dateUtils.getWeekArray(val || new Date(), this.firstDayOfWeek);
    },
  },
};
</script>

<style lang="scss">
.mu-calendar-monthday-content {
  .selectedRange {
    background-color: #6ecaff;
    &.startDate{
      background-color: transparent;
      background: linear-gradient(to right, white 50%, #6ecaff 50%);
    }
    &.endDate{
      background-color: transparent;
      background: linear-gradient(to right, #6ecaff 50%, white 50%);
    }
  }
}
</style>
