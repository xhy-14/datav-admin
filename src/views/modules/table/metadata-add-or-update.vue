<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="文件id" prop="fileId">
      <el-input v-model="dataForm.fileId" placeholder="文件id"></el-input>
    </el-form-item>
    <el-form-item label="数据集文件" prop="dataFileId">
      <el-input v-model="dataForm.dataFileId" placeholder="数据集文件"></el-input>
    </el-form-item>
    <el-form-item label="名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="描述" prop="depiction">
      <el-input v-model="dataForm.depiction" placeholder="描述"></el-input>
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
          userId: '',
          fileId: '',
          dataFileId: '',
          name: '',
          depiction: '',
          isDelete: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          fileId: [
            { required: true, message: '文件id不能为空', trigger: 'blur' }
          ],
          dataFileId: [
            { required: true, message: '数据集文件不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          depiction: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/table/metadata/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.metadata.userId
                this.dataForm.fileId = data.metadata.fileId
                this.dataForm.dataFileId = data.metadata.dataFileId
                this.dataForm.name = data.metadata.name
                this.dataForm.depiction = data.metadata.depiction
                this.dataForm.isDelete = data.metadata.isDelete
                this.dataForm.createTime = data.metadata.createTime
                this.dataForm.updateTime = data.metadata.updateTime
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
              url: this.$http.adornUrl(`/table/metadata/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'fileId': this.dataForm.fileId,
                'dataFileId': this.dataForm.dataFileId,
                'name': this.dataForm.name,
                'depiction': this.dataForm.depiction,
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
