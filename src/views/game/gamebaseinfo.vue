<template>
  <div class="mod-game__gamebaseinfo">
    <el-form :inline="true" :model="dataForm" @keyup.enter="getDataList()">
      <el-form-item>
        <el-button @click="getDataList()">{{ $t("query") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="info" @click="exportHandle()">{{ $t("export") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('game:gamebaseinfo:save')" type="primary" @click="addOrUpdateHandle()">{{ $t("add") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('game:gamebaseinfo:delete')" type="danger" @click="deleteHandle()">{{ $t("deleteBatch") }}</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="gameTitle" label="游戏标题" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameCustomTitle" label="自定义标题" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameTitleKeyword" label="标题关键词" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameVersion" label="游戏版本号" header-align="center" align="center"></el-table-column>
      <el-table-column prop="classifyId" label="分类id" header-align="center" align="center"></el-table-column>
      <el-table-column prop="classifyName" label="游戏分类名称" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gamePlatformType" label="游戏平台" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameSummaryn" label="摘要" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameInfo" label="游戏介绍" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameKeyWord" label="游戏关键词" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameSize" label="游戏大小" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gamePicUrl" label="游戏缩略图" header-align="center" align="center"></el-table-column>
      <el-table-column prop="gameMark" label="游戏评分" header-align="center" align="center"></el-table-column>
      <el-table-column prop="isUsed" label="是否启用" header-align="center" align="center"></el-table-column>
      <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="hasPermission('game:gamebaseinfo:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t("update") }}</el-button>
          <el-button v-if="hasPermission('game:gamebaseinfo:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t("delete") }}</el-button>
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
import AddOrUpdate from "./gamebaseinfo-add-or-update.vue";
export default defineComponent({
  components: {
    AddOrUpdate
  },
  setup() {
    const state = reactive({
      getDataListURL: "/game/gamebaseinfo/page",
      getDataListIsPage: true,
      exportURL: "/game/gamebaseinfo/export",
      deleteURL: "/game/gamebaseinfo",
      deleteIsBatch: true,
      dataForm: {}
    });
    return { ...useView(state), ...toRefs(state) };
  },
  watch: {}
});
</script>
