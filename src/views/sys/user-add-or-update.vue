<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
      <el-form-item prop="username" label="用户名">
        <el-input v-model="dataForm.username" placeholder="用户名"></el-input>
      </el-form-item>
<!--      <el-form-item prop="deptName" label="所属部门">-->
<!--        <ren-dept-tree v-model="dataForm.deptId" placeholder="选择部门" v-model:deptName="dataForm.deptName"></ren-dept-tree>-->
<!--      </el-form-item>-->
      <el-form-item prop="password" label="密码" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
      </el-form-item>
      <el-form-item prop="confirmPassword" label="确认密码" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.confirmPassword" type="password" placeholder="确认密码"></el-input>
      </el-form-item>
      <el-form-item prop="realName" label="真实姓名">
        <el-input v-model="dataForm.realName" placeholder="真实姓名"></el-input>
      </el-form-item>
      <el-form-item prop="gender" label="性别">
        <ren-radio-group v-model="dataForm.gender" dict-type="gender"></ren-radio-group>
      </el-form-item>
      <el-form-item prop="email" label="邮箱">
        <el-input v-model="dataForm.email" placeholder="邮箱"></el-input>
      </el-form-item>
      <el-form-item prop="mobile" label="手机号">
        <el-input v-model="dataForm.mobile" placeholder="手机号"></el-input>
      </el-form-item>
      <el-form-item prop="roleIdList" label="角色配置" class="role-list">
        <el-select v-model="dataForm.roleIdList" multiple placeholder="角色配置">
          <el-option v-for="role in roleList" :key="role.id" :label="role.name" :value="role.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item prop="status" label="状态">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">停用</el-radio>
          <el-radio :label="1">正常</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="headUrl" label="头像">
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
          <el-button type="primary">上传头像</el-button>
          <template #tip>
            <div class="el-upload__tip">
              上传头像
            </div>
          </template>
        </el-upload>
      </el-form-item>
    </el-form>
    <template v-slot:footer>
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">确定</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import {computed, reactive, ref} from "vue";
import baseService from "@/service/baseService";
import { isEmail, isMobile } from "@/utils/utils";
import { IObject } from "@/types/interface";
import {ElMessage, UploadProps} from "element-plus";
import axios from "axios";
import {getToken} from "@/utils/cache";
const emit = defineEmits(["refreshDataList"]);

const visible = ref(false);
const roleList = ref<any[]>([]);
const dataFormRef = ref();

const dataForm = reactive({
  id: "",
  username: "",
  headUrl: null,
  // deptId: "",
  // deptName: "",
  password: "",
  confirmPassword: "",
  realName: "",
  gender: 0,
  email: "",
  mobile: "",
  roleIdList: [] as IObject[],
  highRoleIdList: [] as IObject[],
  status: 1
});

const validatePassword = (rule: any, value: string, callback: (e?: Error) => any): any => {
  if (!dataForm.id && !/\S/.test(value)) {
    return callback(new Error("必填项不能为空"));
  }
  callback();
};
const validateConfirmPassword = (rule: any, value: string, callback: (e?: Error) => any): any => {
  if (!dataForm.id && !/\S/.test(value)) {
    return callback(new Error("必填项不能为空"));
  }
  if (dataForm.password !== value) {
    return callback(new Error("确认密码与密码输入不一致"));
  }
  callback();
};
const validateEmail = (rule: any, value: string, callback: (e?: Error) => any): any => {
  if (value && !isEmail(value)) {
    return callback(new Error("邮箱格式错误"));
  }
  callback();
};
const validateMobile = (rule: any, value: string, callback: (e?: Error) => any): any => {
  if (value && !isMobile(value)) {
    return callback(new Error("手机格式错误"));
  }
  callback();
};
const rules = ref({
  username: [{ required: true, message: "必填项不能为空", trigger: "blur" }],
  // deptName: [{ required: true, message: "必填项不能为空", trigger: "change" }],
  headUrl: [{ required: true, message: "必填项不能为空", trigger: "blur"}],
  password: [{ required: true, validator: validatePassword, trigger: "blur" }],
  confirmPassword: [{ required: true, validator: validateConfirmPassword, trigger: "blur" }],
  realName: [{ required: true, message: "必填项不能为空", trigger: "blur" }],
  email: [{ validator: validateEmail, trigger: "blur" }],
  mobile: [{ validator: validateMobile, trigger: "blur" }]
});

const init = (id?: number) => {
  visible.value = true;
  dataForm.id = "";
  // dataForm.deptId = "";

  // 重置表单数据
  if (dataFormRef.value) {
    dataFormRef.value.resetFields();
    cleanFiles()
  }

  Promise.all([getRoleList()]).then(() => {
    if (id) {
      getInfo(id);
    }
  });
};

// 获取角色列表
const getRoleList = () => {
  return baseService.get("/sys/role/list").then((res) => {
    roleList.value = res.data;
  });
};

// 获取信息
const getInfo = (id: number) => {
  baseService.get(`/sys/user/${id}`).then((res) => {
    Object.assign(dataForm, res.data);
    dataForm.highRoleIdList = dataForm.roleIdList.filter(id => !roleList.value.some(role => role.id === id));
    dataForm.roleIdList = dataForm.roleIdList.filter(id => !dataForm.highRoleIdList.includes(id))
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/sys/user", {
      ...dataForm,
      roleIdList: [...dataForm.roleIdList,...dataForm.highRoleIdList]
    }).then((res) => {
      ElMessage.success({
        message: "成功",
        duration: 500,
        onClose: () => {
          visible.value = false;
          emit("refreshDataList");
        }
      });
    });
  });
};

// 上传商品图片

const uploadFile = ref();

let fileList = computed(() => {
  if (dataForm.headUrl != null) {
    return [{uid: dataForm.headUrl, name: dataForm.headUrl}]
  }
})

const handleExceed: UploadProps['onExceed'] = (uploadFiles) => {
  ElMessage.warning(
      "只能上传一张头像"
  )
}
const handleRemove: UploadProps['onRemove'] = (file, uploadFiles) => {
  dataForm.headUrl = null
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
  axios.post('/sys/user/upload', formData, {headers: {'Token': getToken()}}).then((res) => {
    if (res.data) {
      ElMessage({
        type: 'success',
        message: '文件上传成功'
      })
      dataForm.headUrl = res.data
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

<style lang="less" scoped>
.mod-sys__user {
  .role-list {
    .el-select {
      width: 100%;
    }
  }
}
</style>
