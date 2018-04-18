<template>
  <div class="calendar">
    <date-display
      :startDate="startDate"
      :endDate="endDate"
      :dateTimeFormat="dateTimeFormat">
    </date-display>
    <div class="mu-calendar-container">
      <div class="mu-calendar-monthday-container">
        <calendar-toolbar
          :slideType="slideType"
          :nextMonth="nextMonth"
          :prevMonth="prevMonth"
          :displayDates="displayDates"
          @monthChange="handleMonthChange"/>
          <div class="mu-calendar-week">
            <span class="mu-calendar-week-day" v-for="(weekText, index) in weekTexts" :key="index">{{weekText}}</span>
          </div>
          <div class="mu-calendar-monthday">
            <transition :name="'mu-calendar-slide-' + slideType" v-for="(displayDate, index) in displayDates" :key="index">
              <div class="mu-calendar-monthday-slide" :key="displayDate.getTime()" >
                <calendar-month
                  :shouldDisableDate="shouldDisableDate"
                  :displayDate="displayDate"
                  :maxDate="maxDate"
                  :minDate="minDate"
                  :startDate="startDate"
                  :endDate="endDate"
                  @selected="handleSelected"/>
              </div>
            </transition>
          </div>
      </div>
      <div class="mu-calendar-actions">
        <mu-flat-button label="取消" @click="handleCancel" primary/>
        <mu-flat-button label="确认" v-if="!autoOk" @click="handleOk" primary/>
      </div>
    </div>
  </div>
</template>

<script>
import * as dateUtils from '../../../es/dateUtils';
import dateDisplay from './dateDisplay';
import calendarToolbar from './calendarToolbar';
import calendarMonth from './calendarMonth';

export default {
  name: 'calendar',
  components: { dateDisplay, calendarToolbar, calendarMonth },
  data() {
    const displayDate = dateUtils.cloneDate(new Date());
    displayDate.setDate(1);
    return {
      dateTimeFormat: dateUtils.dateTimeFormat,
      weekTexts: dateUtils.dateTimeFormat.getWeekDayArray(this.firstDayOfWeek),
      displayDates: [displayDate],
      displaySlideType: 'next',
      slideType: 'next',
      startDate: null,
      endDate: null,
    };
  },
  props: {
    maxDate: {
      type: Date,
    },
    minDate: {
      type: Date,
    },
    shouldDisableDate: {
      type: Function,
    },
    autoOk: {
      type: Boolean,
    },
  },
  watch: {},
  computed: {
    prevMonth() {
      return this.displayDates && dateUtils.monthDiff(this.displayDates[0], this.minDate) > 0;
    },
    nextMonth() {
      return this.displayDates && dateUtils.monthDiff(this.displayDates[0], this.maxDate) < 0;
    },
  },
  mounted() {},
  methods: {
    accept() {
      this.$emit('accept', [this.startDate, this.endDate]);
    },
    handleMonthChange(val) {
      const displayDate = dateUtils.addMonths(this.displayDates[0], val);
      this.changeDislayDate(displayDate);
      this.$emit('monthChange', displayDate);
    },
    changeDislayDate(date) {
      const oldDate = this.displayDates[0];
      if (date.getFullYear() === oldDate.getFullYear() && date.getMonth() === oldDate.getMonth()) return;
      this.slideType = date.getTime() > oldDate.getTime() ? 'next' : 'prev';
      const displayDate = dateUtils.cloneDate(date);
      displayDate.setDate(1);
      this.displayDates.push(displayDate);
      this.displayDates.splice(0, 1);
    },
    handleSelected(date) {
      this.setSelected(date);
      if (this.autoOk && this.startDate && this.endDate) this.handleOk();
    },
    replaceSelected(date) {
      const oldDate = this.displayDates[0];
      this.displaySlideType = date.getTime() > oldDate.getTime() ? 'next' : 'prev';
      this.displayDates.push(date);
      this.displayDates.splice(0, 1);
    },
    setSelected(date) {
      if (this.startDate && this.endDate) {
        this.startDate = date;
        this.endDate = null;
      } else if (this.startDate && this.startDate !== date) {
        this.endDate = date;
      } else {
        this.startDate = date;
      }
      this.changeDislayDate(date);
    },
    handleCancel() {
      this.$emit('dismiss');
    },
    handleOk() {
      const { startDate, endDate, maxDate, minDate } = this;
      if (endDate.getTime() > maxDate.getTime()) this.endDate = new Date(maxDate.getTime());
      if (startDate.getTime() < minDate.getTime()) this.startDate = new Date(minDate.getTime());
      this.$emit('accept', [startDate, endDate]);
    },
  },
};
</script>

<style lang="scss">
.calendar {
}
</style>

