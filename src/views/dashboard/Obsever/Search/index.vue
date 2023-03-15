<template>
  <div>
    <el-card>
      <div slot="header" class="clearfix">
        <div class="search-header">
          <span>线上热门搜索</span>
          <el-dropdown>
            <span class="el-dropdown-link">
              <i class="el-icon-more" />
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>黄金糕</el-dropdown-item>
              <el-dropdown-item>狮子头</el-dropdown-item>
              <el-dropdown-item>螺蛳粉</el-dropdown-item>
              <el-dropdown-item>双皮奶</el-dropdown-item>
              <el-dropdown-item>蚵仔煎</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </div>
      <div>
        <el-row :gutter="6">
          <el-col :span="12">
            <lineCharts :number="1234" :change="12.2" title="搜索用户数" />
          </el-col>
          <el-col :span="12">
            <lineCharts :number="2334" :change="14.1" title="活跃用户数" />
          </el-col>
        </el-row>
        <!-- 表格 -->
        <el-table :data="tableData" style="width: 100%;margin-top: 12px;" border>
          <el-table-column label="排名" width="60" />
          <el-table-column label="搜索关键字" />
          <el-table-column label="用户数" sortable />
          <el-table-column label="周涨幅" sortable />
        </el-table>
        <el-pagination layout="prev, pager, next" :total="50" class="pagination" />
      </div>
    </el-card>
  </div>
</template>

<script>
import lineCharts from './lineCharts'
export default {
  name: '',
  components: {
    lineCharts
  }
}
</script>

<style scoped>
.search-header{
  display: flex;
  justify-content: space-between;
}
.pagination{
  float: right;
}
</style>
