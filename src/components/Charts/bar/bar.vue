<template>
<div style="height:100%">
  <div :class="className" :id="id"  :style="{height:height,width:width}"/>
</div>
</template>

<script>
import resize from '../mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    id: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '200px'
    },
    height: {
      type: String,
      default: '200px'
    },
    themeType: {
      type: String,
      default: 'macarons'
    }
  },
  data() {
    return {
      chart: null
    }
  },
  mounted() {
    this.initChart() // 初始化
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  watch: {
    themeType() {
      this.chart.dispose() // 实例销毁
      this.chart = null
      this.initChart() // 重新初始化
    }
  },
  methods: {
    initChart() {
      this.chart = this.$echarts.init(document.getElementById(this.id), this.themeType)
      const xData = (function() {
        const data = []
        for (let i = 1; i < 13; i++) {
          data.push(i + 'month')
        }
        return data
      }())
      this.chart.setOption({
        // backgroundColor: '#344b58',
        title: {
          text: '某地区蒸发量和降水量',
          subtext: '纯属虚构'
        },
        // 提示框
        tooltip: {
          trigger: 'axis'
        },
        // 图例
        legend: {
          data: ['蒸发量', '降水量']
        },
        // 工具栏
        toolbox: {
          show: true,
          feature: {
            mark: { show: true },
            dataView: { show: true, readOnly: false },
            magicType: { show: true, type: ['line', 'bar', 'stack', 'tiled'] },
            restore: { show: true },
            saveAsImage: { show: true }
          }
        },
        // 是否显示拖拽用的手柄（手柄能拖拽调整选中范围）
        calculable: true,
        // 直角坐标系中横轴数组
        xAxis: [
          {
            type: 'category',
            data: xData
          }
        ],
        // 直角坐标系中纵轴数组
        yAxis: [
          {
            type: 'value'
          }
        ],
        // 通用
        series: [
          {
            name: '蒸发量',
            type: 'bar', // 图表类型，折线图line、散点图scatter、柱状图bar、饼图pie、雷达图radar
            data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2, 32.6, 20.0, 6.4, 3.3],
            markPoint: {
              data: [
                { type: 'max', name: '最大值' },
                { type: 'min', name: '最小值' }
              ]
            },
            markLine: {
              data: [
                { type: 'average', name: '平均值' }
              ]
            }
          },
          {
            name: '降水量',
            type: 'bar',
            data: [2.6, 5.9, 9.0, 26.4, 28.7, 70.7, 175.6, 182.2, 48.7, 18.8, 6.0, 2.3],
            markPoint: {
              data: [
                { name: '年最高', value: 182.2, xAxis: 7, yAxis: 183, symbolSize: 18 },
                { name: '年最低', value: 2.3, xAxis: 11, yAxis: 3 }
              ]
            },
            markLine: {
              data: [
                { type: 'average', name: '平均值' }
              ]
            }
          }
        ],
        // 数据区域缩放
        dataZoom: [{
          show: true,
          start: 10,
          end: 80
        },
        { // 鼠标滚轮缩放
          type: 'inside',
          show: true,
          height: 15,
          start: 1,
          end: 35
        }
        ]
      })
    }
  }
}
</script>
