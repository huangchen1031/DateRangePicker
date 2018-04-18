<template>
  <div class="mu-date-display">
    <div class="mu-date-display-year">
      <div class="mu-date-display-slideIn-wrapper">
        <div class="mu-date-display-year-title">
          <span>{{startYear}}</span>
          <span v-if="startYear !== endYear">{{endYear}}</span>
        </div>
      </div>
    </div>
    <div class="mu-date-display-monthday">
      <transition :name="'mu-date-display-' +  slideType" v-for="(displayDate, index) in displayDates.startDate" :key="index">
        <div class="date startDate mu-date-display-slideIn-wrapper" :key="dateTimeFormat.formatDisplay(displayDate)">
          <div class="mu-date-display-monthday-title">
            {{dateTimeFormat.formatDisplay(startDate)}}
          </div>
        </div>
      </transition>
      <div class="hyphen">-</div>
      <transition :name="'mu-date-display-' +  slideType" v-for="(displayDate, index) in displayDates.endDate" :key="index">
        <div class="date endDate mu-date-display-slideIn-wrapper" :key="dateTimeFormat.formatDisplay(displayDate)">
          <div class="mu-date-display-monthday-title">
           {{dateTimeFormat.formatDisplay(endDate)}}
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import { addYears } from '../../../es/dateUtils';

export default {
  name: 'dateDisplay',
  props: {
    dateTimeFormat: {
      type: Object,
    },
    startDate: {
      type: Date,
    },
    endDate: {
      type: Date,
    },
  },
  data() {
    return {
      displayDates: {
        startDate: [this.startDate],
        endDate: [this.endDate],
      },
      slideType: 'next',
    };
  },
  computed: {
    startYear() {
      return this.startDate && this.startDate.getFullYear();
    },
    endYear() {
      return this.endDate && this.endDate.getFullYear();
    },
  },
  methods: {
    replaceSelected(date, type) {
      const oldDate = this.displayDates[type][0] || addYears(new Date(), type === 'startDate' ? -100 : 100);
      this.slideType = date.getTime() > oldDate.getTime() ? 'next' : 'prev';
      this.displayDates[type].push(date);
      this.displayDates[type].splice(0, 1);
    },
  },
  watch: {
    startDate(val) {
      if (val) {
        this.replaceSelected(val, 'startDate');
      }
    },
    endDate(val) {
      if (val) {
        this.replaceSelected(val, 'endDate');
      }
    },
  },
};
</script>

<style lang="scss">
.mu-date-display {
  .mu-date-display-year-title {
    position: relative;
    &>span:nth-child(2) {
      position: absolute;
      left: 60%;
    }
  }
  .mu-date-display-monthday {
    .date {
      width: 40%;
      text-align: center;
    }
    .startDate {
      left: 0;
    }
    .endDate {
      left: 60%;
    }
    .hyphen {
      position: absolute;
      left: 40%;
      width: 20%;
      text-align: center;
      display: inline-block;
    }
  }
}
</style>


