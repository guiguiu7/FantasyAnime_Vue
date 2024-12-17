<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false"
             :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()"
             label-width="120px">
      <el-form-item label="动漫id" prop="animeId">
        <el-input v-model="dataForm.animeId" placeholder="动漫id" :disabled="dataForm.animeId !== null"></el-input>
      </el-form-item>
      <el-form-item label="角色名" prop="name">
        <el-input v-model="dataForm.name" placeholder="角色名"></el-input>
      </el-form-item>
      <el-form-item label="角色年龄" prop="age">
        <el-input v-model="dataForm.age" placeholder="角色年龄"></el-input>
      </el-form-item>
      <el-form-item label="角色出生日期" prop="birthday">
<!--        <el-input v-model="dataForm.birthday" placeholder="角色出生日期"></el-input>-->
        <el-date-picker
            v-model="dataForm.birthday"
            type="date"
            placeholder="Pick a day"
            :size="'default'"
            value-format="YYYY-MM-DD"
        />
      </el-form-item>
      <el-form-item label="角色描述" prop="description">
        <el-input v-model="dataForm.description" placeholder="角色描述"></el-input>
      </el-form-item>
      <el-form-item label="角色图片url" prop="imageUrl">
        <el-input v-model="dataForm.imageUrl" placeholder="角色图片url"></el-input>
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">确定</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import {reactive, ref, toRef} from "vue";
import baseService from "@/service/baseService";
import {ElMessage} from "element-plus";

const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const dataFormRef = ref();

const dataForm = reactive({
  id: '', animeId: '', name: '', age: '', birthday: '', description: '', imageUrl: ''
});

const props = defineProps({anime:String})

const rules = ref({
  animeId: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  name: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  age: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  birthday: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  description: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ],
  imageUrl: [
    {required: true, message: '必填项不能为空', trigger: 'blur'}
  ]
});

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";
  dataForm.animeId = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
  }


  dataForm.animeId = <string>props.anime
  console.log(dataForm)
  if (id) {
    getInfo(id);
  }
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get("/anime/animecharacter/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animecharacter", dataForm).then((res) => {
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
