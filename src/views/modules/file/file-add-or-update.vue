<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="文件名" prop="name">
      <el-input v-model="dataForm.name" placeholder="文件名"></el-input>
    </el-form-item>
    <el-form-item label="文件存储路径" prop="path">
      <el-input v-model="dataForm.path" placeholder="文件存储路径"></el-input>
    </el-form-item>
    <el-form-item label="文件类型ID" prop="fileTypeId">
      <el-input v-model="dataForm.fileTypeId" placeholder="文件类型ID"></el-input>
    </el-form-item>
    <el-form-item label="文件大小，单位kb" prop="size">
      <el-input v-model="dataForm.size" placeholder="文件大小，单位kb"></el-input>
    </el-form-item>
    <el-form-item label="描述" prop="depiction">
      <el-input v-model="dataForm.depiction" placeholder="描述"></el-input>
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
          userId: '',
          name: '',
          path: '',
          fileTypeId: '',
          size: '',
          depiction: '',
          isDelete: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '文件名不能为空', trigger: 'blur' }
          ],
          path: [
            { required: true, message: '文件存储路径不能为空', trigger: 'blur' }
          ],
          fileTypeId: [
            { required: true, message: '文件类型ID不能为空', trigger: 'blur' }
          ],
          size: [
            { required: true, message: '文件大小，单位kb不能为空', trigger: 'blur' }
          ],
          depiction: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/file/file/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.file.userId
                this.dataForm.name = data.file.name
                this.dataForm.path = data.file.path
                this.dataForm.fileTypeId = data.file.fileTypeId
                this.dataForm.size = data.file.size
                this.dataForm.depiction = data.file.depiction
                this.dataForm.isDelete = data.file.isDelete
                this.dataForm.createTime = data.file.createTime
                this.dataForm.updateTime = data.file.updateTime
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
              url: this.$http.adornUrl(`/file/file/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'name': this.dataForm.name,
                'path': this.dataForm.path,
                'fileTypeId': this.dataForm.fileTypeId,
                'size': this.dataForm.size,
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
