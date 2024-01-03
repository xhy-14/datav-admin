<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="已生成图表id" prop="generatedChartId">
      <el-input v-model="dataForm.generatedChartId" placeholder="已生成图表id"></el-input>
    </el-form-item>
    <el-form-item label="参数类型id" prop="parameterTypeId">
      <el-input v-model="dataForm.parameterTypeId" placeholder="参数类型id"></el-input>
    </el-form-item>
    <el-form-item label="参数内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="参数内容"></el-input>
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
          generatedChartId: '',
          parameterTypeId: '',
          content: '',
          isDelete: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          generatedChartId: [
            { required: true, message: '已生成图表id不能为空', trigger: 'blur' }
          ],
          parameterTypeId: [
            { required: true, message: '参数类型id不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '参数内容不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/table/generatedchartparameterrelation/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.generatedChartId = data.generatedChartParameterRelation.generatedChartId
                this.dataForm.parameterTypeId = data.generatedChartParameterRelation.parameterTypeId
                this.dataForm.content = data.generatedChartParameterRelation.content
                this.dataForm.isDelete = data.generatedChartParameterRelation.isDelete
                this.dataForm.createTime = data.generatedChartParameterRelation.createTime
                this.dataForm.updateTime = data.generatedChartParameterRelation.updateTime
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
              url: this.$http.adornUrl(`/table/generatedchartparameterrelation/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'generatedChartId': this.dataForm.generatedChartId,
                'parameterTypeId': this.dataForm.parameterTypeId,
                'content': this.dataForm.content,
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
