<template>
  <div>
    <el-card v-show="isShowTable==true" style="margin:20px 0">
      <CategorySelect @getCategoryId="getCategoryId" />
    </el-card>
    <div v-show="isShowTable">
      <el-card>
        <el-button
          :disabled="!category3Id"
          type="primary"
          icon="el-icon-plus"
          style="margin-bottom:20px"
          @click="addAttr"
        >添加属性</el-button>
        <!-- 三级列表下的表格 -->
        <el-table :data="attrList" style="width: 100%" border>
          <el-table-column label="序号" type="index" width="180" align="center" />
          <el-table-column prop="attrName" label="属性名称" width="180" />
          <el-table-column prop="" label="属性值列表">
            <template slot-scope="{row}">
              <el-tag v-for="attrValue in row.attrValueList" :key="attrValue.id" style="margin:2px 10px">
                {{ attrValue.valueName }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="210">
            <template slot-scope="{row}">
              <el-button size="mini" type="warning" icon="el-icon-edit" @click="updateAttr(row)">编辑</el-button>
              <el-button size="mini" type="danger" icon="el-icon-delete">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
    <div v-show="!isShowTable">
      <el-form :inline="true" :model="attrInfo">
        <el-form-item label="属性名">
          <el-input v-model="attrInfo.attrName" placeholder="请输入属性名" width="200" />
        </el-form-item>
      </el-form>
      <el-button :disabled="!attrInfo.attrName" icon="el-icon-plus" @click="addAttrValue">添加属性值</el-button>
      <el-button type="primary">取消</el-button>
      <!-- 修改属性表格 -->
      <el-table :data="attrInfo.attrValueList" style="margin:20px">
        <el-table-column label="序号" type="index" width="100" />
        <el-table-column prop="valueName" label="属性值名称" width="600">
          <template slot-scope="{row,$index}">
            <el-input
              v-if="row.flag"
              :ref="$index"
              v-model="row.valueName"
              size="mini"
              @keyup.native.enter="toLook(row)"
              @blur="toLook(row)"
            />
            <span v-else style="display:block" @click="toEdit(row, $index)">{{ row.valueName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="300">
          <template slot-scope="{row, $index}">
            <el-popconfirm :title="`确认删除${row.valueName}?`" @onConfirm="deleteAttrValue($index)">
              <el-button slot="reference" type="danger" size="mini" icon="el-icon-delete">删除</el-button>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
      <el-button type="primary" :disabled="attrInfo.attrValueList.length<1" @click="addOrUpdateAttr">保存</el-button>
      <el-button @click="isShowTable = true">取消</el-button>
    </div>
  </div>
</template>

<script>
import cloneDeep from 'lodash/cloneDeep'
export default {
  name: 'Attr',
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      // 服务器返回的数据
      attrList: [],
      isShowTable: true,
      // 表单收集的数据
      attrInfo: {
        attrName: '', // 属性名
        attrValueList: [ // 属性名中属性值
          // 占位符
          /* {
            attrId: 0, // 属性的id
            valueName: '' // 属性值
          } */
        ],
        categoryId: 0, // category3Id
        categoryLevel: 3
      }
    }
  },
  methods: {
    async getCategoryId(category1Id, category2Id, category3Id) {
      // console.log(category1Id, category2Id, category3Id)
      this.category1Id = category1Id
      this.category2Id = category2Id
      this.category3Id = category3Id
      const result = await this.$API.attr.reqAttrList(category1Id, category2Id, category3Id)
      console.log('reqAttrList', result)
      if (result.code === 200) {
        this.attrList = result.data
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    // 添加属性
    addAttr() {
      // 清空数据 + 收集三级分类ID
      this.attrInfo = {
        attrName: '', // 属性名
        attrValueList: [ // 属性名中属性值
        ],
        categoryId: this.category3Id, // category3Id
        categoryLevel: 3
      }
      this.isShowTable = false
    },
    // 添加属性值
    addAttrValue() {
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id,
        valueName: '',
        // 添加标识符，处于编辑模式
        flag: true
      })
      this.$nextTick(() => {
        this.$refs[this.attrInfo.attrValueList.length - 1].focus()
      })
    },
    // 编辑属性
    updateAttr(row) {
      this.isShowTable = false
      this.attrInfo = cloneDeep(row)
      this.attrInfo.attrValueList.forEach(item => {
        // 添加响应式属性
        this.$set(item, 'flag', false)
      })
    },
    // 显示模式,显示span flag = false
    toLook(row) {
      row.flag = false
      if (row.valueName.trim() === '') {
        this.$message('请正常输入')
        return
      }
      const isRepeat = this.attrInfo.attrValueList.some(item => {
        if (row !== item) { // 需要把自己排除在外
          return item.valueName === row.valueName
        }
      })
      if (isRepeat) {
        this.$message('请不要重复输入')
        return
      }
    },
    // 编辑模式 flag = true
    toEdit(row, index) {
      row.flag = true
      this.$nextTick(() => {
        this.$refs[index].focus() // index非常量，不能this.$refs.index
      })
    },
    deleteAttrValue(index) {
      this.attrInfo.attrValueList.splice(index, 1)
    },
    // 保存修改
    async addOrUpdateAttr() {
      this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter(item => {
        if (item.valueName !== '') {
          // 删除flag属性
          delete item.flag
          return true
        }
      })
      const result = await this.$API.attr.reqAddOrUpdateAttr(this.attrInfo)
      console.log('reqAddOrUpdateAttr', result)
      if (result.code === 200) {
        this.isShowTable = true
        this.$message.success('保存成功')
        // 发请求，请求更新后的列表
        const { category1Id, category2Id, category3Id } = this
        this.getCategoryId(category1Id, category2Id, category3Id)
      } else {
        this.$message('保存失败')
        return Promise.reject(new Error('fail'))
      }
    }
  }
}
</script>

<style>

</style>
