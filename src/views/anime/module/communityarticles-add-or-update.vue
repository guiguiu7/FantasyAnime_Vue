<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="用户id" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
      </el-form-item>
          <el-form-item label="用户名" prop="userName">
        <el-input v-model="dataForm.userName" placeholder="用户名"></el-input>
      </el-form-item>
          <el-form-item label="用户头像" prop="userAvatar">
        <el-input v-model="dataForm.userAvatar" placeholder="用户头像"></el-input>
      </el-form-item>
          <el-form-item label="文字内容" prop="content">
        <el-input v-model="dataForm.content" placeholder="文字内容"></el-input>
      </el-form-item>
          <el-form-item label="图片内容" prop="image">
        <el-input v-model="dataForm.image" placeholder="图片内容"></el-input>
      </el-form-item>
          <el-form-item label="发布日期" prop="releaseDate">
        <el-input v-model="dataForm.releaseDate" placeholder="发布日期"></el-input>
      </el-form-item>
<!--          <el-form-item label="是否删除" prop="isDelete">-->
<!--        <el-input v-model="dataForm.isDelete" placeholder="是否删除"></el-input>-->
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
  id: '',  userId: '',  userName: '',  userAvatar: '',  content: '',  image: '',  releaseDate: '',  isDelete: ''});

const rules = ref({
          userId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          userName: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          userAvatar: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          content: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          image: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          releaseDate: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          isDelete: [
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
  baseService.get("/anime/communityarticles/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/communityarticles", dataForm).then((res) => {
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
