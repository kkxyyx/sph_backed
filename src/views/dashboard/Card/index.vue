<template>
  <el-row :gutter="20">
    <el-col :span="6">
      <el-card>
        <detail title="总销售额" count="￥ 12324">
          <template slot="charts">
            <span>周同比&nbsp;&nbsp;23.2%</span>
            <i class="el-icon-caret-bottom" style="color: red;" />
            <span>&nbsp;&nbsp;日同比&nbsp;&nbsp;24.3%</span>
            <i class="el-icon-caret-top" />
          </template>
          <template slot="footer">
            <span>日销售额 ￥1234</span>
          </template>
        </detail>
      </el-card>
    </el-col>
    <el-col :span="6">
      <el-card>
        <detail title="访问量" count="7232">
          <template slot="charts">
            <lineChart />
          </template>
          <template slot="footer">
            <span>日访问量 ￥123</span>
          </template>
        </detail>
      </el-card>
    </el-col>
    <el-col :span="6">
      <el-card>
        <detail title="支付笔数" count="12324">
          <template slot="charts">
            <barChart />
          </template>
          <template slot="footer">
            <span>转化率 23%</span>
          </template>
        </detail>
      </el-card>
    </el-col>
    <el-col :span="6">
      <el-card>
        <detail title="运行活动效果" count="12%">
          <template slot="charts">
            <progressChart />
          </template>
          <template slot="footer">
            <span>周同比&nbsp;&nbsp;12.2%</span>
            <i class="el-icon-caret-bottom" style="color: red;" />
            <span>&nbsp;&nbsp;日同比&nbsp;&nbsp;26.3%</span>
            <i class="el-icon-caret-top" />
          </template>
        </detail>
      </el-card>
    </el-col>
  </el-row>
</template>

<script>
import Detail from './detail'
import lineChart from './lineChart'
import barChart from './barChart'
import progressChart from './progressChart'

export default {
  name: 'Card',
  components: {
    Detail,
    lineChart,
    barChart,
    progressChart
  }
}
</script>

<style scoped>

.el-icon-caret-top{
  color: rgb(117, 174, 31);
}
</style>
