<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="套餐名称，唯一且不能为空" prop="name">
      <el-input v-model="dataForm.name" placeholder="套餐名称，唯一且不能为空"></el-input>
    </el-form-item>
    <el-form-item label="价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格"></el-input>
    </el-form-item>
    <el-form-item label="时长" prop="term">
      <el-input v-model="dataForm.term" placeholder="时长"></el-input>
    </el-form-item>
    <el-form-item label="折扣" prop="discount">
      <el-input v-model="dataForm.discount" placeholder="折扣"></el-input>
    </el-form-item>
    <el-form-item label="是否删除，默认为0表示未删除" prop="isDelete">
      <el-input v-model="dataForm.isDelete" placeholder="是否删除，默认为0表示未删除"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          price: '',
          term: '',
          discount: '',
          isDelete: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          name: [
            { required: true, message: '套餐名称，唯一且不能为空不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
          ],
          term: [
            { required: true, message: '时长不能为空', trigger: 'blur' }
          ],
          discount: [
            { required: true, message: '折扣不能为空', trigger: 'blur' }
          ],
          isDelete: [
            { required: true, message: '是否删除，默认为0表示未删除不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/menber/combo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.combo.name
                this.dataForm.price = data.combo.price
                this.dataForm.term = data.combo.term
                this.dataForm.discount = data.combo.discount
                this.dataForm.isDelete = data.combo.isDelete
                this.dataForm.createTime = data.combo.createTime
                this.dataForm.updateTime = data.combo.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/menber/combo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'price': this.dataForm.price,
                'term': this.dataForm.term,
                'discount': this.dataForm.discount,
                'isDelete': this.dataForm.isDelete,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
