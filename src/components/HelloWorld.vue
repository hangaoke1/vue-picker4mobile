<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <!-- <picker :data="pickerArr" ref="pickerx"></picker> -->
    <date-picker @onValueChange="handelValueChange"></date-picker>
  </div>
</template>
<script>
import picker from './picker/picker'
import cascadePicker from './cascade-picker/cascade-picker'
import datePicker from './datePicker/datePicker'
export default {
  name: 'hello',
  components: {
    picker,
    cascadePicker,
    datePicker
  },
  data () {
    return {
      msg: 'Picker',
      pickerArr: [
        [{
          name: '北京',
          value: '北京'
        }, {
          name: '浙江',
          value: '浙江'
        }, {
          name: '江苏',
          value: '江苏'
        }, {
          name: '黑龙江',
          value: '黑龙江'
        }],
        [{
          name: '杭州',
          value: '杭州'
        }, {
          name: '南京',
          value: '南京'
        }, {
          name: '齐齐哈尔',
          value: '齐齐哈尔'
        }, {
          name: '宁波',
          value: '宁波'
        }],
        []
      ]
    }
  },
  mounted () {
  },
  methods: {
    handelValueChange (selectedValue, selectedIndex, selectedName) {
      console.log(selectedValue)
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
