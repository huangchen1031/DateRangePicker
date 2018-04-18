<template>
<div class="mu-calendar-toolbar">
  <mu-icon-button :disabled="!prevMonth" @click.stop="prev">
    <svg viewBox="0 0 24 24" class="mu-calendar-svg-icon">
      <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
    </svg>
  </mu-icon-button>
  <div class="mu-calendar-toolbar-title-wrapper">
    <transition :name="'mu-calendar-slide-' + slideType"  v-for="(displayDate, index) in displayDates" :key="index">
      <div class="mu-calendar-toolbar-title" :key="displayDate.getTime()">
        {{dateTimeFormat.formatMonth(displayDate)}}
      </div>
    </transition>
  </div>
  <mu-icon-button :disabled="!nextMonth" @click.stop="next">
    <svg viewBox="0 0 24 24" class="mu-calendar-svg-icon">
      <path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z"/>
    </svg>
  </mu-icon-button>
</div>
</template>

<script>
import * as dateUtils from '../../../es/dateUtils';

export default {
  name: 'calendarToolbar',
  props: {
    dateTimeFormat: {
      type: Object,
      default() {
        return dateUtils.dateTimeFormat;
      },
    },
    displayDates: {
      type: Array,
    },
    nextMonth: {
      type: Boolean,
      default: true,
    },
    prevMonth: {
      type: Boolean,
      default: true,
    },
    slideType: {
      type: String,
    },
  },
  methods: {
    prev() {
      this.$emit('monthChange', -1);
    },
    next() {
      this.$emit('monthChange', 1);
    },
  },
};
</script>