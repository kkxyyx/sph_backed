<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="sale">
        <span>销售额类别占比</span>
        <el-radio-group v-model="value">
          <el-radio-button label="全部渠道" />
          <el-radio-button label="线上" />
          <el-radio-button label="门店" />
        </el-radio-group>
      </div>
      <div ref="charts" class="charts" style="width:100%;height:240px" />
    </el-card>
  </div>
</template>

<script>
// 引入echarts
import * as echarts from 'echarts'
export default {
  name: '',
  data() {
    return {
      value: '全部渠道'
    }
  },
  mounted() {
    const mycharts = echarts.init(this.$refs.charts)
    mycharts.setOption(
      {
        title: {
          text: '视频',
          subtext: '1048',
          top: 'center',
          left: 'center'
        },
        tooltip: {
          trigger: 'item'
        },
        series: [
          {
            name: 'Access From',
            type: 'pie',
            radius: ['40%', '70%'],
            // avoidLabelOverlap: false,
            itemStyle: {
              borderRadius: 10,
              borderColor: '#fff',
              borderWidth: 2
            },
            label: {
              show: true,
              position: 'outside'
            },
            data: [
              { value: 1048, name: 'Search Engine' },
              { value: 735, name: 'Direct' },
              { value: 580, name: 'Email' },
              { value: 484, name: 'Union Ads' },
              { value: 300, name: 'Video Ads' }
            ]
          }
        ]
      })
    mycharts.on('mouseover', (params) => {
      const { name, value } = params.data
      mycharts.setOption({
        title: {
          text: name,
          subtext: value
        }
      })
    })
  }
}
</script>

<style scoped>
.sale{
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.box-card{
  height: 351px;
}
</style>
