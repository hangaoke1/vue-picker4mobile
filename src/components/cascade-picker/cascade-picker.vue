<template>
  <div class="cascade-picker">
    <picker :data="pickerData" :selectedIndex="pickerSelectedIndex" ref="picker" @onChange="handleChange" @onValueChange="handelValueChange"></picker>
  </div>
</template>
<script>
import picker from '../picker/picker'
const EVENT_VALUE_CHANGE = 'onValueChange'
const EVENT_CHANGE = 'onChange'
export default {
  name: 'cascadePicker',
  components: {
    picker
  },
  props: {
    data: {
      type: Array,
      default () {
        return []
      }
    }
  },
  data () {
    return {
      cascadeData: this.data.slice(), // 级联数据结构
      pickerData: [],
      pickerSelectedIndex: [0, 0, 0]
    }
  },
  created () {
    this.updatePickerData()
  },
  mounted () {},
  methods: {
    handelValueChange (selectedValue, selectedIndex, selectedName) {
      this.$emit(EVENT_VALUE_CHANGE, selectedValue, selectedIndex, selectedName)
    },
    handleChange (i, newIndex) {
      if (newIndex !== this.pickerSelectedIndex[i]) {
        this.pickerSelectedIndex.splice(i, 1, newIndex)
        this.updatePickerData(i + 1)
      }
      this.$emit(EVENT_CHANGE, i, newIndex)
    },
    updatePickerData (fromColumn = 0) {
      let data = this.cascadeData
      let i = 0
      while (data) {
        if (i >= fromColumn) {
          let columnData = []
          data.forEach((item) => {
            columnData.push({
              value: item.value,
              name: item.name
            })
          })
          this.pickerData[i] = columnData
          this.pickerSelectedIndex[i] = fromColumn === 0 ? (this.pickerSelectedIndex[i] < data.length ? this.pickerSelectedIndex[i] || 0 : 0) : this.$refs.picker.refillColumn(i, columnData)
        }
        data = data.length ? data[this.pickerSelectedIndex[i]].children : null
        i++
      }
      this.pickerData = this.pickerData.slice()
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
</style>
