<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
          <el-form-item label="商品id" prop="goodId">
        <el-input v-model="dataForm.goodId" placeholder="商品id"></el-input>
      </el-form-item>
          <el-form-item label="用户id" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
      </el-form-item>
          <el-form-item label="商品名称" prop="goodName">
        <el-input v-model="dataForm.goodName" placeholder="商品名称"></el-input>
      </el-form-item>
          <el-form-item label="购买数量" prop="num">
        <el-input v-model="dataForm.num" placeholder="购买数量"></el-input>
      </el-form-item>
          <el-form-item label="购买日期" prop="buyTime">
        <el-input v-model="dataForm.buyTime" placeholder="购买日期"></el-input>
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
  id: '',  goodId: '',  userId: '',  goodName: '',  num: '',  buyTime: '',  isDelete: ''});

const rules = ref({
          goodId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          userId: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          goodName: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          num: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          buyTime: [
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
  baseService.get("/anime/goodsRecord/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/goodsRecord", dataForm).then((res) => {
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
