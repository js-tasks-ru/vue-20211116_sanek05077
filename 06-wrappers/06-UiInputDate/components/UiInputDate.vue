<template>
  <ui-input ref="uiInput" v-model="modelDate" :type="type" @input="handlerInput">
    <template v-for="slotName in Object.keys($slots)" #[slotName]>
      <slot :name="slotName" />
    </template>
  </ui-input>
</template>

<script>
const getDate = (date) => {
  return date.toISOString().split('T')[0];
};
const getTime = (date) => {
  return date.toISOString().split('.')[0].split('T')[1];
};
const getDatetimeLocal = (date) => {
  return date.toISOString().split('.')[0];
};

import UiInput from './UiInput';

export default {
  name: 'UiInputDate',
  components: { UiInput },
  props: {
    type: {
      type: String,
      default: 'date',
    },
    modelValue: {
      default: Number,
    },
  },
  emits: ['update:modelValue'],
  computed: {
    dateFormat() {
      let date = new Date(this.modelValue);
      if (this.type === 'time') {
        if (this.$attrs.step && this.$attrs.step % 60 !== 0) {
          return getTime(date);
        }
        return getTime(date).slice(0, -3);
      }
      if (this.type === 'datetime-local') {
        return getDatetimeLocal(date);
      }
      return getDate(date);
    },
    modelDate: {
      get() {
        return this.modelValue ? this.dateFormat : null;
      },
    },
  },
  methods: {
    handlerInput(event) {
      this.$emit('update:modelValue', event.target.valueAsNumber);
    },
  },
};
</script>
