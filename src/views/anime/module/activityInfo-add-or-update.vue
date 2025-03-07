<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="活动名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="活动名称"></el-input>
      </el-form-item>
          <el-form-item label="活动内容" prop="content">
        <el-input v-model="dataForm.content" placeholder="活动内容"></el-input>
      </el-form-item>
          <el-form-item label="开始时间" prop="startTime">
        <el-input v-model="dataForm.startTime" placeholder="开始时间"></el-input>
      </el-form-item>
          <el-form-item label="结束时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="结束时间"></el-input>
      </el-form-item>
          <el-form-item label="举办者" prop="organizerName">
        <el-input v-model="dataForm.organizerName" placeholder="举办者"></el-input>
      </el-form-item>
          <el-form-item label="举办者id" prop="organizerId">
        <el-input v-model="dataForm.organizerId" placeholder="举办者id"></el-input>
      </el-form-item>
          <el-form-item label="参加人数" prop="number">
        <el-input v-model="dataForm.number" placeholder="参加人数"></el-input>
      </el-form-item>
          <el-form-item label="是否删除" prop="isDelete">
        <el-input v-model="dataForm.isDelete" placeholder="是否删除"></el-input>
      </el-form-item>
          <el-form-item label="更新时间" prop="updateTime">
        <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
      </el-form-item>
          <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
  id: '',  name: '',  content: '',  startTime: '',  endTime: '',  organizerName: '',  organizerId: '',  number: '',  isDelete: '',  updateTime: '',  createTime: ''});

const rules = ref({
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          content: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          startTime: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          endTime: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          organizerName: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          organizerId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          number: [
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
  baseService.get("/anime/activityInfo/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/activityInfo", dataForm).then((res) => {
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
