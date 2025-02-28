<template>
  <div id="app">
    <!-- <div>{{ timer }}</div> -->
    <!-- <SelectDemo /> -->
    <!-- <Cascader /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <Echart />
  </div>
</template>

<script>
import Echart from './components/Echart.vue'
import HelloWorld from './components/HelloWorld.vue'
import SelectDemo from './components/SelectDemo.vue'
// import Cascader from './components/Cascader.vue'
import moment from 'moment'
export default {
  name: 'App',
  components: {
    Echart,
    HelloWorld,
    SelectDemo
    // Cascader
  },
  data() {
    return {
      timer: '',
      t1: '2023-07-05 16:45:09',
      timerList: [
        { start: '2023-07-07 16:00:00', end: '2023-07-07 20:59:59' },
        { start: '2023-07-07 21:00:00', end: '2023-07-07 23:00:00' },
        { start: '2023-07-07 08:00:00', end: '2023-07-07 15:00:00' },
        { start: '2023-07-08 08:00:00', end: '2023-07-08 18:00:00' }
      ]
    }
  },
  mounted() {
    let a = moment().format()
    console.log('a: ', a)
    this.timer = moment(this.t1).valueOf()

    let result = this.judge(this.timerList)
    console.log('result: ', result)
  },
  methods: {
    deepClone(obj) {
      let objClone = Array.isArray(obj) ? [] : {}
      if (obj && typeof obj === 'object') {
        for (var key in obj) {
          if (Object.prototype.hasOwnProperty.call(obj, key)) {
            if (obj[key] && typeof obj[key] === 'object') {
              objClone[key] = this.deepClone(obj[key])
            } else {
              objClone[key] = obj[key]
            }
          }
        }
      }
      return objClone
    },
    changeFormat(list) {
      let result = list.map((item) => {
        return {
          start: moment(item.start).valueOf(),
          end: moment(item.end).valueOf()
        }
      })
      return result
    },
    sortList(list) {
      let newList = this.deepClone(list)
      var temp
      for (var i = 0; i < newList.length - 1; i++) {
        for (var j = i + 1; j < newList.length; j++) {
          if (newList[i].start > newList[j].start) {
            temp = newList[i]
            newList[i] = newList[j]
            newList[j] = temp
          }
        }
      }
      return newList
    },
    judge(list) {
      let result = false
      let ss1 = this.changeFormat(list)
      console.log('ss1: ', ss1)
      let sortArr = this.sortList(ss1)
      console.log('sortArr: ', sortArr)

      for (var i = 0; i < sortArr.length - 1; i++) {
        if (sortArr[i].end < sortArr[i + 1].start) {
          console.log('success')
        } else {
          result = true
          console.log(`the ${i + 1} and ${i + 2} rang is conflicting`)
        }
      }

      return result
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
