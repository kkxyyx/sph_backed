<template>
  <div>
    <div class="header">
      <span>{{ title }}</span>
      <i class="el-icon-info" />
    </div>
    <div class="main">
      <span class="main-title">{{ number }}</span>
      <span class="main-content">{{ change }}</span>
      <i class="el-icon-caret-top" style="color:red" />
    </div>
    <div class="footer">
      <div ref="charts" class="charts" />
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts'
export default {
  name: '',
  props: {
    number: {
      type: Number,
      default: 0
    },
    change: {
      type: Number,
      default: 0
    },
    title: {
      type: String,
      default: 'XXX'
    }
  },
  mounted() {
    // 初始化echarts实例
    const lineCharts = echarts.init(this.$refs.charts)
    // 配置数据
    lineCharts.setOption({
      xAxis: {
        // 隐藏x轴
        show: false,
        type: 'category'
      },
      yAxis: {
        // 隐藏y轴
        show: false
      },
      // 系列
      series: [
        {
          type: 'line',
          data: [10, 7, 33, 12, 48, 9, 29, 10, 4],
          // 拐点的样式的设置
          itemStyle: {
            opacity: 0
          },
          // 线条的样式
          lineStyle: {
            color: 'blue'
          },
          // 填充颜色设置
          areaStyle: {
            color: {
              type: 'linear',
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              colorStops: [
                {
                  offset: 0,
                  color: 'blue' // 0% 处的颜色
                },
                {
                  offset: 1,
                  color: '#fff' // 100% 处的颜色
                }
              ],
              global: false // 缺省为 false
            }
          },
          smooth: true
        }
      ],
      // 布局调试
      grid: {
        left: 0,
        top: 0,
        right: 0,
        bottom: 0
      }
    })
  }
}
</script>

<style>
.header{
    color: rgb(218, 215, 215);
}
.header span{
margin-right: 10px;
}
.main{
    margin: 10px 0;
}
.main .main-title{
    font-size: 20px;
    font-weight: 700;
    margin: 20px 20px;
}
.main .main-content{
color: rgb(224, 221, 221);
}
.charts{
    width: 100%;
    height: 50px
}
</style>
