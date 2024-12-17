<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="角色id" prop="characterId">
        <el-input v-model="dataForm.characterId" placeholder="角色id"></el-input>
      </el-form-item>
          <el-form-item label="配音演员名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="配音演员名称"></el-input>
      </el-form-item>
          <el-form-item label="联系方式" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="联系方式"></el-input>
      </el-form-item>
          <el-form-item label="角色名称" prop="characterName">
        <el-input v-model="dataForm.characterName" placeholder="角色名称"></el-input>
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
  id: '',  characterId: '',  name: '',  phone: '',  characterName: ''});

const rules = ref({
          characterId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          phone: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          characterName: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ]
  });

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
  }

  if (id) {
    getInfo(id);
  }
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get("/anime/animecv/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animecv", dataForm).then((res) => {
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
