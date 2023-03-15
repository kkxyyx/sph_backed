<template>
  <div>
    <el-row :gutter="10" style="margin-top:12px">
      <el-col :span="12">
        <Search />
      </el-col>
      <el-col :span="12">
        <Category />
      </el-col>
    </el-row>
  </div>
</template>

<script>
import Category from '../Obsever/Category'
import Search from '../Obsever/Search'
export default {
  name: '',
  components: {
    Search,
    Category
  }
}
</script>

<style>

</style>
