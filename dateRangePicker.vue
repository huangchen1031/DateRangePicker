<template>
  <div class="date-range-picker">
    <mu-text-field :hintText="hintText" @focus="handleFocus" @labelClick="handleClick" :value="dateRange"/>
    <mu-dialog
      dialogClass="mu-date-picker-dialog"
      v-if="!disabled"
      :open="dialogVisible"
      @close="dialogVisible = false">
      <calendar
        :initialDate="dialogDate"
        :shouldDisableDate="shouldDisableDate"
        :maxDate="maxDate"
        :minDate="minDate"
        @accept="handleAccept"
        @dismiss="handleDismiss">
      </calendar>
    </mu-dialog>
  </div>
</template>

<script>
import * as dateUtils from '../../../es/dateUtils';
import calendar from './calendar';

export default {
  name: 'dateRangePicker',
  data() {
    return {
      dialogVisible: false,
      inputValue: this.value,
      dialogDate: null,
    };
  },
  components: { calendar },
  props: {
    value: {
      type: Array,
    },
    hintText: {
      type: String,
    },
    format: {
      type: String,
      default: 'YYYY-MM-DD',
    },
    maxDate: {
      type: Date,
      default() {
        return dateUtils.addYears(new Date(), 100);
      },
    },
    minDate: {
      type: Date,
      default() {
        return dateUtils.addYears(new Date(), -100);
      },
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    shouldDisableDate: {
      type: Function,
    },
  },
  computed: {
    dateRange() {
      return this.inputValue.length === 2 ? `${this.inputValue[0]} - ${this.inputValue[1]}` : '';
    },
  },
  watch: {
    value(val) {
      this.inputValue = val;
    },
    inputValue(val) {
      this.$emit('input', val);
    },
  },
  methods: {
    handleFocus(event) {
      event.target.blur();
      this.$emit('focus', event);
    },
    handleClick() {
      if (!this.disabled) {
        setTimeout(() => {
          this.openDialog();
        }, 0);
      }
    },
    openDialog() {
      if (this.disabled) return;
      this.dialogDate = this.inputValue.length ? dateUtils.strFormatToDate(this.inputValue[0], this.format) : new Date();
      this.dialogVisible = true;
    },
    handleAccept(val) {
      const newValue = val.map(date => dateUtils.dateToStr(date, this.format));
      this.inputValue = newValue;
      this.dialogVisible = false;
      this.$emit('change', newValue);
    },
    handleDismiss() {
      this.dialogVisible = false;
      this.$emit('dismiss');
    },
  },
};
</script>
