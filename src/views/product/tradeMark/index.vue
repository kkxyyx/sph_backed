<template>
  <div>
    <el-button type="primary" icon="el-icon-plus" style="margin:10px 0" @click="addTradeMark">添加</el-button>
    <el-table :data="list" border style="width: 100%">
      <el-table-column type="index" label="序号" width="180" align="center" />
      <el-table-column prop="tmName" label="品牌名称" width="180" />
      <el-table-column prop="logoUrl" label="品牌logo">
        <template slot-scope="{row, $index}">
          <img :src="row.logoUrl" style="width:100px;height:100px">
        </template>
      </el-table-column>

      <el-table-column prop="edit" label="操作">
        <template slot-scope="{row}">
          <el-button type="warning" icon="el-icon-edit" size="mini" @click="updateTradeMark(row)">修改</el-button>
          <el-button type="danger" icon="el-icon-delete" size="mini" @click="deleteTradeMark(row)">删除</el-button>
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
      @current-change="getTradeMarkList"
      @size-change="handleSizeChange"
    />

    <!-- 对话框 -->
    <el-dialog :title="tmForm.id?'修改品牌':'添加品牌'" :visible.sync="dialogFormVisible">
      <el-form ref="ruleForm" :model="tmForm" :rules="rules">
        <el-form-item label="品牌名称" prop="tmName" label-width="100px" style="width:85%">
          <el-input v-model="tmForm.tmName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="品牌logo" prop="logoUrl" label-width="100px">
          <!-- 上传图片 -->
          <el-upload
            class="avatar-uploader"
            action="/dev-api/admin/product/fileUpload"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img v-if="tmForm.logoUrl" :src="tmForm.logoUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon" />
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addOrUpdateTradeMark">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>

export default {
  name: 'TradeMark',
  data() {
    // 自定义规则
    var validateTmName = (rule, value, callback) => {
      if (value.length < 2 || value.length > 7) {
        callback(new Error('品牌名称必须2-7位'))
      } else {
        callback()// 放行
      }
    }
    return {
      page: 1,
      limit: 3,
      list: [],
      total: '',
      // 显示对话框的属性
      dialogFormVisible: false,
      tmForm: {
        logoUrl: '',
        tmName: ''
      },
      // 表单验证规则
      rules: {
        tmName: [
          { required: true, message: '请输入品牌名称', trigger: 'blur' },
          // { min: 2, max: 7, message: '长度在 2 到 7 个字符', trigger: 'change' },
          { validator: validateTmName, trigger: 'change' }// 自定义校验规则
        ],
        logoUrl: [
          { required: true, message: '请选择品牌图片' }
        ]
      }
    }
  },
  mounted() {
    this.getTradeMarkList()
  },
  methods: {
    async getTradeMarkList(pager = 1) {
      // pager 当前的页码，默认为 1
      this.page = pager
      const { page, limit } = this
      const result = await this.$API.tradeMark.reqTradeMarkList(page, limit)
      console.log('reqTradeMarkList', result)
      if (result.code === 200) {
        this.list = result.data.records
        this.total = result.data.total
      } else {
        return Promise.reject(new Error('fail'))
      }
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getTradeMarkList()
    },
    // 点击添加按钮
    addTradeMark() {
      // 每次弹出对话框时 清除数据
      this.tmForm = { logoUrl: '', tmName: '' }
      // 显示对话框
      this.dialogFormVisible = true
    },
    // 点击修改按钮 修改品牌
    updateTradeMark(row) {
      console.log('row', row)
      // 扩展运算法是不完全深拷贝，在第一层是深拷贝，第二层是浅拷贝
      this.tmForm = { ...row }
      this.dialogFormVisible = true
    },
    // 上传照片成功的回调
    handleAvatarSuccess(res, file) {
      // res是服务器返回的图片数据
      // file是服务器返回的详细数据
      console.log('handleAvatarSuccess', res, file)
      this.tmForm.logoUrl = res.data
      // this.tmForm.logoUrl = URL.createObjectURL(file.raw)
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    },
    // 点击对话框的确定按钮 (添加|修改品牌)
    addOrUpdateTradeMark() {
      this.$refs.ruleForm.validate(async success => {
        if (success) {
          this.dialogFormVisible = false
          const result = await this.$API.tradeMark.reqAddOrUpdateTradeMark(this.tmForm)
          console.log('reqAddOrUpdateTradeMark', result)
          if (result.code === 200) {
            this.$message.success(this.tmForm.id ? '修改品牌成功' : '添加品牌成功')
            // 对象形式写法
            /*  this.$message({
              message: this.tmForm.id ? '修改品牌成功' : '添加品牌成功',
              type: 'success' }) */
            this.getTradeMarkList(this.tmForm.id ? this.page : 1)
          }
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    // 删除品牌
    deleteTradeMark(row) {
      this.$confirm(`确认删除${row.tmName}吗?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async() => {
        const result = await this.$API.tradeMark.reqDeleteTradeMark(row.id)
        console.log('reqDeleteTradeMark', result)
        if (result.code === 200) {
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.getTradeMarkList(this.list.length < 1 ? this.page - 1 : this.page)
        } else {
          return Promise.reject(new Error('fail'))
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  }
}
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
