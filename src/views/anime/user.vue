<template>
  <div class="mod-anime__user">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:user:save')" type="primary" @click="addOrUpdateHandle()">新增
        </el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:user:delete')" type="danger" @click="state.deleteHandle()">删除
        </el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border
              @selection-change="state.dataListSelectionChangeHandle" style="width: 100%"
    >
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="id" label="id" header-align="center" align="center"></el-table-column>
      <el-table-column prop="name" label="用户名" header-align="center" align="center"
                       min-width="120px"></el-table-column>
      <!--      <el-table-column prop="password" label="密码" header-align="center" align="center"></el-table-column>-->
      <el-table-column prop="gender" label="性别" header-align="center" align="center">
        <template #default="scope">
          <template v-if="scope.row.gender === null">
            <el-tag type="info">
              未知
            </el-tag>
          </template>
        </template>
      </el-table-column>
      <el-table-column prop="birthday" label="出生日期" header-align="center" align="center" min-width="140px">
        <template #default="scope">
          <template v-if="!!scope.row.birthday">
            <el-date-picker
                style="width: 100px;"
                class="my-date-picker"
                v-model="scope.row.birthday"
                type="date"
                size="small"
                readonly
            />
          </template>
          <template v-else>
            <el-tag type="info">
              未知
            </el-tag>
          </template>
        </template>
      </el-table-column>
      <el-table-column prop="location" label="地址" header-align="center" align="center" min-width="140px">
        <template #default="scope">
          <template v-if="scope.row.location === null">
            <el-tag type="info">
              未知
            </el-tag>
          </template>
        </template>
      </el-table-column>
      <el-table-column prop="joined" label="注册日期" header-align="center" align="center" min-width="140px">
        <template #default="scope">
          <template v-if="!!scope.row.joined">
            <el-date-picker
                style="width: 100px;"
                class="my-date-picker"
                v-model="scope.row.joined"
                type="date"
                size="small"
                readonly
            />
          </template>
          <template v-else>
            <el-tag type="info">
              未知
            </el-tag>
          </template>
        </template>
      </el-table-column>
      <el-table-column prop="daysWatched" label="观看天数" header-align="center" align="center"></el-table-column>
      <el-table-column prop="meanScore" label="平均得分" header-align="center" align="center"></el-table-column>
      <el-table-column prop="watching" label="追番数" header-align="center" align="center"></el-table-column>
      <el-table-column prop="completed" label="看番数" header-align="center" align="center"></el-table-column>
      <el-table-column prop="onHold" label="暂时停止看番数" header-align="center" align="center"></el-table-column>
      <el-table-column prop="dropped" label="弃番数" header-align="center" align="center"></el-table-column>
      <el-table-column prop="lastLogin" label="最近登录日期" header-align="center" align="center" min-width="140px">
        <template #default="scope">
          <template v-if="!!scope.row.lastLogin">
            <el-date-picker
                style="width: 100px;"
                class="my-date-picker"
                v-model="scope.row.lastLogin"
                type="date"
                size="small"
                readonly
            />
          </template>
          <template v-else>
            <el-tag type="info">
              未知
            </el-tag>
          </template>
        </template>
      </el-table-column>
      <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:user:update')" type="primary" link
                     @click="addOrUpdateHandle(scope.row.id)">修改
          </el-button>
          <el-button v-if="state.hasPermission('anime:user:delete')" type="primary" link
                     @click="state.deleteHandle(scope.row.id)">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination :current-page="state.page" :page-sizes="[10, 20, 50, 100]" :page-size="state.limit"
                   :total="state.total" layout="total, sizes, prev, pager, next, jumper"
                   @size-change="state.pageSizeChangeHandle"
                   @current-change="state.pageCurrentChangeHandle"></el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update ref="addOrUpdateRef" @refreshDataList="state.getDataList">确定</add-or-update>
  </div>
</template>

<script lang="ts" setup>
import useView from "@/hooks/useView";
import {reactive, ref, h, toRefs} from "vue";
import AddOrUpdate from "./module/user-add-or-update.vue";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/user/getUserInfo",
  getDataListIsPage: true,
  exportURL: "/anime/user/export",
  deleteURL: "/anime/user"
});

const state = reactive({...useView(view), ...toRefs(view)});

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};

const cellStyle= ({ row, column, rowIndex, columnIndex }:any)=> {
  if (column.property == "birthday" || column.property == "joined" || column.property == "lastLogin") {
    if (row[column.property] == null) {
      return {'content': '--'}
    }
  }
}

// :deep (.el-table .el-table__body td .cell:empty::after){
//   content: "--";
// }
</script>

<style scoped>
.my-date-picker {
  width: 80px;
}

:deep(.el-input__inner) {
  background-color: transparent !important;
}

/* el-table列数据为空自动显示-- */
.cell:empty::before{
  content:'--';
  color:gray;
}

</style>
