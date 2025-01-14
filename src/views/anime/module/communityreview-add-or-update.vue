<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="所属文章id" prop="articleId">
        <el-input v-model="dataForm.articleId" placeholder="所属文章id"></el-input>
      </el-form-item>
          <el-form-item label="所属用户id" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="所属用户id"></el-input>
      </el-form-item>
          <el-form-item label="父id" prop="parentId">
        <el-input v-model="dataForm.parentId" placeholder="父id"></el-input>
      </el-form-item>
          <el-form-item label="内容" prop="content">
        <el-input v-model="dataForm.content" placeholder="内容"></el-input>
      </el-form-item>
          <el-form-item label="点赞数" prop="like">
        <el-input v-model="dataForm.like" placeholder="点赞数"></el-input>
      </el-form-item>
          <el-form-item label="回复评论id" prop="commentId">
        <el-input v-model="dataForm.commentId" placeholder="回复评论id"></el-input>
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
  id: '',  articleId: '',  userId: '',  parentId: '',  content: '',  like: '',  commentId: '',  updateTime: '',  createTime: ''});

const rules = ref({
          articleId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          userId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          parentId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          content: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          like: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          commentId: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          updateTime: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          createTime: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
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
  baseService.get("/anime/communityreview/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/communityreview", dataForm).then((res) => {
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
