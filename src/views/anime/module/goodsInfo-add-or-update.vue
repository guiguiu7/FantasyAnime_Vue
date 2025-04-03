<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false"
             :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()"
             label-width="120px">
      <el-form-item label="商品名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="商品名称"></el-input>
      </el-form-item>
      <el-form-item label="商品描述" prop="description">
        <el-input v-model="dataForm.description" placeholder="商品描述"></el-input>
      </el-form-item>
      <el-form-item label="商品价格" prop="price">
        <el-input v-model="dataForm.price" placeholder="商品价格" type="number"></el-input>
      </el-form-item>
      <el-form-item label="商品数量" prop="num">
        <el-input v-model="dataForm.num" placeholder="商品数量" type="number"></el-input>
      </el-form-item>
      <el-form-item label="上传图片" prop="url">
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
              上传商品图片
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

const dataForm = reactive({name: '', description: '', price: '', num: '', url: null});

const rules = ref({
  name: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  description: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  price: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  num: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  url: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ]
});

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
  baseService.get("/anime/goodsInfo/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/goodsInfo", dataForm).then((res) => {
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
  if (dataForm.url != null) {
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
  axios.post('anime/goodsInfo/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
    if (res.data) {
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
