<template>
  <div class="picker-slot">
    <div class="picker-slot-wrap">
      <div class="picker-item" :style="{height: itemHeight + 'px', lineHeight: itemHeight + 'px'}" v-for="item in [2001, 2002, 2003, 2004]">{{item}}</div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'picker-slot',
  props: {
    itemHeight: {
      type: Number,
      default: 36
    }
  },
  data () {
    return {
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
