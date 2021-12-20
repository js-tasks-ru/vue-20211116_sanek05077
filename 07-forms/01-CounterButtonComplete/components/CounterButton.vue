<template>
  <button @click="localCounter += 1">{{ localCounter }}</button>
</template>

<script>
export default {
  name: 'CounterButton',
  props: {
    count: {
      type: Number,
      default: 0,
    },
  },
  emits: ['update:count'],
  data() {
    return {
      localCounter: this.count,
    };
  },
  watch: {
    count: {
      immediate: true,
      handler(newValue) {
       this.localCounter = newValue;
      },
    },
    localCounter: {
      handler(newValue) {
        this.$emit('update:count', newValue);
      },
    },
  },
};
</script>
