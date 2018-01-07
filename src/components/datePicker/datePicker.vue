<template>
  <div class="date-picker">
    <cascadePicker :data="data" @onValueChange="handelValueChange" @onChange="handleChange"></cascadePicker>
  </div>
</template>
<script>
import cascadePicker from '../cascade-picker/cascade-picker'
const EVENT_VALUE_CHANGE = 'onValueChange'
const EVENT_CHANGE = 'onChange'
export default {
  name: 'datePicker',
  components: {
    cascadePicker
  },
  props: {},
  data () {
    return {
      min: [2000, 8, 8],
      max: [2020, 10, 10],
      pickerArr: []
    }
  },
  computed: {
    data () {
      let data = range(this.min[0], this.max[0], false, '年')
      data.forEach(year => {
        let minMonth = year.value === this.min[0] ? this.min[1] : 1
        let maxMonth = year.value === this.max[0] ? this.max[1] : 12
        year.children = range(minMonth, maxMonth, false, '月')
        year.children.forEach(month => {
          let day = 30
          if ([1, 3, 5, 7, 8, 10, 12].includes(month.value)) {
            day = 31
          } else {
            if (month.value === 2) {
              day = !(year.value % 400) || (!(year.value % 4) && year.value % 100) ? 29 : 28
            }
          }
          let minDay = year.value === this.min[0] && month.value === this.min[1] ? this.min[2] : 1
          let maxDay = year.value === this.max[0] && month.value === this.max[1] ? this.max[2] : day
          month.children = range(minDay, maxDay, false, '日')
        })
      })
      return data
    }
  },
  mounted () {
  },
  methods: {
    handelValueChange (selectedValue, selectedIndex, selectedName) {
      this.$emit(EVENT_VALUE_CHANGE, selectedValue, selectedIndex, selectedName)
    },
    handleChange (i, index) {
      // i变动的列， index变动后的位置
      this.$emit(EVENT_CHANGE, i, index)
    }
  }
}

function range (n, m, polyfill = false, unit = '') {
  let arr = []
  for (let i = n; i <= m; i++) {
    let value = (polyfill && i < 10 ? '0' + i : i) + unit
    arr.push({
      name: value,
      value: i
    })
  }
  return arr
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
</style>
