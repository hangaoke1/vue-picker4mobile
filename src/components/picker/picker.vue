<template>
  <div class="picker">
    <div class="picker-action">
      <button class="picker-confirm" @click="confirm">确定</button>
    </div>
    <div class="picker-content">
      <div class="border-top-1px"></div>
      <div class="border-bottom-1px"></div>
      <div class="picker-wrap" ref="wheelWrapper">
        <div class="wheel-wrapper" v-for="(item, index) in pickerArr" :key="index">
          <ul class="wheel-scroll">
            <li v-for="(subItem, index) in item" class="wheel-item" :key="subItem.value">{{subItem.name}}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import BScroll from 'better-scroll'
const EVENT_VALUE_CHANGE = 'onValueChange'
const EVENT_CHANGE = 'onChange'

export default {
  name: 'picker',
  props: {
    data: {
      type: Array,
      default () {
        return []
      }
    },
    selectedIndex: {
      type: Array,
      default () {
        return []
      }
    }
  },
  watch: {
    data (newData) {
      this.setData(newData, this.selectedIndex)
    }
  },
  data () {
    return {
      pickerArr: this.data.slice(), // 展示的数据，二维数组
      pickerSelectedIndex: [], // 选中的Index
      wheels: [],
      pickerSelectedVal: []
    }
  },
  mounted () {
    let wheelWrapper = this.$refs.wheelWrapper
    this.initWheel(wheelWrapper)
  },
  methods: {
    confirm () {
      if (!this._canConfirm()) {
        return
      }
      let changed = false
      let selectedValue = []
      let selectedIndex = this.pickerSelectedIndex.slice()
      let selectedName = []
      this.pickerSelectedIndex.forEach((item, i) => {
        selectedValue[i] = this.pickerArr[i][item].value
        selectedName[i] = this.pickerArr[i][item].name
        if (this.pickerSelectedVal[i] !== selectedValue[i]) {
          changed = true
          this.pickerSelectedVal[i] = selectedValue[i]
        }
      })
      if (changed) {
        this.$emit(EVENT_VALUE_CHANGE, selectedValue, selectedIndex, selectedName)
      }
    },
    _canConfirm () {
      return this.wheels.every((wheel) => {
        return !wheel.isInTransition
      })
    },
    // i是列 newData是该列的新数据
    refillColumn (i, newData) {
      let newIndex = 0
      const wheelWrapper = this.$refs.wheelWrapper
      let scroll = wheelWrapper.children[i].querySelector('.wheel-scroll')
      let wheel = this.wheels ? this.wheels[i] : false

      // 如果原来没有则直接忽略计算
      if (scroll && wheel) {
        const oldData = this.pickerArr[i]
        const oldSelect = this.pickerSelectedIndex[i] || 0
        const oldVal = oldData[oldSelect].value
        if (oldSelect) {
          newData.forEach((item, index) => {
            if (item.value === oldVal) {
              newIndex = index
            }
          })
        }
      }
      this.pickerSelectedIndex[i] = newIndex
      this.$nextTick(() => {
        this._createWheel(this.$refs.wheelWrapper, i)
        this.wheels[i].wheelTo(newIndex)
      })
      return newIndex
    },
    initWheel (wheelWrapper) {
      for (let i = 0, length = this.pickerArr.length; i < length; i++) {
        this._createWheel(wheelWrapper, i)
      }
    },
    setData (newData, selectedIndex) {
      this.pickerArr = newData.slice()
      this.pickerSelectedIndex = selectedIndex ? [...selectedIndex] : []
      this.wheels.forEach((wheel, i) => {
        wheel.refresh()
        wheel.wheelTo(this.pickerSelectedIndex[i])
      })
    },
    _createWheel (wheelWrapper, i) {
      if (!this.wheels[i]) {
        const wheel = this.wheels[i] = new BScroll(wheelWrapper.children[i], {
          wheel: {
            selectedIndex: this.pickerSelectedIndex[i] || 0,
            wheelWrapperClass: 'wheel-scroll',
            wheelItemClass: 'wheel-item'
          },
          probeType: 3
        })
        this.pickerSelectedIndex[i] = wheel.getSelectedIndex()
        wheel.on('scrollEnd', () => {
          this.pickerSelectedIndex[i] = wheel.getSelectedIndex()
          this.$emit(EVENT_CHANGE, i, wheel.getSelectedIndex())
        })
      } else {
        this.wheels[i].refresh()
      }
      return this.wheels[i]
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
ul{
  padding: 0;
  margin: 0;
}
.picker {
  width: 200px;
  position: relative;
  border: 1px solid red;
  .border-top-1px {
    position: absolute;
    top: 0;
    width: 100%;
    height: 72px;
    border-bottom: 1px solid #ccc;
  }
  .border-bottom-1px {
    position: absolute;
    top: 0;
    width: 100%;
    height: 108px;
    border-bottom: 1px solid #ccc;
  }
}
.picker-confirm{
  cursor: pointer;
  outline: none;
  border: 1px solid #ccc;
  background: #fff;
  &:hover{
    outline: 1px dashed red;
  }
}
.picker-content{
  position: relative;
}
.picker-wrap{
  display: flex;
  justify-content: space-between;
}
.wheel-wrapper {
  flex: 1;
  height: 173px;
  overflow: hidden;
}

.wheel-scroll {
  list-style: none;
  margin-top: 72px;
  .wheel-item {
    height: 36px;
    line-height: 36px;
  }
}
</style>
