<template>
  <div class="mod-sys__attachment">
    <el-form :inline="true" :model="dataForm" @keyup.enter="getDataList()">
      <el-form-item>
        <el-button @click="getDataList()">{{ $t("query") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="info" @click="exportHandle()">{{ $t("export") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('sys:attachment:save')" type="primary" @click="addOrUpdateHandle()">{{ $t("add") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="uploadHandle()">{{ $t("oss.upload") }}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button v-if="hasPermission('sys:attachment:delete')" type="danger" @click="deleteHandle()">{{ $t("deleteBatch") }}</el-button>
      </el-form-item>
    </el-form>
    <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="attachName" label="附件名称" header-align="center" align="center"></el-table-column>
      <el-table-column prop="attachPath" label="路径" header-align="center" align="center"></el-table-column>
      <el-table-column prop="attachLength" label="数据长度" header-align="center" align="center"></el-table-column>
      <el-table-column prop="attachExt" label="后缀" header-align="center" align="center"></el-table-column>
      <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
        <template v-slot="scope">
          <el-button v-if="hasPermission('sys:attachment:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t("update") }}</el-button>
          <el-button v-if="hasPermission('sys:attachment:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t("delete") }}</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination :current-page="page" :page-sizes="[10, 20, 50, 100]" :page-size="limit" :total="total" layout="total, sizes, prev, pager, next, jumper" @size-change="pageSizeChangeHandle" @current-change="pageCurrentChangeHandle"> </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update
    ><!-- 弹窗, 上传文件 -->
    <upload v-if="uploadVisible" ref="upload" @refreshDataList="getDataList"></upload>
  </div>
</template>

<script lang="ts">
import useView from "@/hooks/useView";
import { defineComponent, reactive, toRefs } from "vue";
import AddOrUpdate from "./attachment-add-or-update.vue";
import Upload from "./attachment-upload.vue";
export default defineComponent({
  components: {
    AddOrUpdate,
    Upload
  },
  setup() {
    const state = reactive({
      getDataListURL: "/sys/attachment/page",
      getDataListIsPage: true,
      exportURL: "/sys/attachment/export",
      deleteURL: "/sys/attachment",
      deleteIsBatch: true,
      dataForm: {},
      uploadVisible: false
    });
    return { ...useView(state), ...toRefs(state) };
  },
  methods: {
    // 上传文件
    uploadHandle() {
      this.uploadVisible = true;
      this.$nextTick(() => {
        this.$refs.upload.init();
      });
    }
  },
  watch: {}
});
</script>
