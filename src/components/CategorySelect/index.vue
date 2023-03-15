<template>
  <div>
    <!-- inline代表行内表单 -->
    <el-form :inline="true" :model="cForm" class="demo-form-inline">
      <el-form-item label="一级分类">
        <el-select v-model="cForm.category1Id" placeholder="请选择" @change="handler1">
          <el-option v-for="c1 in list1" :key="c1.id" :label="c1.name" :value="c1.id" />
        </el-select>
      </el-form-item>
      <el-form-item label="二级分类">
        <el-select v-model="cForm.category2Id" placeholder="请选择" @change="handler2">
          <el-option v-for="c2 in list2" :key="c2.id" :label="c2.name" :value="c2.id" />
        </el-select>
      </el-form-item>
      <el-form-item label="三级分类">
        <el-select v-model="cForm.category3Id" placeholder="请选择" @change="handler3">
          <el-option v-for="c3 in list3" :key="c3.id" :label="c3.name" :value="c3.id" />
        </el-select>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'CategorySelect',
  data() {
    return {
      list1: [],
      list2: [],
      list3: [],
      cForm: {
        category1Id: '',
        category2Id: '',
        category3Id: ''
      }
    }
  },
  mounted() {
    this.getCategoryList1()
  },
  methods: {
    async getCategoryList1() {
      const result = await this.$API.attr.reqCategory1List()
      if (result.code === 200) {
        this.list1 = result.data
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    // 一级分类的select事件回调
    async handler1() {
      // 有优化方案吗？？？
      this.list3 = []
      this.cForm.category2Id = ''
      this.cForm.category3Id = ''
      const { category1Id } = this.cForm
      const result = await this.$API.attr.reqCategory2List(category1Id)
      console.log('reqCategory2List', result)
      if (result.code === 200) {
        this.list2 = result.data
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    async handler2() {
      this.cForm.category3Id = ''
      const { category2Id } = this.cForm
      const result = await this.$API.attr.reqCategory3List(category2Id)
      console.log('reqCategory3List', result)
      if (result.code === 200) {
        this.list3 = result.data
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    handler3() {
      const { category1Id, category2Id, category3Id } = this.cForm
      this.$emit('getCategoryId', category1Id, category2Id, category3Id)
    }
  }
}
</script>

<style>

</style>
