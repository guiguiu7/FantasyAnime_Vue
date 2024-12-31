<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="用户名" prop="name">
        <el-input v-model="dataForm.name" placeholder="用户名"></el-input>
      </el-form-item>
          <el-form-item label="密码" prop="password">
        <el-input v-model="dataForm.password" placeholder="密码"></el-input>
      </el-form-item>
          <el-form-item label="性别" prop="gender">
        <el-input v-model="dataForm.gender" placeholder="性别"></el-input>
      </el-form-item>
          <el-form-item label="出生日期" prop="birthday">
        <el-input v-model="dataForm.birthday" placeholder="出生日期"></el-input>
      </el-form-item>
          <el-form-item label="地址" prop="location">
        <el-input v-model="dataForm.location" placeholder="地址"></el-input>
      </el-form-item>
<!--          <el-form-item label="注册日期" prop="joined">-->
<!--        <el-input v-model="dataForm.joined" placeholder="注册日期"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="观看天数" prop="daysWatched">-->
<!--        <el-input v-model="dataForm.daysWatched" placeholder="观看天数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="平均得分" prop="meanScore">-->
<!--        <el-input v-model="dataForm.meanScore" placeholder="平均得分"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="追番数" prop="watching">-->
<!--        <el-input v-model="dataForm.watching" placeholder="追番数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="看番数" prop="completed">-->
<!--        <el-input v-model="dataForm.completed" placeholder="看番数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="暂时停止看番数" prop="onHold">-->
<!--        <el-input v-model="dataForm.onHold" placeholder="暂时停止看番数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="弃番数" prop="dropped">-->
<!--        <el-input v-model="dataForm.dropped" placeholder="弃番数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="最近登录日期" prop="lastLogin">-->
<!--        <el-input v-model="dataForm.lastLogin" placeholder="最近登录日期"></el-input>-->
<!--      </el-form-item>-->
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
  id: '',  name: '',  password: '',  gender: '',  birthday: '',  location: '',  joined: '',  daysWatched: '',  meanScore: '',  watching: '',  completed: '',  onHold: '',  dropped: '',  lastLogin: ''});

const rules = ref({
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          password: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          gender: [
      { required: false, message: '', trigger: 'blur' }
    ],
          birthday: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          location: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          joined: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          daysWatched: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          meanScore: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          watching: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          completed: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          onHold: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          dropped: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          lastLogin: [
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
  baseService.get("/anime/user/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/user", dataForm).then((res) => {
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
