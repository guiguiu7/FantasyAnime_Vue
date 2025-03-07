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
      </el-form>
    <template #footer>
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">确定</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import { reactive, ref } from "vue";
import baseService from "@/service/baseService";
import { ElMessage } from "element-plus";
const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();

const dataForm = reactive({
  id: '',  animeId: '',  num: '',  title: '',  description: ''});

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
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ]
  });

const props = defineProps({anime:String})

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";
  // 填入动漫id
  dataForm.animeId = "";


  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
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

defineExpose({
  init
});
</script>
