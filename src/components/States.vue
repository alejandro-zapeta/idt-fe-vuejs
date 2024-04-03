<template>
  <div class="boxScrollable">
    <div @click="handleEventClick(item)" :class="{ 'highlighted': item.disabled }" v-for="item in states"
      :key="item.idstate">
      {{ item.state }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'StatesItem',
  props: {
    states: Array,
    handleSelectItem: Function
  },
  data() {
    return {
      delay: 450,
      clicks: 0,
      timer: null
    }
  },
  methods: {
    toggleInput(item) {
      if (item.disabled) {
        item.disabled = !item.disabled;
      } else {
        item.disabled = true;
      }
    },
    handleEventClick(item) {
      this.clicks++;
      if (this.clicks === 1) {
        this.timer = setTimeout(() => {
          this.clicks = 0
          this.handleSelectItem(item)
          clearTimeout(this.timer);
        }, this.delay);
      } else {
        this.toggleInput(item)
        clearTimeout(this.timer);
        this.clicks = 0;
      }
    }
  }
}
</script>

<style scoped>
.highlighted {
  background-color: gray;
}
</style>
