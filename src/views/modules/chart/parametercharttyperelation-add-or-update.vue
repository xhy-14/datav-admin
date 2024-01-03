<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="类型id" prop="typeId">
      <el-input v-model="dataForm.typeId" placeholder="类型id"></el-input>
    </el-form-item>
    <el-form-item label="参数id" prop="parameterId">
      <el-input v-model="dataForm.parameterId" placeholder="参数id"></el-input>
    </el-form-item>
    <el-form-item label="默认值" prop="defaultValue">
      <el-input v-model="dataForm.defaultValue" placeholder="默认值"></el-input>
    </el-form-item>
    <el-form-item label="是否删除" prop="isDelete">
      <el-input v-model="dataForm.isDelete" placeholder="是否删除"></el-input>
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
          typeId: '',
          parameterId: '',
          defaultValue: '',
          isDelete: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          typeId: [
            { required: true, message: '类型id不能为空', trigger: 'blur' }
          ],
          parameterId: [
            { required: true, message: '参数id不能为空', trigger: 'blur' }
          ],
          defaultValue: [
            { required: true, message: '默认值不能为空', trigger: 'blur' }
          ],
          isDelete: [
            { required: true, message: '是否删除不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/chart/parametercharttyperelation/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.typeId = data.parameterChartTypeRelation.typeId
                this.dataForm.parameterId = data.parameterChartTypeRelation.parameterId
                this.dataForm.defaultValue = data.parameterChartTypeRelation.defaultValue
                this.dataForm.isDelete = data.parameterChartTypeRelation.isDelete
                this.dataForm.createTime = data.parameterChartTypeRelation.createTime
                this.dataForm.updateTime = data.parameterChartTypeRelation.updateTime
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
              url: this.$http.adornUrl(`/chart/parametercharttyperelation/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'typeId': this.dataForm.typeId,
                'parameterId': this.dataForm.parameterId,
                'defaultValue': this.dataForm.defaultValue,
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
