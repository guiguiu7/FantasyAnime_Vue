<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="动漫id" prop="animeId">
        <el-input v-model="dataForm.animeId" placeholder="动漫id" :disabled="dataForm.animeId !== null"></el-input>
      </el-form-item>
          <el-form-item label="集数" prop="num">
        <el-input v-model="dataForm.num" placeholder="集数"></el-input>
      </el-form-item>
          <el-form-item label="标题名" prop="title">
        <el-input v-model="dataForm.title" placeholder="标题名"></el-input>
      </el-form-item>
          <el-form-item label="描述" prop="description">
        <el-input v-model="dataForm.description" placeholder="描述"></el-input>
      </el-form-item>
      <el-upload
          ref="uploadFile"
          v-model:file-list="fileList"
          class="update"
          action="#"
          :http-request="upload"
          :on-remove="handleRemove"
          :on-success="handleSuccess"
          :limit="1"
          :on-exceed="handleExceed"
      >
        <el-button type="primary">上传视频</el-button>
        <template #tip>
          <div class="el-upload__tip">
            上传本集资源
          </div>
        </template>
      </el-upload>
      </el-form>
    <template #footer>
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">确定</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import {computed, reactive, ref} from "vue";
import baseService from "@/service/baseService";
import { UploadProps, UploadUserFile, ElMessage } from "element-plus";
import { getToken } from "@/utils/cache";
import axios from "axios";
const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();
const uploadFile = ref();

const dataForm = reactive({
  id: '',  animeId: '',  num: '',  title: '',  description: '', url: ''});

const rules = ref({
          animeId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          num: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          title: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          description: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ]
  });

let fileList = computed(() => {
  if (dataForm.url != null){
    return [{uid: dataForm.url, name: dataForm.url}]
  }
})

const props = defineProps({anime:String})

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";
  // 填入动漫id
  dataForm.animeId = "";


  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
    cleanFiles()
  }

  dataForm.animeId = <string>props.anime

  if (id) {
    getInfo(id);
  }
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get("/anime/animeEpisode/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animeEpisode", dataForm).then((res) => {
      ElMessage.success({
        message: '成功',
        duration: 500,
        onClose: () => {
          visible.value = false;
          emit("refreshDataList");
        }
      });
    });
  });
};

// 上传视频--------------------------------------

const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {
  ElMessage.warning(
      "只能上传一个视频"
  )
}
const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {
  ElMessage({
    type: 'warning',
    message: '已移除文件',
  })
}
const handleSuccess: UploadProps['onSuccess'] = (response, uploadFile, uploadFiles) => {
  console.log(uploadFile)
}
function upload(param: { file: string | Blob; }) {
  const formData = new FormData()
  formData.append('file', param.file)
  formData.append('animeId', dataForm.animeId)
  formData.append('id', dataForm.id)
  axios.post('anime/animeEpisode/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
    if (res.data){
      ElMessage({
        type: 'success',
        message: '文件上传成功'
      })
      dataForm.url = res.data
    } else {
      throw new Error("文件上传失败")
    }
  }).catch((err) => {
    ElMessage({
      type: 'success',
      message: err
    })
  })
}
const cleanFiles = () => {
  uploadFile.value.clearFiles()
}
//--------------------------------------------------------------
defineExpose({
  init
});
</script>
<style>
.update {
  padding-left: 120px;
}
</style>
