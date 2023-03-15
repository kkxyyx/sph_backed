<template>
  <div ref="echarts" class="echarts" style="width:100%;height:100%;" />
</template>

<script>
import * as echarts from 'echarts'
export default {
  name: 'ProgressChart',
  mounted() {
    const progressChart = echarts.init(this.$refs.echarts)
    progressChart.setOption({
      xAxis: {
        show: false,
        min: 0,
        max: 100
      },
      yAxis: {
        type: 'category',
        show: false
      },
      series: [
        {
          type: 'bar',
          barWidth: 10,
          data: [78],
          color: 'yellowgreen',
          showBackground: true,
          backgroundStyle: {
            color: '#eee'
          },
          // 文本
          label: {
            show: true,
            formatter: '|',
            position: 'right'
          }
        }
      ]
    })
  }

}
</script>

<style>

</style>
