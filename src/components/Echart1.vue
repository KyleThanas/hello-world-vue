<template>
  <div>
    <div
      id="main"
      style="width: 400px; height: 400px; background: gainsboro"
    ></div>
  </div>
</template>

<script>
import * as echarts from 'echarts'
export default {
  components: {},
  data() {
    return { viewType: 'level1' }
  },
  props: {},
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.initChartChangeNew()
  },
  methods: {
    baseData() {
      return [
        { id: 1, name: '张大力', level: 1 },
        { id: 11, name: '关注号1', level: 2, parent: 1 },
        { id: 12, name: '关注号2', level: 2, parent: 1 },
        { id: 13, name: '关注号3', level: 2, parent: 1 },
        { id: 14, name: '关注号4', level: 2, parent: 1 },
        { id: 15, name: '关注号5', level: 2, parent: 1 },
        { id: 16, name: '关注号6', level: 2, parent: 1 },
        { id: 17, name: '关注号7', level: 2, parent: 1 },
        { id: 18, name: '关注号8', level: 2, parent: 1 },
        { id: 19, name: '关注号9', level: 2, parent: 1 },
        { id: 111, name: '粉丝1', level: 3, parent: 11 },
        { id: 112, name: '粉丝2', level: 3, parent: 12 },
        { id: 113, name: '粉丝3', level: 3, parent: 13 },
        { id: 114, name: '粉丝4', level: 3, parent: 14 },
        { id: 115, name: '粉丝5', level: 3, parent: 14 },
        { id: 116, name: '粉丝6', level: 3, parent: 16 },
        { id: 117, name: '粉丝7', level: 3, parent: 16 },
        { id: 118, name: '粉丝8', level: 3, parent: 16 }
      ]
    },
    calcData(type = 'level1', centerName = '') {
      let pic =
        'image://data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7'
      this.viewType = type
      let baseData = this.baseData()
      let initialData = []
      let initialLinks = []
      if (type == 'level1') {
        baseData.forEach((item) => {
          let tmp = {
            id: item.id + '',
            name: item.name,
            parent: item?.parent || '',
            symbolSize: 30,
            itemStyle: { color: '#fac858' },
            level: item.level
          }
          if (item.level == 2) {
            initialData.push(tmp)
          }
          if (item.level == 1) {
            tmp.symbol = pic
            tmp.symbolSize = 40
            tmp.itemStyle = { color: '#5470c6' }
            initialData.push(tmp)
          }
          if (item.level == 3) {
            let tmpIndex = initialData.findIndex(
              (el) => el.parent == item.parent
            )
            if (tmpIndex == -1) {
              tmp.name = 1
              tmp.symbolSize = 25
              tmp.itemStyle = { color: '#91cc75' }
              initialData.push(tmp)
            } else {
              initialData[tmpIndex].name += 1
            }
          }
        })
        initialData.forEach((item) => {
          if (item.level != 1) {
            let tmp = {
              source: item.parent + '',
              target: item.id + ''
            }
            initialLinks.push(tmp)
          }
        })
      } else {
        let centerObj = baseData.find((el) => el.name == centerName)
        baseData.forEach((item) => {
          if (
            item.level == 1 ||
            centerObj.id == item.id ||
            centerObj.id == item.parent
          ) {
            let tmp = {
              id: item.id + '',
              name: item.name,
              parent: item?.parent || '',
              symbolSize: 30,
              itemStyle: { color: '#fac858' },
              level: item.level
            }
            if (item.level == 1) {
              tmp.symbol = pic
              tmp.symbolSize = 40
              tmp.itemStyle = { color: '#5470c6' }
            }
            if (item.level == 3) {
              tmp.symbolSize = 25
              tmp.itemStyle = { color: '#91cc75' }
            }

            initialData.push(tmp)
            let tmp1 = {
              source: centerObj.id + '',
              target: item.id + ''
            }
            initialLinks.push(tmp1)
          }
        })
      }
      console.log('initialData, initialLinks: ', initialData, initialLinks)
      return {
        initialData,
        initialLinks
      }
    },
    initChartChangeNew() {
      var chartDom = document.getElementById('main')
      var myChart = echarts.init(chartDom)
      myChart.showLoading()
      myChart.hideLoading()
      const { initialData, initialLinks } = this.calcData()
      // 初始配置项
      var option = {
        series: [
          {
            type: 'graph',
            layout: 'force',
            force: {
              // layoutAnimation: false,
              repulsion: 200
            },
            data: initialData,
            links: initialLinks,
            label: {
              show: true,
              formatter: '{b}'
            },
            lineStyle: {
              color: 'source'
            }
          }
        ]
      }
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option, true)

      // 监听节点点击事件
      const _this = this
      myChart.on('click', function (params) {
        if (params.dataType === 'node' && params.data.level == 2) {
          if (_this.viewType == 'level2') return
          const { initialData, initialLinks } = _this.calcData(
            'level2',
            params.data.name
          )
          option.series[0].data = initialData
          option.series[0].links = initialLinks
          // 更新图表
          option.series[0].force.initLayout = null
          myChart.setOption(option, true)
        }
        if (params.dataType === 'node' && params.data.level == 1) {
          if (_this.viewType == 'level1') return
          const { initialData, initialLinks } = _this.calcData('level1')
          option.series[0].data = initialData
          option.series[0].links = initialLinks
          // 更新图表
          option.series[0].force.initLayout = null
          myChart.setOption(option, true)
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped></style>
