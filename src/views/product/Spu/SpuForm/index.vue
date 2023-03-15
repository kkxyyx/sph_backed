<template>
  <div>
    <el-form :model="spu">
      <el-form-item label="SPU名称">
        <el-input v-model="spu.spuName" placeholder="SPU名称" />
      </el-form-item>
      <el-form-item label="品牌">
        <el-select v-model="spu.tmId" placeholder="请选择品牌" value="">
          <el-option
            v-for="(tm,index) in tradeMarkList"
            :key="tm.id"
            :label="tm.tmName"
            :value="tm.id"
          />
          <el-option />
        </el-select>
      </el-form-item>
      <el-form-item label="SPU描述" prop="">
        <el-input v-model="spu.description" type="textarea" rows="4" placeholder="SPU描述" />
      </el-form-item>
      <el-form-item label="SPU图片">
        <el-upload
          action="/dev-api/admin/product/fileUpload"
          list-type="picture-card"
          :file-list="spuImageList"
          :on-preview="handlePictureCardPreview"
          :on-success="handleSuccess"
          :on-remove="handleRemove"
        >
          <i class="el-icon-plus" />
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>
      </el-form-item>
      <el-form-item :inline="true" label="销售属性">
        <el-select v-model="attrIdAndAttrName" :placeholder="`有${unSelectSaleAttr.length}个未选择`">
          <el-option v-for="(unSelect,index) in unSelectSaleAttr" :key="unSelect.id" :label="unSelect.name" :value="`${unSelect.id}:${unSelect.name}`" />
        </el-select>
        <el-button icon="el-icon-plus" :disabled="!attrIdAndAttrName" @click="addSaleAttr">添加销售属性</el-button>
      </el-form-item>
      <el-form-item>
        <!-- 表格 -->
        <el-table :data="spu.spuSaleAttrList" style="width: 100%" border>
          <el-table-column type="index" label="序号" width="100" align="center" />
          <el-table-column prop="saleAttrName" label="属性名" width="260" />
          <el-table-column prop="prop" label="属性值列表" width="260">
            <template slot-scope="{row,$index}">
              <el-tag v-for="(tag,index) in row.spuSaleAttrValueList" :key="tag.id" closable @close="row.spuSaleAttrValueList.splice(index, 1)">
                {{ tag.saleAttrValueName }}
              </el-tag>
              <el-input
                v-if="row.inputVisible"
                ref="saveTagInput"
                v-model="row.inputValue"
                autofocus
                class="input-new-tag"
                size="small"
                @keyup.enter.native="$event.target.blur"
                @blur="handleInputConfirm(row)"
              />
              <el-button v-else class="button-new-tag" size="small" @click="addSaleAttrValue(row)">添加</el-button>
            </template>
          </el-table-column>
          <el-table-column property="" label="操作">
            <template slot-scope="{row,$index}">
              <el-button size="mini" type="danger" icon="el-icon-delete" @click="spu.spuSaleAttrList.splice($index,1)" />
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-button size="mini" type="primary" @click="addOrUpdateSpu">保存</el-button>
        <el-button size="mini" @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: '',
  data() {
    return {
      dialogImageUrl: '',
      dialogVisible: false,
      // 存储spu信息
      spu: {
        'category3Id': 0,
        'tmId': 0,
        'description': '',
        'spuName': '',
        'spuImageList': [
          {
            'id': 0,
            'imgName': '',
            'imgUrl': '',
            'spuId': 0
          }
        ],
        'spuSaleAttrList': [
          {
            'baseSaleAttrId': 0,
            'id': 0,
            'saleAttrName': '',
            'spuId': 0,
            'spuSaleAttrValueList': [
              {
                'baseSaleAttrId': 0,
                'id': 0,
                'isChecked': '',
                'saleAttrName': '',
                'saleAttrValueName': '',
                'spuId': 0
              }
            ]
          }
        ]
      },
      tradeMarkList: [], // 品牌的信息
      spuImageList: [],
      saleAttrList: [],
      attrIdAndAttrName: '' // 收集未选择的销售属性的id
    }
  },
  computed: {
    unSelectSaleAttr() {
      const result = this.saleAttrList.filter(allItem => {
        // every数组的方法，会返回一个布尔值
        // 当遍历到某一个值不满足条件时，函数立即返回false，不再继续遍历
        return this.spu.spuSaleAttrList.every(item => {
          return item.saleAttrName !== allItem.name
        })
      })
      // 必须返回
      return result
    }
  },
  methods: {
    // 删除图片
    handleRemove(file, fileList) {
      console.log(file, fileList)
      this.spuImageList = fileList
    },
    // 添加图片成功
    handleSuccess(response, file, fileList) {
      this.spuImageList = fileList
    },
    // 图片预览
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url
      this.dialogVisible = true
    },
    // 初始化页面
    async initSpu(spu) {
      // 获取SPU信息的数据
      const spuResult = await this.$API.spu.reqSpu(spu.id)
      if (spuResult.code === 200) {
        this.spu = spuResult.data
      }
      // 获取品牌的信息
      const tradeMarkResult = await this.$API.spu.reqTradeMarkList()
      if (tradeMarkResult.code === 200) {
        this.tradeMarkList = tradeMarkResult.data
      }
      // 获取spu图片的数据
      const spuImageResult = await this.$API.spu.reqSpuImageList(spu.id)
      if (spuImageResult.code === 200) {
        const listArr = spuImageResult.data
        // 由于照片墙显示图片的数据需要 数组，数组里面的元素需要有name与url字段
        // 需要把服务器返回的数据进行修改
        listArr.forEach(item => {
          item.name = item.imgName
          item.url = item.imgUrl
        })
        // 把整理好的数据赋值给
        this.spuImageList = listArr
      }
      // 获取平台全部的销售属性
      const saleResult = await this.$API.spu.reqBaseSaleAttrList()
      if (saleResult.code === 200) {
        this.saleAttrList = saleResult.data
      }
    },
    // 点击 添加销售属性 按钮
    addSaleAttr() {
      const [baseSaleAttrId, saleAttrName] = this.attrIdAndAttrName.split(':')
      const newSaleAttr = {
        baseSaleAttrId,
        saleAttrName,
        spuSaleAttrValueList: []
      }
      this.spu.spuSaleAttrList.push(newSaleAttr)
      // 清空数据
      this.attrIdAndAttrName = ''
    },
    // 添加 按钮的回调
    addSaleAttrValue(row) {
      console.log('row', row)
      this.$set(row, 'inputVisible', true)
      this.$set(row, 'inputValue', '')
      // ???
      this.$nextTick(() => {
        this.$refs.saveTagInput.focus()
      })
    },
    // el-input失却焦点的事件
    handleInputConfirm(row) {
      // console.log('handleInputConfirm-row', row)
      const { baseSaleAttrId, inputValue } = row
      // 1.不能输入重复值
      // 一个元素满足条件，则表达式返回true , 剩余的元素不会再执行检测
      const result = row.spuSaleAttrValueList.some(item => {
        return inputValue === item.saleAttrValueName
      })
      if (result) return this.$message.warning('不要输入重复值')
      // 2.不能输入空值
      if (inputValue.trim() === '') {
        this.$refs.saveTagInput.focus()
        return this.$message.warning('不要输入空值')
      }

      const newSaleAttr = { baseSaleAttrId, saleAttrValueName: inputValue }
      if (inputValue.trim()) {
        row.spuSaleAttrValueList.push(newSaleAttr)
      }
      row.inputVisible = false
    },
    // 保存按钮的回调
    async addOrUpdateSpu() {
      this.spu.spuImageList = this.spuImageList.map(item => {
        return {
          imgName: item.name,
          imgUrl: (item.response && item.response.data.url) || item.url
        }
      })
      const result = await this.$API.spu.reqAddOrUpdateSpu(this.spu)
      console.log('reqAddOrUpdateSpu', result)
      if (result.code === 200) {
        this.$message.success('保存成功')
        this.$emit('changeScene', {
          scene: 0,
          flag: this.spu.id ? '修改' : '添加'
        })
      }
      // 清除数据
      console.log('this._data', this._data, 'this.$options.data', this.$options.data)
      Object.assign(this._data, this.$options.data)
    },
    async addSpuData(category3Id) {
      // 添加SPU的时候收集三级分类的id
      this.spu.category3Id = category3Id
      // 获取品牌的信息
      const tradeMarkResult = await this.$API.spu.reqTradeMarkList()
      if (tradeMarkResult.code === 200) {
        this.tradeMarkList = tradeMarkResult.data
      }
      // 获取平台全部的销售属性
      const saleResult = await this.$API.spu.reqBaseSaleAttrList()
      if (saleResult.code === 200) {
        this.saleAttrList = saleResult.data
      }
    },
    // 点击取消按钮
    cancel() {
      this.$emit('changeScene', { scene: 0 })
      // 清除数据
      console.log('this._data', this._data, 'this.$options.data', this.$options.data)
      Object.assign(this._data, this.$options.data)
    }
  }
}
</script>

<style scoped>
.el-tag+.el-tag {
  margin-left: 10px;
}

.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}

.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;
}
</style>
