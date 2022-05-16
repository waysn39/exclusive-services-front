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
        <el-checkbox-group v-model="dataForm.gamePlatformType">
          <el-checkbox label="启用" name="type"></el-checkbox>
          <el-checkbox label="禁用" name="type"></el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item label="下载跳转类型" prop="downloadJumpType">
        <ren-radio-group v-model="dataForm.downloadJumpType" dict-type="game_down_jump"></ren-radio-group>
      </el-form-item>
      <el-form-item label="游戏下载地址" prop="gameDownloadUrl">
        <el-input v-model="dataForm.gameDownloadUrl" placeholder="游戏下载地址"></el-input>
      </el-form-item>
      <el-form-item label="游戏安卓下载地址" prop="gameDownloadAndroidUrl">
        <el-input v-model="dataForm.gameDownloadAndroidUrl" placeholder="游戏安卓下载地址"></el-input>
      </el-form-item>
      <el-form-item label="游戏苹果下载地址" prop="gameDownloadIosUrl">
        <el-input v-model="dataForm.gameDownloadIosUrl" placeholder="游戏苹果下载地址"></el-input>
      </el-form-item>
      <el-form-item label="游戏下载提示" prop="gameDownloadInfo" style="height: 300px">
        <editor v-model="dataForm.gameDownloadInfo" :init="{ height: 300 }"></editor>
      </el-form-item>
      <el-form-item label="摘要" prop="gameSummaryn">
        <el-input type="textarea" v-model="dataForm.gameSummaryn"></el-input>
      </el-form-item>
      <el-form-item label="游戏介绍" prop="gameInfo" style="height: 300px">
        <editor v-model="dataForm.gameInfo" :init="{ height: 300 }"></editor>
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
        <el-radio-group v-model="dataForm.isUsed">
          <el-radio :label="0">启用</el-radio>
          <el-radio :label="1">禁用</el-radio>
        </el-radio-group>
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
        downloadJumpType: "",
        gameDownloadUrl: "",
        gameDownloadAndroidUrl: "",
        gameDownloadIosUrl: "",
        gameDownloadInfo: "",
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
      return {
        gameTitle: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        gameCustomTitle: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        classifyName: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        gamePlatformType: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        downloadJumpType: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        gameDownloadInfo: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        gameSummaryn: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        gameInfo: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }]
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
      baseService.get("/game/gameinfo/" + this.dataForm.id).then((res) => {
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
        (!this.dataForm.id ? baseService.post : baseService.put)("/game/gameinfo", this.dataForm).then((res) => {
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
