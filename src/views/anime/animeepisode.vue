<template>
  <div class="mod-anime__animeepisode">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeepisode:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animeepisode:delete')" type="danger" @click="state.deleteHandle()">删除</el-button>
      </el-form-item>
    </el-form>
    <div style="display: flex; height: calc(100vh - 260px)">
      <!--      <el-select-->
      <!--          v-model="animeInfo"-->
      <!--          filterable-->
      <!--          placeholder="Select"-->
      <!--          style="width: 240px"-->
      <!--      >-->
      <div style="margin-right: 5px;display: flex;flex-direction: column">
        <el-input v-model="query"
                  style="width: 240px; margin: 2px 2px 0 2px"
                  placeholder="Please enter keyword"
                  @input="onQueryChanged"/>
        <el-tree-v2
            ref="treeRef"
            style="max-width: 240px; max-height: 100vh;"
            icon=""
            :data="animeInfo.list"
            :props="{value:'id',label:'name'}"
            :highlight-current="true"
            :height="defaultHeight"
            @node-click="clickNode"
        />
        <div style="max-width: 240px">
          <el-pagination size="small"
                         layout="prev, pager, next"
                         :total="parseInt(animeInfo.count)"
                         v-model:current-page="page.page"
                         v-model:page-size="page.limit"
                         @change="pageChange"/>
        </div>
      </div>
    <el-table v-loading="state.dataListLoading" :data="state.dataList" border @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--              <el-table-column prop="animeId" label="动漫id" header-align="center" align="center"></el-table-column>-->
              <el-table-column prop="num" label="集数" header-align="center" align="center" width="100px"></el-table-column>
              <el-table-column prop="title" label="标题名" header-align="center" align="center"></el-table-column>
              <el-table-column prop="description" label="描述" header-align="center" align="center"></el-table-column>
            <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="state.hasPermission('anime:animeepisode:update')" type="primary" link @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="state.hasPermission('anime:animeepisode:delete')" type="primary" link @click="state.deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    </div>

    <el-pagination :current-page="state.page" :page-sizes="[10, 20, 50, 100]" :page-size="state.limit" :total="state.total" layout="total, sizes, prev, pager, next, jumper" @size-change="state.pageSizeChangeHandle" @current-change="state.pageCurrentChangeHandle"> </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update :anime="animeId" ref="addOrUpdateRef" @refreshDataList="state.getDataList">确定</add-or-update>
  </div>
</template>

<script lang="ts" setup>
import useView from "@/hooks/useView";
import { reactive, ref, toRefs, onMounted } from "vue";
import AddOrUpdate from "./module/animeepisode-add-or-update.vue";
import {ElTreeV2} from "element-plus";
import {TreeNodeData} from "element-plus/es/components/tree-v2/src/types";
import axios from "axios";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/animeepisode/page",
  getDataListIsPage: true,
  exportURL: "/anime/animeepisode/export",
  deleteURL: "/anime/animeepisode",
  dataForm: {
    animeId: "",
    name: ""
  }
});

const state = reactive({ ...useView(view), ...toRefs(view) });

const addOrUpdateRef = ref();

// 新增时自动填入动漫id
const addOrUpdateHandle = (id?: number) => {
  addOrUpdateRef.value.init(id);
};

onMounted(async () => {
  // 设置Tree V2高度
  defaultHeight.value = document.documentElement.clientHeight - 330
  await getAnimeInfoTree(page)
  await state.getDataList()
})


let query = ref('');
const treeRef = ref<InstanceType<typeof ElTreeV2>>()

//对对应的动漫进行角色的操作
let animeId = ref()

const clickNode = (data: TreeNodeData) => {
  animeId.value = data.id
  view.dataForm.animeId = data.id
  state.getDataList()
}

//设置tree-v2高度
let defaultHeight = ref({})

//侧边栏
let animeInfo = ref({})
let page = reactive({limit:25, page:1, name:''})

const getAnimeInfoTree = async (page: any) => {axios.get('/api/anime/animeinfo/getInfoTree', {params: page}).then((res) => {
  animeInfo.value = res.data
})}

const onQueryChanged = (query: string) => {
  // TODO: fix typing when refactor tree-v2
  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  // treeRef.value!.filter(query)
  page.name = query
  getAnimeTreeByName(page)
}

const getAnimeTreeByName = async (page: any) => {axios.get("/api/anime/animeinfo/getAnimeTreeByName", {params: page}).then((res) => {
  animeInfo.value = res.data
  console.log(animeInfo)
})}

const pageChange = async (num: number) => {
  page.page = num
  if (query !== null){
    getAnimeTreeByName(page)
  }else {
    getAnimeInfoTree(page)
  }
}
</script>
