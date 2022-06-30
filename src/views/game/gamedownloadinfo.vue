<template>
  <div class="mod-game__gamedownloadinfo">
    <el-form :inline="true" :model="dataForm" @keyup.enter="getDataList()">
      <el-form-item>
        <el-button @click="getDataList()">{{ $t("query") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="info" @click="exportHandle()">{{ $t("export") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('game:gamedownloadinfo:save')" type="primary" @click="addOrUpdateHandle()">{{ $t("add") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('game:gamedownloadinfo:delete')" type="danger" @click="deleteHandle()">{{ $t("deleteBatch") }}</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="gameId" label="游戏id" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameDownloadUrl" label="下载链接" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameDownloadAndroidUrl" label="安卓链接" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameDownloadIosUrl" label="ios链接" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameDownloadTip" label="下载提示" header-align="center" align="center"></el-table-column>
      <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="hasPermission('game:gamedownloadinfo:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t("update") }}</el-button>
          <el-button v-if="hasPermission('game:gamedownloadinfo:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t("delete") }}</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination :current-page="page" :page-sizes="[10, 20, 50, 100]" :page-size="limit" :total="total" layout="total, sizes, prev, pager, next, jumper" @size-change="pageSizeChangeHandle" @current-change="pageCurrentChangeHandle"> </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script lang="ts">
import useView from "@/hooks/useView";
import { defineComponent, reactive, toRefs } from "vue";
import AddOrUpdate from "./gamedownloadinfo-add-or-update.vue";
export default defineComponent({
  components: {
    AddOrUpdate
  },
  setup() {
    const state = reactive({
      getDataListURL: "/game/gamedownloadinfo/page",
      getDataListIsPage: true,
      exportURL: "/game/gamedownloadinfo/export",
      deleteURL: "/game/gamedownloadinfo",
      deleteIsBatch: true,
      dataForm: {}
    });
    return { ...useView(state), ...toRefs(state) };
  },
  watch: {}
});
</script>
