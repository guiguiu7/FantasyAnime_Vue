<template>
  <el-dialog v-model="visible" :title="!dataForm.userId ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="" prop="goodId">
        <el-input v-model="dataForm.goodId" placeholder=""></el-input>
      </el-form-item>
          <el-form-item label="商品数量" prop="num">
        <el-input v-model="dataForm.num" placeholder="商品数量"></el-input>
      </el-form-item>
          <el-form-item label="是否购买" prop="isBuy">
        <el-input v-model="dataForm.isBuy" placeholder="是否购买"></el-input>
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
  userId: '',  goodId: '',  num: '',  isBuy: '',  isDelete: '',  updateTime: '',  createTime: ''});

const rules = ref({
          goodId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          num: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          isBuy: [
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

const init = (userId?: number) => {
  visible.value = true;
  dataForm.userId = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
  }

  if (userId) {
    getInfo(userId);
  }
};

// 获取信息
const getInfo = (userId: number) => {
  baseService.get("/anime/cartInfo/" + userId).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.userId ? baseService.post : baseService.put)("/anime/cartInfo", dataForm).then((res) => {
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
