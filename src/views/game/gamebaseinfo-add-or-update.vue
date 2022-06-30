<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
      <el-form-item label="游戏标题" prop="gameTitle">
        <el-input v-model="dataForm.gameTitle" placeholder="游戏标题"></el-input>
      </el-form-item>
      <el-form-item label="自定义标题" prop="gameCustomTitle">
        <el-input v-model="dataForm.gameCustomTitle" placeholder="自定义标题"></el-input>
      </el-form-item>
      <el-form-item label="标题关键词" prop="gameTitleKeyword">
        <el-input v-model="dataForm.gameTitleKeyword" placeholder="标题关键词"></el-input>
      </el-form-item>
      <el-form-item label="游戏版本号" prop="gameVersion">
        <el-input v-model="dataForm.gameVersion" placeholder="游戏版本号"></el-input>
      </el-form-item>
      <el-form-item label="分类id" prop="classifyId">
        <el-input v-model="dataForm.classifyId" placeholder="分类id"></el-input>
      </el-form-item>
      <el-form-item label="游戏分类名称" prop="classifyName">
        <el-input v-model="dataForm.classifyName" placeholder="游戏分类名称"></el-input>
      </el-form-item>
      <el-form-item label="游戏平台" prop="gamePlatformType">
        <el-input v-model="dataForm.gamePlatformType" placeholder="游戏平台"></el-input>
      </el-form-item>
      <el-form-item label="摘要" prop="gameSummaryn" style="height: 300px">
        <editor v-model="dataForm.gameSummaryn" :init="{ height: 300 }"></editor>
      </el-form-item>
      <el-form-item label="游戏介绍" prop="gameInfo">
        <el-input v-model="dataForm.gameInfo" placeholder="游戏介绍"></el-input>
      </el-form-item>
      <el-form-item label="游戏关键词" prop="gameKeyWord">
        <el-input v-model="dataForm.gameKeyWord" placeholder="游戏关键词"></el-input>
      </el-form-item>
      <el-form-item label="游戏大小" prop="gameSize">
        <el-input v-model="dataForm.gameSize" placeholder="游戏大小"></el-input>
      </el-form-item>
      <el-form-item label="游戏缩略图" prop="gamePicUrl">
        <el-input v-model="dataForm.gamePicUrl" placeholder="游戏缩略图"></el-input>
      </el-form-item>
      <el-form-item label="游戏评分" prop="gameMark">
        <el-input v-model="dataForm.gameMark" placeholder="游戏评分"></el-input>
      </el-form-item>
      <el-form-item label="是否启用" prop="isUsed">
        <el-input v-model="dataForm.isUsed" placeholder="是否启用"></el-input>
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
import Tinymce from "@/components/tinymce";
export default defineComponent({
  components: { editor: Tinymce },
  setup() {
    return reactive({
      visible: false,
      dataForm: {
        id: "",
        gameTitle: "",
        gameCustomTitle: "",
        gameTitleKeyword: "",
        gameVersion: "",
        classifyId: "",
        classifyName: "",
        gamePlatformType: "",
        gameSummaryn: "",
        gameInfo: "",
        gameKeyWord: "",
        gameSize: "",
        gamePicUrl: "",
        gameMark: "",
        isUsed: "",
        creator: "",
        createDate: "",
        updater: "",
        updateDate: ""
      }
    });
  },
  computed: {
    dataRule() {
      return {};
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
      baseService.get("/game/gamebaseinfo/" + this.dataForm.id).then((res) => {
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
        (!this.dataForm.id ? baseService.post : baseService.put)("/game/gamebaseinfo", this.dataForm).then((res) => {
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
