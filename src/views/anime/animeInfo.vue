<template>
  <div class="mod-anime__animeInfo">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-input v-model="state.dataForm.name" placeholder="名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="state.getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeInfo:save')" type="primary" @click="addOrUpdateHandle()">新增
        </el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeInfo:delete')" type="danger" @click="state.deleteHandle()">
          删除
        </el-button>
      </el-form-item>
    </el-form>
    <div style="display: flex; height: calc(100vh - 260px)">
      <el-table v-loading="state.dataListLoading" :data="state.dataList" border
                @selection-change="state.dataListSelectionChangeHandle" @sort-change="state.dataListSortChangeHandle"
                style="width: 100%;height: 100%"
      >
        <template #empty>暂无数据</template>
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="name" label="动漫名称" header-align="center" align="center"
                         min-width="150px"></el-table-column>
        <el-table-column prop="genres" label="动漫类别" header-align="center" align="center" min-width="150px">
          <template #default="scope">
            <el-tag v-for="i in getLocalizedGenre(scope.row.genres)" type="success" style="margin: 2px">
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
        <el-table-column prop="type" label="动漫类型" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column prop="imageUrl" label="动漫封面图片" header-align="center" align="center" min-width="120px">
          <template #default="scope">
            <el-image preview-teleported :src="scope.row.imageUrl" :preview-src-list="[scope.row.imageUrl]"></el-image>
          </template>
        </el-table-column>
        <el-table-column prop="episodes" label="动漫集数" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column prop="score" label="动漫评分" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column prop="premiered" label="首映年份" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column prop="status" label="动漫状态" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column prop="source" label="动漫来源" header-align="center"
                         align="center" min-width="100px"></el-table-column>
<!--        <el-table-column prop="favorites" label="被收藏数" header-align="center" align="center"-->
<!--                         min-width="100px"></el-table-column>-->
        <el-table-column prop="likes" label="点赞数" header-align="center" align="center"
                         min-width="100px"></el-table-column>
        <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
          <template v-slot="scope">
            <el-button v-if="state.hasPermission('anime:animeInfo:update')" type="primary" link
                       @click="addOrUpdateHandle(scope.row.id)">修改
            </el-button>
            <el-button v-if="state.hasPermission('anime:animeInfo:delete')" type="primary" link
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
    <add-or-update ref="addOrUpdateRef" @refreshDataList="state.getDataList" :tree="tree" :generMap="genreMap">确定</add-or-update>
  </div>
</template>

<script lang="ts" setup>
import useView from "@/hooks/useView";
import {onMounted, reactive, ref, toRefs} from "vue";
import AddOrUpdate from "./module/animeInfo-add-or-update.vue";
import axios from "axios";
import {getToken} from "@/utils/cache";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/animeInfo/page",
  getDataListIsPage: true,
  exportURL: "/anime/animeInfo/export",
  deleteURL: "/anime/animeInfo",
  dataForm: {
    name: ""
  }
});

const state = reactive({...useView(view), ...toRefs(view)});

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};

// 选择动漫类型
onMounted(async () => {
  await getTree()
})

const genreMap = ref()
const tree = ref([{id: '', name: '', nameEn: ''}])
const getTree = async () => {
  await axios.get("/anime/animeType/tree", {headers: {'token': getToken()}}).then(({data}) => {
    tree.value = data.data
  })
  getMap()
}

const getMap = () => {
  genreMap.value = new Map(
      tree.value.map(genre =>
          [genre.nameEn, genre.name]
      )
  )
}

const getLocalizedGenre = (genres: string) => {
  const safe = String(genres || '')
  return safe.split(',')
      .map(item => item.trim())
      .map(genre => genreMap.value.get(genre) || genre)
}
</script>
