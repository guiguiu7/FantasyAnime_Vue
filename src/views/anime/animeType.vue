<template>
  <div class="mod-anime__animeType">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeType:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeType:delete')" type="danger" @click="state.deleteHandle()">删除</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--              <el-table-column prop="id" label="" header-align="center" align="center"></el-table-column>-->
              <el-table-column prop="name" label="动漫类型名" header-align="center" align="center"></el-table-column>
              <el-table-column prop="nameEn" label="动漫类型名(英文)" header-align="center" align="center"></el-table-column>
              <el-table-column prop="status" label="状态" header-align="center" align="center">
                <template #default="scope">
                  <el-switch v-model="scope.row.status" :active-value="1" :inactive-value="0" @change="changeStatus(scope.row)"/>
                </template>
              </el-table-column>
            <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:animeType:update')" type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="state.hasPermission('anime:animeType:delete')" type="primary" link @click="state.deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination :current-page="state.page" :page-sizes="[10, 20, 50, 100]" :page-size="state.limit" :total="state.total" layout="total, sizes, prev, pager, next, jumper" @size-change="state.pageSizeChangeHandle" @current-change="state.pageCurrentChangeHandle"> </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update ref="addOrUpdateRef" @refreshDataList="state.getDataList">确定</add-or-update>
  </div>
</template>

<script lang="ts" setup>
import useView from "@/hooks/useView";
import { reactive, ref, toRefs } from "vue";
import AddOrUpdate from "./module/animeType-add-or-update.vue";
import axios from "axios";
import {ElMessage} from "element-plus";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/animeType/page",
  getDataListIsPage: true,
  exportURL: "/anime/animeType/export",
  deleteURL: "/anime/animeType"
});

const state = reactive({ ...useView(view), ...toRefs(view) });

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};

function changeStatus(row: any){
  axios.post('/anime/animeType/changeStatus',{id : row.id, status: row.status}).then(() => {
    ElMessage({
      message: '修改状态成功',
      type: 'success',
      plain: true,
    })
  }).catch(() => {
    ElMessage({
      message: '修改状态失败',
      type: 'error',
      plain: true,
    })
  })
}
</script>
