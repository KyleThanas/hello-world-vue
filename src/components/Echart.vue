<template>
  <div id="main" style="width: 600px; height: 400px">123</div>
</template>

<script>
import * as echarts from 'echarts'
// import $ from 'jquery'
import lesMiserables from './les-miserables.json'
import lesMiserables1 from './les-miserables1.json'
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
    // this.initChart()
    // this.initChartChange()
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
      this.viewType = type
      let baseData = this.baseData()
      let initialData = []
      let initialLinks = []
      if (type == 'level1') {
        let tmpObj = {}
        baseData.forEach((item) => {
          if (item.level != 3) {
            let tmp = {
              name: item.name,
              parent: item?.parent || '',
              level: item.level
            }
            initialData.push(tmp)
          } else {
            if (tmpObj[item.parent]) {
              tmpObj[item.parent] += 1
            } else {
              tmpObj[item.parent] = 1
            }
          }
        })
        for (let i in tmpObj) {
          let tmp = {
            name: i + '子',
            value: tmpObj[i],
            parent: i,
            level: 3
          }
          initialData.push(tmp)
        }
        initialData.forEach((item) => {
          if (item.level != 1) {
            let targetObj = baseData.find((el) => el.id == item.parent)
            let tmp = {
              source: item.name + '',
              target: targetObj.name
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
              name: item.name,
              level: item.level
            }
            initialData.push(tmp)
          }
        })
        initialData.forEach((item) => {
          let tmp = {
            source: item.name,
            target: centerObj.name
          }
          initialLinks.push(tmp)
        })
      }
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
        tooltip: {},
        legend: [
          {
            data: 'A'
          }
        ],
        animation: false,
        animationDuration: 1500,
        animationEasingUpdate: 'quinticInOut',
        series: [
          {
            type: 'graph',
            layout: 'force',
            data: initialData,
            links: initialLinks,
            force: {
              repulsion: 100
            },
            label: {
              show: true,
              position: 'right',
              formatter: '{b}'
            },
            lineStyle: {
              color: 'source'
            }
          }
        ]
      }
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option)

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
          myChart.setOption(option)
        } else if (params.dataType === 'node' && params.data.level == 1) {
          if (_this.viewType == 'level1') return
          const { initialData, initialLinks } = _this.calcData('level1')
          option.series[0].data = initialData
          option.series[0].links = initialLinks
          // 更新图表
          myChart.setOption(option)
        } else {
        }
      })
    },
    initChart() {
      var chartDom = document.getElementById('main')
      var myChart = echarts.init(chartDom)
      var option
      let graph
      myChart.showLoading()
      myChart.hideLoading()
      graph = lesMiserables
      // graph = lesMiserables1
      graph.nodes.forEach(function (node) {
        node.label = {
          show: node.symbolSize > 3
        }
      })
      option = {
        title: {
          text: 'Les Miserables',
          subtext: 'Default layout',
          top: 'bottom',
          left: 'right'
        },
        tooltip: {},
        legend: [
          {
            // selectedMode: 'single',
            data: graph.categories.map(function (a) {
              return a.name
            })
          }
        ],
        animationDuration: 1500,
        animationEasingUpdate: 'quinticInOut',
        series: [
          {
            name: 'Les Miserables',
            type: 'graph',
            legendHoverLink: false,
            layout: 'none',
            data: graph.nodes,
            links: graph.links,
            categories: graph.categories,
            roam: true,
            label: {
              position: 'right',
              formatter: '{b}'
            },
            lineStyle: {
              color: 'source'
              // curveness: 0.3 // 边的曲度，支持从 0 到 1 的值，值越大曲度越大。
            },
            emphasis: {
              focus: 'adjacency',
              lineStyle: {
                width: 20
              }
            }
          }
        ]
      }
      myChart.setOption(option)
      option && myChart.setOption(option)
    },
    initChartChange() {
      var myChart = echarts.init(document.getElementById('main'))

      // 初始数据
      var initialData = [
        { name: '节点1', label: { show: true } },
        { name: '节点2', label: { show: true } },
        { name: '节点3', label: { show: true } }
      ]
      var initialLinks = [
        { source: '节点1', target: '节点2' },
        { source: '节点2', target: '节点3' },
        { source: '节点3', target: '节点4' }
      ]

      // 切换后的数据（示例）
      var switchedData = [
        { name: '新节点A', label: { show: true } },
        { name: '新节点B', label: { show: true } },
        { name: '新节点C', label: { show: true } }
      ]
      var switchedLinks = [
        { source: '新节点A', target: '新节点B' },
        { source: '新节点B', target: '新节点C' },
        { source: '新节点C', target: '新节点A' }
      ]

      // 初始配置项
      var option = {
        title: {
          text: 'Les Miserables',
          subtext: 'Default layout',
          top: 'bottom',
          left: 'right'
        },
        tooltip: {},
        legend: [
          {
            data: 'A'
          }
        ],
        animationDuration: 1500,
        animationEasingUpdate: 'quinticInOut',
        series: [
          {
            type: 'graph',
            layout: 'force',
            data: initialData,
            links: initialLinks,
            force: {
              repulsion: 100
            },
            label: {
              position: 'right',
              formatter: '{b}'
            },
            lineStyle: {
              color: 'source'
            }
          }
        ]
      }

      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option)

      // 监听节点点击事件
      myChart.on('click', function (params) {
        if (params.dataType === 'node') {
          // 切换视图
          if (option.series[0].data === initialData) {
            option.series[0].data = switchedData
            option.series[0].links = switchedLinks
          } else {
            option.series[0].data = initialData
            option.series[0].links = initialLinks
          }
          // 更新图表
          myChart.setOption(option)
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped></style>
