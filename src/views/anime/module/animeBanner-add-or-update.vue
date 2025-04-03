<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="动漫" prop="animeId">
            <el-select
                v-model="dataForm.animeId"
                filterable
                remote
                reserve-keyword
                placeholder="Please enter a keyword"
                :remote-method="remoteMethod"
                style="width: 200px"
            >
              <el-option
                  v-for="item in animeList"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id"
              />
            </el-select>
      </el-form-item>
          <el-form-item label="轮播图名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="轮播图名称"></el-input>
      </el-form-item>
      <el-form-item label="轮播图" prop="url">
        <el-upload
            ref="uploadFile"
            v-model:file-list="fileList"
            action="#"
            :http-request="upload"
            :on-remove="handleRemove"
            :on-success="handleSuccess"
            :limit="1"
            :on-exceed="handleExceed"
        >
          <el-button type="primary">上传图片</el-button>
          <template #tip>
            <div class="el-upload__tip">
              上传轮播图
            </div>
          </template>
        </el-upload>
      </el-form-item>
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
import {ElMessage, UploadProps} from "element-plus";
import axios from "axios";
import {getToken} from "@/utils/cache";
const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();

const dataForm = reactive({
  id: '',  animeId: '',  url: null,  name: '',  status: '',  isDelete: '',  updateTime: '',  createTime: ''});

const rules = ref({
          animeId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          url: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          status: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          isDelete: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          updateTime: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          createTime: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ]
  });

// 动漫数据
const animeList = ref([{id: '',name: ''}])
const remoteMethod = (query: string) => {
  axios.get(`/anime/animeInfo/remoteSearchByName?name=${query}`,
      {headers: {'token': getToken()}}).then(({data}) => {
    animeList.value = data.data
    console.log(animeList.value)
  })
}

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
    cleanFiles()
  }

  if (id) {
    getInfo(id);
  }
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get("/anime/animeBanner/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animeBanner", dataForm).then((res) => {
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

// 上传商品图片

const uploadFile = ref();

let fileList = computed(() => {
  if (dataForm.url != null){
    return [{uid: dataForm.url, name: dataForm.url}]
  }
})

const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {
  ElMessage.warning(
      "只能上传一张商品图片"
  )
}
const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {
  dataForm.url = null
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
  formData.append('id', dataForm.id)
  axios.post('anime/animeBanner/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
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


defineExpose({
  init
});
</script>
