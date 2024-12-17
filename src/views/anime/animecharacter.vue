<template>
  <div class="mod-anime__animecharacter">
    <el-form :inline="true" :model="state.dataForm" @keyup.enter="state.getDataList()">
      <el-form-item>
        <el-input v-model="state.dataForm.name" placeholder="名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="state.getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animecharacter:save')" type="primary"
                   @click="addOrUpdateHandle()">
          新增
        </el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="state.hasPermission('anime:animecharacter:delete')" type="danger"
                   @click="state.deleteHandle()">删除
        </el-button>
      </el-form-item>
    </el-form>
    <div style="display: flex; height: calc(100vh - 260px)">
      <!--      <el-select-->
      <!--          v-model="animeInfo"-->
      <!--          filterable-->
      <!--          placeholder="Select"-->
      <!--          style="width: 240px"-->
      <!--      >-->
      <div style="margin-right: 5px; height: 100%">
        <el-input v-model="query"
                  style="width: 240px; margin: 2px 2px 0 2px"
                  placeholder="Please enter keyword"
                  @input="onQueryChanged"/>
        <el-tree-v2
            ref="treeRef"
            style="width: 300px; max-width: 240px; max-height: 100vh;"
            icon=""
            :data="animeInfo"
            :props="{value:'id',label:'name'}"
            :filter-method="filter"
            :highlight-current="true"
            :height="defaultHeight"
            @node-click="clickNode"
        />
      </div>
      <!--      </el-select>-->

      <el-table v-loading="state.dataListLoading" :data="state.dataList" border
                @selection-change="state.dataListSelectionChangeHandle" style="width: 100%">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <!--              <el-table-column prop="id" label="" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="animeId" label="动漫id" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="name" label="角色名" header-align="center" align="center" min-width="100px"></el-table-column>
        <el-table-column prop="age" label="角色年龄" header-align="center" align="center" min-width="50px"></el-table-column>
        <el-table-column prop="birthday" label="角色出生日期" header-align="center" align="center" min-width="80px">
          <template #default="scope">
            <el-date-picker
                v-model="scope.row.birthday"
                readonly
                style="width: 100px;"
                size="small"
            />
          </template>
        </el-table-column>
        <el-table-column prop="description" label="角色描述" header-align="center" align="center" min-width="200px"></el-table-column>
        <el-table-column prop="imageUrl" label="角色图片" header-align="center" align="center" min-width="150px"></el-table-column>
        <el-table-column label="操作" fixed="right" header-align="center" align="center" width="150">
          <template v-slot="scope">
            <el-button v-if="state.hasPermission('anime:animecharacter:update')" type="primary" link
                       @click="addOrUpdateHandle(scope.row.id)">修改
            </el-button>
            <el-button v-if="state.hasPermission('anime:animecharacter:delete')" type="primary" link
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
    <add-or-update :anime="animeId" ref="addOrUpdateRef" @refreshDataList="state.getDataList">确定</add-or-update>
  </div>
</template>

<script lang="ts" setup>
import useView from "@/hooks/useView";
import {reactive, ref, toRefs, onMounted} from "vue";
import AddOrUpdate from "./module/animecharacter-add-or-update.vue";
import axios from "axios"
import {TreeNodeData} from "element-plus/es/components/tree-v2/src/types";
import {ElTreeV2} from "element-plus";

const view = reactive({
  deleteIsBatch: true,
  getDataListURL: "/anime/animecharacter/page",
  getDataListIsPage: true,
  exportURL: "/anime/animecharacter/export",
  deleteURL: "/anime/animecharacter",
  dataForm: {
    name: ""
  }
});
//设置tree-v2高度
let defaultHeight = ref({})


const state = reactive({...useView(view), ...toRefs(view)});
let animeInfo = reactive([])

const addOrUpdateRef = ref();
const addOrUpdateHandle = (id?: number) => {
  console.log(animeId,"out")

  addOrUpdateRef.value.init(id);
};

let query = ref('');
const treeRef = ref<InstanceType<typeof ElTreeV2>>()
let animeId = ref()

onMounted(async () => {
  defaultHeight.value = document.documentElement.clientHeight - 300
  await getAnimeInfoTree
  await state.getDataList()
})
const getAnimeInfoTree = axios.get('/api/anime/animeinfo/getInfoTree').then((res) => {
  animeInfo = res.data
})

const onQueryChanged = (query: string) => {
  // TODO: fix typing when refactor tree-v2
  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  treeRef.value!.filter(query)
}

const filter = (query:String , node:any) =>
  node.name.toLowerCase().includes(query.toLowerCase())

const clickNode = (data: TreeNodeData) => {
  animeId.value = data.id
}
</script>
