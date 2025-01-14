<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-scrollbar>
    <el-form :model="dataForm" :rules="rules" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px" style="max-height: 70vh;">
          <el-form-item label="动漫名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="动漫名称"></el-input>
      </el-form-item>
          <el-form-item label="动漫类别" prop="genres">
        <el-input v-model="dataForm.genres" placeholder="动漫类别"></el-input>
      </el-form-item>
          <el-form-item label="动漫简介" prop="synopsis">
        <el-input v-model="dataForm.synopsis" placeholder="动漫简介"></el-input>
      </el-form-item>
          <el-form-item label="动漫类型" prop="type">
        <el-input v-model="dataForm.type" placeholder="动漫类型"></el-input>
      </el-form-item>
          <el-form-item label="动漫封面图片" prop="imageUrl">
        <el-input v-model="dataForm.imageUrl" placeholder="动漫封面图片"></el-input>
      </el-form-item>
          <el-form-item label="动漫在映日期" prop="aired">
        <el-input v-model="dataForm.aired" placeholder="动漫在映日期"></el-input>
      </el-form-item>
          <el-form-item label="动漫集数" prop="episodes">
        <el-input v-model="dataForm.episodes" placeholder="动漫集数"></el-input>
      </el-form-item>
          <el-form-item label="动漫评分" prop="score">
        <el-input v-model="dataForm.score" placeholder="动漫评分"></el-input>
      </el-form-item>
          <el-form-item label="制作公司" prop="producers">
        <el-input v-model="dataForm.producers" placeholder="制作公司"></el-input>
      </el-form-item>
<!--          <el-form-item label="动漫工作室" prop="studios">-->
<!--        <el-input v-model="dataForm.studios" placeholder="动漫工作室"></el-input>-->
<!--      </el-form-item>-->
          <el-form-item label="导演" prop="director">
        <el-input v-model="dataForm.director" placeholder="导演"></el-input>
      </el-form-item>
          <el-form-item label="首映年份及季度" prop="premiered">
        <el-input v-model="dataForm.premiered" placeholder="首映年份及季度"></el-input>
      </el-form-item>
          <el-form-item label="动漫状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="动漫状态"></el-input>
      </el-form-item>
          <el-form-item label="动漫来源" prop="source">
        <el-input v-model="dataForm.source" placeholder="动漫来源"></el-input>
      </el-form-item>
          <el-form-item label="每集时长" prop="duration">
        <el-input v-model="dataForm.duration" placeholder="每集时长"></el-input>
      </el-form-item>
<!--          <el-form-item label="适龄范围" prop="rating">-->
<!--        <el-input v-model="dataForm.rating" placeholder="适龄范围"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="动漫排名" prop="rank">-->
<!--        <el-input v-model="dataForm.ranking" placeholder="动漫排名"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="人气排名" prop="popularity">-->
<!--        <el-input v-model="dataForm.popularity" placeholder="人气排名"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="被收藏数" prop="favorites">-->
<!--        <el-input v-model="dataForm.favorites" placeholder="被收藏数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="评分用户数" prop="scoredBy">-->
<!--        <el-input v-model="dataForm.scoredBy" placeholder="评分用户数"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="粉丝数" prop="members">-->
<!--        <el-input v-model="dataForm.members" placeholder="粉丝数"></el-input>-->
<!--      </el-form-item>-->
      </el-form>
    </el-scrollbar>
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
  id: '',  name: '',  genres: '',  synopsis: '',  type: '',  imageUrl: '',  aired: '',  episodes: '',  score: '',  producers: '',  studios: '',  director: '',  premiered: '',  status: '',  source: '',  duration: '',  rating: '',  ranking: '',  popularity: '',  favorites: '',  scoredBy: '',  members: ''});

const rules = ref({
          name: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          genres: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          synopsis: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          type: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          imageUrl: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          aired: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          episodes: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          score: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
          producers: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
    //       studios: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
          director: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          premiered: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          status: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          source: [
      { required: true, message: '必填项不能为空', trigger: 'blur' }
    ],
          duration: [
      { required: false, message: '必填项不能为空', trigger: 'blur' }
    ],
    //       rating: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
    //       ranking: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
    //       popularity: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
    //       favorites: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
    //       scoredBy: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ],
    //       members: [
    //   { required: true, message: '必填项不能为空', trigger: 'blur' }
    // ]
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
  baseService.get("/anime/animeinfo/" + id).then((res) => {
    Object.assign(dataForm, res.data);
  });
};

// 表单提交
const dataFormSubmitHandle = () => {
  dataFormRef.value.validate((valid: boolean) => {
    if (!valid) {
      return false;
    }
    (!dataForm.id ? baseService.post : baseService.put)("/anime/animeinfo", dataForm).then((res) => {
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
