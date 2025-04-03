<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-scrollbar>
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px" style="max-height: 70vh;">
          <el-form-item label="动漫名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="动漫名称"></el-input>
      </el-form-item>
          <el-form-item label="动漫类别" prop="genres">
            <el-select
                v-model="dataForm.genres"
                multiple
                placeholder="选择动漫类型"
                style="width: 200px"
            >
              <el-option
                  v-for="item in tree"
                  :key="item.id"
                  :label="item.name"
                  :value="item.nameEn"
              />
            </el-select>
      </el-form-item>
          <el-form-item label="动漫简介" prop="synopsis">
        <el-input v-model="dataForm.synopsis" placeholder="动漫简介"></el-input>
      </el-form-item>
          <el-form-item label="动漫类型" prop="type">
            <el-select
                v-model="dataForm.type"
                placeholder="选择动漫类型"
                style="width: 200px"
            >
              <el-option
                  v-for="item in typeList"
                  :key="item.label"
                  :label="item.label"
                  :value="item.label"
              />
            </el-select>
      </el-form-item>
          <el-form-item label="首映年份" prop="premiered">
            <el-date-picker
                v-model="dataForm.premiered"
                type="month"
                placeholder="选择年月"
                format="YYYY-MM"
                value-format="YYYY-MM"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="动漫状态" prop="status">
            <el-select
                v-model="dataForm.status"
                placeholder="选择动漫状态"
                style="width: 200px"
            >
              <el-option
                  v-for="item in stateList"
                  :key="item.label"
                  :label="item.label"
                  :value="item.label"
              />
            </el-select>
      </el-form-item>
          <el-form-item label="动漫来源" prop="source">
            <el-select
                v-model="dataForm.source"
                placeholder="选择动漫状态"
                style="width: 200px"
            >
              <el-option
                  v-for="item in sourceList"
                  :key="item.label"
                  :label="item.label"
                  :value="item.label"
              />
            </el-select>
      </el-form-item>
      <el-form-item label="动漫封面图片" prop="imageUrl">
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
              上传动漫封面图片
            </div>
          </template>
        </el-upload>
      </el-form-item>
      </el-form>
    </el-scrollbar>
    <template #footer>
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">确定</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import {computed, onMounted, reactive, ref} from "vue";
import baseService from "@/service/baseService";
import {ElMessage, UploadProps} from "element-plus";
import axios from "axios";
import {getToken} from "@/utils/cache";
import {isArray} from "lodash";
const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();

const dataForm = reactive({
  id: '',  name: '',  genres: [] as [] | string,  synopsis: '',  type: '',  imageUrl: null,  aired: '',  episodes: '',  score: '',  producers: '',  studios: '',  director: '',  premiered: '',  status: '',  source: '',  duration: '',  rating: '',  ranking: '',  popularity: '',  favorites: '',  scoredBy: '',  members: ''});

const rules = ref({
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          genres: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          synopsis: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          type: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          imageUrl: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          premiered: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          status: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          source: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ]
  });

const prop = defineProps({
  tree: {}
})

// 动漫状态
const stateList = [
  {label: '连载中', name: '连载中'},
  {label: '已完结', name: '已完结'},
  {label: '暂停中', name: '暂停中'},
  {label: '计划中', name: '计划中'}
]

const typeList = [
  {label: 'TV', name: 'TV'},
  {label: 'OVA', name: 'OVA'},
  {label: '剧场版', name: '剧场版'},
  {label: '网络动画', name: '网络动画'},
]

const sourceList = [
  {label: '原创', name: '原创'},
  {label: '漫画改', name: '漫画改'},
  {label: '小说改', name: '小说改'},
  {label: '游戏改', name: '游戏改'},
]

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
  baseService.get("/anime/animeInfo/" + id).then((res) => {
    Object.assign(dataForm, res.data);
    dataForm.genres = res.data.genres.split(',')
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    if (isArray(dataForm.genres)){
      dataForm.genres = dataForm.genres.join(',')
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animeInfo", dataForm).then((res) => {
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


// 上传动漫图片

const uploadFile = ref();

let fileList = computed(() => {
  if (dataForm.imageUrl != null){
    return [{uid: dataForm.imageUrl, name: dataForm.imageUrl}]
  }
})

const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {
  ElMessage.warning(
      "只能上传一张商品图片"
  )
}
const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {
  dataForm.imageUrl = null
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
  axios.post('anime/animeInfo/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
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
