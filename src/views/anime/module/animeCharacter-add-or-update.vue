<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false"
             :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()"
             label-width="120px">
      <el-form-item label="动漫id" prop="animeId">
        <el-input v-model="dataForm.animeId" placeholder="动漫id" :disabled="dataForm.animeId !== null"></el-input>
      </el-form-item>
      <el-form-item label="角色名" prop="name">
        <el-input v-model="dataForm.name" placeholder="角色名"></el-input>
      </el-form-item>
      <el-form-item label="角色年龄" prop="age">
        <el-input v-model="dataForm.age" placeholder="角色年龄"></el-input>
      </el-form-item>
      <el-form-item label="角色出生日期" prop="birthday">
<!--        <el-input v-model="dataForm.birthday" placeholder="角色出生日期"></el-input>-->
        <el-date-picker
            v-model="dataForm.birthday"
            type="date"
            placeholder="Pick a day"
            :size="'default'"
            value-format="MM-DD"
        />
      </el-form-item>
      <el-form-item label="配音演员名" prop="cvName">
        <el-input v-model="dataForm.cvName" placeholder="配音演员名"></el-input>
      </el-form-item>
      <el-form-item label="角色描述" prop="description">
        <el-input v-model="dataForm.description" placeholder="角色描述" type="textarea" :autosize="{minRows: 3, maxRows:6}"></el-input>
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
          accept=".jpg,.png"
          style="padding-left: 120px;"
      >
        <el-button type="primary">上传图片</el-button>
        <template #tip>
          <div class="el-upload__tip">
            上传角色图片，限制jpg,png
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
import {computed, reactive, ref, toRef} from "vue";
import baseService from "@/service/baseService";
import {ElMessage, UploadProps} from "element-plus";
import axios from "axios";
import {getToken} from "@/utils/cache";

const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();

const dataForm = reactive({
  id: '', animeId: '', name: '', age: '', birthday: '', description: '', imageUrl: '', cvName: ''
});

const props = defineProps({anime:String})

const rules = ref({
  animeId: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  name: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  age: [
    {required: false, message: '必填项不能为空', trigger: 'blur'}
  ],
  birthday: [
    {required: false, message: '必填项不能为空', trigger: 'blur'}
  ],
  description: [
    {required: false, message: '', trigger: 'blur'}
  ],
  imageUrl: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  cvName: [
    {required: false, message: '', trigger: 'blur'}
  ]
});

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";
  dataForm.animeId = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
    //重置文件列表
    cleanFiles()
  }


  dataForm.animeId = <string>props.anime

  if (id) {
    getInfo(id);
  }
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get("/anime/animeCharacter/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animeCharacter", dataForm).then((res) => {
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
const uploadFile = ref();

// 文件列表
let fileList = computed(() => {
  if (dataForm.imageUrl != null){
    return [{uid: dataForm.imageUrl, name: dataForm.imageUrl}]
  }
})
const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {
  ElMessage.warning(
      "只能上传一个文件"
  )
}
const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {
  ElMessage({
    type: 'warning',
    message: '已移除文件',
  })
}
function upload(param: { file: string | Blob; }) {
  const formData = new FormData()
  formData.append('file', param.file)
  formData.append('animeId', dataForm.animeId)
  formData.append('id', dataForm.id)
  axios.post('anime/animeCharacter/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
    if (res.data){
      ElMessage({
        type: 'success',
        message: '文件上传成功'
      })
      dataForm.imageUrl = res.data
    } else {
      throw new Error("文件上传失败")
    }
  }).catch((err) => {
    ElMessage({
      type: "warning",
      message: err
    })
  })
}
const handleSuccess: UploadProps['onSuccess'] = (response, uploadFile, uploadFiles) => {
  console.log(dataForm)
}
const cleanFiles = () => {
  uploadFile.value.clearFiles()
}
//--------------------------------------------------------------
defineExpose({
  init
});
</script>
