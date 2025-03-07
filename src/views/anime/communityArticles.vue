<template>
  <div class="mod-anime__communityArticles">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
<!--      <el-form-item>-->
<!--        <el-button v-if="state.hasPermission('anime:communityarticles:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
<!--      </el-form-item>-->
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:communityArticles:delete')" type="danger" @click="state.deleteHandle()">删除</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--              <el-table-column prop="id" label="id" header-align="center" align="center"></el-table-column>-->
<!--              <el-table-column prop="userId" label="用户id" header-align="center" align="center"></el-table-column>-->
              <el-table-column prop="userName" label="用户名" header-align="center" align="center"></el-table-column>
              <el-table-column prop="userAvatar" label="用户头像" header-align="center" align="center"></el-table-column>
              <el-table-column prop="content" label="文字内容" header-align="center" align="center"></el-table-column>
              <el-table-column prop="image" label="图片内容" header-align="center" align="center"></el-table-column>
              <el-table-column prop="releaseDate" label="发布日期" header-align="center" align="center"></el-table-column>
<!--              <el-table-column prop="isDelete" label="是否删除" header-align="center" align="center"></el-table-column>-->
            <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:communityArticles:update')" type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="state.hasPermission('anime:communityArticles:delete')" type="primary" link @click="state.deleteHandle(scope.row.id)">删除</el-button>
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
import AddOrUpdate from "./module/communityArticles-add-or-update.vue";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/communityArticles/page",
  getDataListIsPage: true,
  exportURL: "/anime/communityArticles/export",
  deleteURL: "/anime/communityArticles"
});

const state = reactive({ ...useView(view), ...toRefs(view) });

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};
</script>
