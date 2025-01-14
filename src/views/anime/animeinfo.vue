<template>
  <div class="mod-anime__animeinfo">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-input v-model="state.dataForm.name" placeholder="名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="state.getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeinfo:save')" type="primary" @click="addOrUpdateHandle()">新增
        </el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeinfo:delete')" type="danger" @click="state.deleteHandle()">
          删除
        </el-button>
      </el-form-item>
    </el-form>
    <div style="display: flex; height: calc(100vh - 260px)">
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border
              @selection-change="state.dataListSelectionChangeHandle" @sort-change="state.dataListSortChangeHandle" style="width: 100%;height: 100%"
    >
      <template #empty>暂无数据</template>
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="id" label="id" header-align="center" align="center" ></el-table-column>
      <el-table-column prop="name" label="动漫名称" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="genres" label="动漫类别" header-align="center" align="center" min-width="150px">
        <template #default="scope">
          <el-tag v-for="i in scope.row.genres.split(',')" type="success" style="margin: 2px">
            {{ i }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="synopsis" label="动漫简介" header-align="center" align="center" min-width="200px">
        <template #default="scope">
          <el-popover
              width="500"
              trigger="hover"
              effect="dark"
              placement="bottom"
              :open-delay="500"
              :content="scope.row.synopsis"
          >
            <template #reference>
              <div class="text-over">{{ scope.row.synopsis }}</div>
            </template>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="type" label="动漫类型" header-align="center" align="center" min-width="100px"></el-table-column>
      <el-table-column prop="imageUrl" label="动漫封面图片" header-align="center" align="center" min-width="120px">
        <template #default="scope">
          <el-image preview-teleported :src="scope.row.imageUrl" :preview-src-list="[scope.row.imageUrl]"></el-image>
        </template>
      </el-table-column>
      <el-table-column prop="aired" label="动漫在映日期" header-align="center" align="center" min-width="200px"></el-table-column>
      <el-table-column prop="episodes" label="动漫集数" header-align="center" align="center" min-width="100px"></el-table-column>
      <el-table-column prop="score" label="动漫评分" header-align="center" align="center" min-width="100px"></el-table-column>
      <el-table-column prop="producers" label="制作公司" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="studios" label="动漫工作室" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="director" label="导演" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="premiered" label="首映年份及季度" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="status" label="动漫状态" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="source" label="动漫来源" header-align="center"
                       align="center" min-width="100px"></el-table-column>
      <el-table-column prop="duration" label="每集时长" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="rating" label="适龄范围" header-align="center" align="center" min-width="150px"></el-table-column>
      <el-table-column prop="ranking" label="动漫排名" header-align="center" align="center" min-width="100px" sortable="custom"></el-table-column>
      <el-table-column prop="popularity" label="人气排名" header-align="center" align="center" min-width="100px" sortable="custom"></el-table-column>
      <el-table-column prop="favorites" label="被收藏数" header-align="center" align="center" min-width="100px" sortable="custom"></el-table-column>
      <el-table-column prop="scoredBy" label="评分用户数" header-align="center" align="center" min-width="100px" sortable="custom"></el-table-column>
      <el-table-column prop="members" label="粉丝数" header-align="center" align="center" min-width="100px" sortable="custom"></el-table-column>
      <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:animeinfo:update')" type="primary" link
                     @click="addOrUpdateHandle(scope.row.id)">修改
          </el-button>
          <el-button v-if="state.hasPermission('anime:animeinfo:delete')" type="primary" link
                     @click="state.deleteHandle(scope.row.id)">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    </div>
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
import {computed, reactive, ref, toRefs} from "vue";
import AddOrUpdate from "./module/animeinfo-add-or-update.vue";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/animeinfo/page",
  getDataListIsPage: true,
  exportURL: "/anime/animeinfo/export",
  deleteURL: "/anime/animeinfo",
  dataForm: {
    name: ""
  }
});

const state = reactive({...useView(view), ...toRefs(view)});

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};

</script>
