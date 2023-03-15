<template>
  <el-card class="box-card" style="margin-top:12px">
    <div slot="header" class="clearfix">
      <!-- 头部左侧 -->
      <el-tabs v-model="activeName" class="tabs">
        <el-tab-pane label="销售额" name="sale" />
        <el-tab-pane label="访问量" name="visite" />
      </el-tabs>
      <!-- 头部右侧 -->
      <div class="right">
        <span @click="setDay">今日</span>
        <span @click="setWeek">本周</span>
        <span @click="setMonth">本月</span>
        <span @click="setYear">本年</span>
        <el-date-picker
          v-model="date"
          type="daterange"
          size="mini"
          range-separator="—"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          style="width:260px"
          value-format="yyyy-mm-dd"
        />
      </div>
    </div>
    <div>
      <!-- gutter 属性来指定每一栏之间的间隔，默认间隔为 0 -->
      <el-row :gutter="10">
        <el-col :span="16">
          <div ref="charts" class="charts" />
        </el-col>
        <el-col :span="8">
          <div class="r_list">
            <ul>
              <div class="list_title">门店{{ title }}排名</div>
              <li>
                <span class="num">1</span>
                <span>肯德基</span>
                <span class="sale">9231</span>
              </li>
              <li>
                <span class="num">2</span>
                <span>肯德基</span>
                <span class="sale">1234</span>
              </li>
              <li>
                <span class="num">3</span>
                <span>肯德基</span>
                <span class="sale">122</span>
              </li>
              <li>
                <span class="num">4</span>
                <span>肯德基</span>
                <span class="sale">123</span>
              </li>
              <li>
                <span class="num">5</span>
                <span>肯德基</span>
                <span class="sale">123</span>
              </li>
              <li>
                <span class="num">6</span>
                <span>肯德基</span>
                <span class="sale">123</span>
              </li>
              <li>
                <span class="num">7</span>
                <span>肯德基</span>
                <span class="sale">121</span>
              </li>
              <li>
                <span class="num">8</span>
                <span>肯德基</span>
                <span class="sale">110</span>
              </li>
              <li>
                <span class="num">9</span>
                <span>肯德基</span>
                <span class="sale">12</span>
              </li>
              <li>
                <span class="num">10</span>
                <span>肯德基</span>
                <span class="sale">5</span>
              </li>
            </ul>
          </div>
        </el-col>
      </el-row>
    </div>
  </el-card>
</template>

<script>
// 引入echarts
import * as echarts from 'echarts'
import dayjs from 'dayjs'
export default {
  name: '',
  data() {
    return {
      activeName: 'sale',
      date: [], // 日期
      mycharts: null
    }
  },
  computed: {
    title() {
      return this.activeName === 'sale' ? '销售额' : '访问量'
    }
  },
  watch: {
    title() {
      this.mycharts.setOption({
        title: { text: this.title }
      })
    }
  },
  mounted() {
    // 初始化echarts实例
    this.mycharts = echarts.init(this.$refs.charts)
    // 配置数据
    this.mycharts.setOption({
      title: {
        text: '销售额'
      },
      tooltip: {
        trigger: 'axis',
        axisPointer: {
          type: 'shadow'
        }
      },
      grid: {
        left: '3%',
        right: '4%',
        bottom: '3%',
        containLabel: true
      },
      xAxis: [
        {
          type: 'category',
          data: ['1月', '2月', '3月', '4月', '5月'],
          axisTick: {
            alignWithLabel: true
          }
        }
      ],
      yAxis: [
        {
          type: 'value'
        }
      ],
      series: [
        {
          name: 'Direct',
          type: 'bar',
          barWidth: '60%',
          data: [23, 312, 312, 312, 32],
          color: 'yellowgreen'
        }
      ]
    })
  },
  methods: {
    setDay() {
      const day = dayjs().format('YYYY-MM-DD')
      const date = new Date()
      console.log(date)
      // console.log(day)// ???
      this.date = [day, day]
    },
    setWeek() {
      const start = dayjs().day(1).format('YYYY-MM-DD')
      const end = dayjs().day(7).format('YYYY-MM-DD')
      this.date = [start, end]
    },
    setMonth() {
      const start = dayjs().startOf('month').format('YYYY-MM-DD')
      const end = dayjs().endOf('month').format('YYYY-MM-DD')
      this.date = [start, end]
    },
    setYear() {
      const start = dayjs().startOf('year').format('YYYY-MM-DD')
      const end = dayjs().endOf('year').format('YYYY-MM-DD')
      this.date = [start, end]
    }
  }
}
</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix {
  position: relative;
  /* 让左右两侧处于同一水平 */
  display: flex;
  justify-content: space-between;
}

.tabs {
  width: 100%;
}

.right {
  position: absolute;
  right: 0px
}

.right span {
  margin-right: 20px;
}

.box-card {
  width: 100%;
}

.charts {
  width: 100%;
  height: 300px;
}

.r_list .list_title {
  font-weight: 700;
}

.r_list .sale {
  float: right;
}

.r_list ul {
  list-style: none;
}

.r_list ul li {
  margin: 10px 0;
}
.num{
  float: left;
  width: 30px;
  height: 16px;
}
</style>
