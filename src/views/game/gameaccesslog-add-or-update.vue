<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
      <el-form-item label="游戏id" prop="gameId">
        <el-input v-model="dataForm.gameId" placeholder="游戏id"></el-input>
      </el-form-item>
      <el-form-item label="分类id" prop="classifyId">
        <el-input v-model="dataForm.classifyId" placeholder="分类id"></el-input>
      </el-form-item>
      <el-form-item label="浏览次数" prop="viewCount">
        <el-input v-model="dataForm.viewCount" placeholder="浏览次数"></el-input>
      </el-form-item>
      <el-form-item label="下载次数" prop="downloadCount">
        <el-input v-model="dataForm.downloadCount" placeholder="下载次数"></el-input>
      </el-form-item>
    </el-form>
    <template v-slot:footer>
      <el-button @click="visible = false">{{ $t("cancel") }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t("confirm") }}</el-button>
    </template>
  </el-dialog>
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";
import baseService from "@/service/baseService";
import { useDebounce } from "@/utils/utils";
export default defineComponent({
  setup() {
    return reactive({
      visible: false,
      dataForm: {
        id: "",
        creator: "",
        createDate: "",
        updater: "",
        updateDate: "",
        gameId: "",
        classifyId: "",
        viewCount: "",
        downloadCount: ""
      }
    });
  },
  computed: {
    dataRule() {
      return {
        gameId: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        classifyId: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }]
      };
    }
  },
  created() {
    this.dataFormSubmitHandle = useDebounce(this.dataFormSubmitHandle);
  },
  methods: {
    init() {
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataFormRef"].resetFields();
        if (this.dataForm.id) {
          this.getInfo();
        }
      });
    },
    // 获取信息
    getInfo() {
      baseService.get("/game/gameaccesslog/" + this.dataForm.id).then((res) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg);
        }
        this.dataForm = res.data;
      });
    },
    // 表单提交
    dataFormSubmitHandle() {
      this.$refs["dataFormRef"].validate((valid: boolean) => {
        if (!valid) {
          return false;
        }
        (!this.dataForm.id ? baseService.post : baseService.put)("/game/gameaccesslog", this.dataForm).then((res) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg);
          }
          this.$message({
            message: this.$t("prompt.success"),
            type: "success",
            duration: 500,
            onClose: () => {
              this.visible = false;
              this.$emit("refreshDataList");
            }
          });
        });
      });
    }
  }
});
</script>
