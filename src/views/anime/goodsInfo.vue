<template>
  <div class="mod-anime__goodsInfo">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:goodsInfo:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:goodsInfo:delete')" type="danger" @click="state.deleteHandle()">删除</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--              <el-table-column prop="id" label="id" header-align="center" align="center"></el-table-column>-->
              <el-table-column prop="name" label="商品名称" header-align="center" align="center"></el-table-column>
              <el-table-column prop="description" label="商品描述" header-align="center" align="center"></el-table-column>
              <el-table-column prop="price" label="商品价格" header-align="center" align="center"></el-table-column>
              <el-table-column prop="num" label="商品数量" header-align="center" align="center"></el-table-column>
              <el-table-column prop="listDate" label="上架日期" header-align="center" align="center"></el-table-column>
              <el-table-column prop="sales" label="销量" header-align="center" align="center"></el-table-column>
<!--              <el-table-column prop="isDelete" label="是否删除" header-align="center" align="center"></el-table-column>-->
            <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:goodsInfo:update')" type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="state.hasPermission('anime:goodsInfo:delete')" type="primary" link @click="state.deleteHandle(scope.row.id)">删除</el-button>
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
import AddOrUpdate from "./module/goodsInfo-add-or-update.vue";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/goodsInfo/page",
  getDataListIsPage: true,
  exportURL: "/anime/goodsInfo/export",
  deleteURL: "/anime/goodsInfo"
});

const state = reactive({ ...useView(view), ...toRefs(view) });

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};
</script>
