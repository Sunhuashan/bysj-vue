<template>
  <div style="margin: 12px 12px 0 0">
    <el-upload
      ref="videoUpload"
      action="http://localhost:8443/api/admin/save_image"
      :before-upload="beforeUpload"
      :on-success="success"
      :before-remove="beforeRemove"
      :limit="1"
      :on-exceed="handleExceed"
      :file-list="fileList">
      <el-button size="small" type="primary">点击上传视频</el-button>
      <div slot="tip">只能上传MP4文件,且不超过300MB</div>
    </el-upload>
  </div>
</template>
<script>
export default {
  name: 'VideoUpload',
  data () {
    return {
      fileList: [],
      url: '',
      // 改变量用于判断文件是否符合要求，避免不符合要求的文件触发 beforeRemove
      suitable: true
    }
  },
  methods: {
    beforeUpload (file) {
      var fileType = file.name.substring(file.name.lastIndexOf('.') + 1)
      var size = file.size / 1024 / 1024
      if (fileType !== 'mp4') {
        this.$message.warning('仅能上传 mp4类型的文件')
        this.suitable = false
        return false
      } else if (size > 300) {
        this.$message.warning('文件限制大小:300MB , 当前文件大小:' + size.toFixed(1) + 'MB')
        this.suitable = false
        return false
      }
      this.suitable = true
      return true
    },
    handleExceed (files, fileList) {
      this.$message.warning(`限制上传 1 个文件`)
    },
    beforeRemove (file, fileList) {
      // 不符合要求的文件移除时，不触发下面操作
      if (this.suitable) {
        this.url = ''
        this.$emit('onRemove')
        return this.$confirm(`确定移除 ${file.name}?`)
      }
    },
    success (response, file, fileList) {
      if (response.code === 200) {
        this.url = response.data
        this.$emit('onUpload')
        this.$message.success('文件上传成功')
      } else {
        this.$message.warning(response.message)
      }
    }
  }
}
</script>
