<template>
  <div class="mod-anime__noticeInfo">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:noticeInfo:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:noticeInfo:delete')" type="danger" @click="state.deleteHandle()">删除</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
              <el-table-column prop="info" label="公告信息" header-align="center" align="center"></el-table-column>
              <el-table-column prop="status" label="状态" header-align="center" align="center">
                <template #default="scope">
                  <el-switch v-model="scope.row.status" :active-value="1" :inactive-value="0" @change="isPub(scope.row)"></el-switch>
                </template>
              </el-table-column>
            <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:noticeInfo:update')" type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="state.hasPermission('anime:noticeInfo:delete')" type="primary" link @click="state.deleteHandle(scope.row.id)">删除</el-button>
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
import AddOrUpdate from "./module/noticeInfo-add-or-update.vue";
import axios from "axios";
import {ElMessage} from "element-plus";
import {getToken} from "@/utils/cache";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/noticeInfo/page",
  getDataListIsPage: true,
  exportURL: "/anime/noticeInfo/export",
  deleteURL: "/anime/noticeInfo"
});

const state = reactive({ ...useView(view), ...toRefs(view) });

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};


const isPub = (row:any) => {
  axios.post("anime/noticeInfo/changeStatus",{
    id: row.id, status: row.status}
  ).then(({data}) => {
    if (data.code == 0){
      ElMessage.success("修改成功")
      row.status = data.data
    }else {
      ElMessage.error(data.msg)
    }
  })
}

</script>
