<template>
  <div>
    <div>
      <el-card style="margin:20px 0">
        <CategorySelect v-show="scene == 0" @getCategoryId="getCategoryId" />
      </el-card>
      <el-card>
        <div v-show="scene == 0">
          <el-button type="primary" icon="el-icon-plus" style="margin:10px 0" @click="addSpu">添加Spu</el-button>
          <el-table :data="records" style="width: 100%" border>
            <el-table-column type="index" label="序号" width="100" align="center" />
            <el-table-column prop="spuName" label="spu名称" width="260" />
            <el-table-column prop="description" label="spu描述" width="260" />
            <el-table-column prop="" label="操作">
              <template slot-scope="{row,$index}">
                <el-tooltip class="item" effect="dark" content="添加" placement="bottom">
                  <el-button size="mini" type="success" icon="el-icon-plus" @click="addSku(row)" />
                </el-tooltip>
                <el-tooltip class="item" effect="dark" content="编辑" placement="bottom">
                  <el-button size="mini" type="warning" icon="el-icon-edit" @click="updateSpu(row)" />
                </el-tooltip>
                <el-tooltip class="item" effect="dark" content="信息" placement="bottom">
                  <el-button size="mini" type="info" icon="el-icon-info" @click="handleInfo(row)" />
                </el-tooltip>
                <el-tooltip class="item" effect="dark" content="删除" placement="bottom">
                  <el-popconfirm title="确定删除吗？" @onConfirm="deleteSpu(row)">
                    <el-button slot="reference" size="mini" type="danger" icon="el-icon-delete" />
                  </el-popconfirm>
                </el-tooltip>
              </template>
            </el-table-column>
          </el-table>
          <!-- 分页器 -->
          <el-pagination
            style="margin-top: 20px; textAlign: center"
            :current-page="page"
            :page-sizes="[3, 5, 7, 9]"
            :page-size="limit"
            layout=" prev, pager, next,jumper,->,sizes,total"
            :total="total"
            @size-change="handleSizeChange"
            @current-change="getSpuList"
          />
        </div>
        <el-dialog :title="`${spu.spuName}的sku列表`" :visible.sync="dialogTableVisible" :before-close="closeInfo">
          <!-- table展示sku列表-->
          <el-table v-loading="loading" :data="skuList" style="width: 100%" border>
            <el-table-column prop="skuName" label="名称" width="width" />
            <el-table-column prop="price" label="价格" width="width" />
            <el-table-column prop="weight" label="重量" width="width" />
            <el-table-column label="默认图片" width="width">
              <template slot-scope="{row,$index}">
                <img :src="row.skuDefaultImg" alt="" style="width:100px;height:100px;">
              </template>
            </el-table-column>
          </el-table>
        </el-dialog>
        <SpuForm v-show="scene == 1" ref="spu" @changeScene="changeScene" />
        <SkuForm v-show="scene == 2" ref="sku" @changeScenes="changeScenes" />
      </el-card>
    </div>

  </div>
</template>

<script>
import SkuForm from './SkuForm'
import SpuForm from './SpuForm'
export default {
  name: 'Spu',
  components: {
    SpuForm,
    SkuForm
  },
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      page: 1,
      total: 21,
      limit: 9,
      records: [], // spu列表的数据
      scene: 0, // 0代表展示SPU列表数据   1 添加SPU|修改SPU   2 添加SKU
      dialogTableVisible: false,
      spu: {},
      skuList: [],
      loading: true
    }
  },
  methods: {
    getCategoryId(category1Id, category2Id, category3Id) {
      console.log(category1Id, category2Id, category3Id)
      this.category1Id = category1Id
      this.category2Id = category2Id
      this.category3Id = category3Id
      this.getSpuList()
    },
    async getSpuList(pager = 1) {
      this.page = pager
      const { page, limit, category3Id } = this
      const result = await this.$API.spu.reqSpuList(page, limit, category3Id)
      console.log('reqSpuList', result)
      if (result.code === 200) {
        this.records = result.data.records
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getSpuList()
    },
    // 点击添加SPU按钮
    addSpu() {
      this.$refs.spu.addSpuData(this.category3Id)
      this.scene = 1
    },
    // 编辑spu
    updateSpu(row) {
      this.scene = 1
      // 获取子组件,并传递参数
      this.$refs.spu.initSpu(row)
    },
    // 触发自定义事件-切换场景
    changeScene({ scene, flag }) {
      this.scene = scene
      if (flag === '修改') {
        this.getSpuList(this.page)
      } else {
        this.getSpuList()
      }
    },
    async deleteSpu(row) {
      // this.records.splice(index, 1)
      const result = await this.$API.spu.reqDeleteSpu(row.id)
      if (result.code === 200) {
        this.$message({ type: 'success', message: '删除成功' })
        // 代表SPU个数大于1删除的时候停留在当前页，如果SPU个数小于1 回到上一页
        this.getSpuList(this.records.length > 1 ? this.page : this.page - 1)
      }
    },
    // 添加sku(四个按钮中的第一个)
    addSku(row) {
      this.scene = 2
      // 父组件调用子组件的方法，让子组件发请求------三个请求
      this.$refs.sku.getData(this.category1Id, this.category2Id, row)
    },
    changeScenes(scene) {
      this.scene = scene
    },
    async handleInfo(spu) {
      // 点击这个按钮的时候，对话框可见的
      this.dialogTableVisible = true
      // 保存spu信息
      this.spu = spu
      // 获取sku列表的数据进行展示
      const result = await this.$API.spu.reqSkuList(spu.id)
      if (result.code === 200) {
        this.skuList = result.data
        // loading隐藏
        this.loading = false
      }
    },
    closeInfo(done) {
      this.loading = true
      this.skuList = []
      done()
    }
  }
}
</script>

<style>

</style>
