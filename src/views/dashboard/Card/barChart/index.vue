<template>
  <div ref="echarts" class="echarts" style="width:100%;height:100%;" />
</template>

<script>
import * as echarts from 'echarts'
export default {
  name: 'BarChart',
  mounted() {
    const barChart = echarts.init(this.$refs.echarts)
    barChart.setOption({
      xAxis: {
        type: 'category',
        show: false
      },
      yAxis: {
        show: false
      },
      series: [
        {
          type: 'bar',
          data: [12, 23, 13, 65, 34, 46]
        }
      ]
    })
  }

}
</script>

<style>

</style>
