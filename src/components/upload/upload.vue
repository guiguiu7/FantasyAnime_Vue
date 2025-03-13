<!--<script setup lang="ts">-->
<!--import {computed, reactive, ref} from "vue";-->
<!--import { UploadProps, UploadUserFile, ElMessage } from "element-plus";-->
<!--import { getToken } from "@/utils/cache";-->
<!--import axios from "axios";-->

<!--const uploadFile = ref('');-->
<!--const url = ref('');-->
<!--let fileList = computed(() => {-->
<!--  if (url != null){-->
<!--    return [{uid: url, name: url}]-->
<!--  }-->
<!--})-->

<!--// 上传视频-->

<!--const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {-->
<!--  ElMessage.warning(-->
<!--  )-->
<!--}-->
<!--const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {-->
<!--  ElMessage({-->
<!--    type: 'warning',-->
<!--    message: '已移除文件',-->
<!--  })-->
<!--}-->
<!--const handleSuccess: UploadProps['onSuccess'] = (response, uploadFile, uploadFiles) => {-->
<!--  console.log(uploadFile)-->
<!--}-->
<!--function upload(param: { file: string | Blob; }) {-->
<!--  const formData = new FormData()-->
<!--  formData.append('file', param.file)-->
<!--  formData.append('animeId', dataForm.animeId)-->
<!--  formData.append('id', dataForm.id)-->
<!--  axios.post('anime/animeEpisode/upload', formData, {headers: {'Token': getToken()}}).then((res) => {-->
<!--    if (res.data){-->
<!--      ElMessage({-->
<!--        type: 'success',-->
<!--        message: '文件上传成功'-->
<!--      })-->
<!--    } else {-->
<!--      throw new Error("文件上传失败")-->
<!--    }-->
<!--  }).catch((err) => {-->
<!--    ElMessage({-->
<!--      type: 'success',-->
<!--      message: err-->
<!--    })-->
<!--  })-->
<!--}-->


<!--const cleanFiles = () => {-->
<!--  uploadFile.value.clearFiles()-->
<!--}-->
<!--</script>-->

<!--<template>-->
<!--  <el-upload-->
<!--      ref="uploadFile"-->
<!--      v-model:file-list="fileList"-->
<!--      class="update"-->
<!--      action="#"-->
<!--      :http-request="upload"-->
<!--      :on-remove="handleRemove"-->
<!--      :on-success="handleSuccess"-->
<!--      :limit="1"-->
<!--      :on-exceed="handleExceed"-->
<!--  >-->
<!--    <el-button type="primary">上传视频</el-button>-->
<!--    <template #tip>-->
<!--      <div class="el-upload__tip">-->
<!--        上传本集资源-->
<!--      </div>-->
<!--    </template>-->
<!--  </el-upload>-->
<!--</template>-->

<!--<style scoped lang="less">-->

<!--</style>-->
