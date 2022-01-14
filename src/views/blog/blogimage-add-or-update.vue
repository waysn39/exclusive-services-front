<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
      <el-form-item label="图片类型" prop="imageType">
        <ren-select v-model="dataForm.imageType" dict-type="bolg_image_type" placeholder="图片类型"></ren-select>
      </el-form-item>
      <el-form-item label="图片路径" prop="imagePath">
        <el-input v-model="dataForm.imagePath" placeholder="图片路径"></el-input>
      </el-form-item>
    </el-form>
    <template v-slot:footer>
      <el-button @click="visible = false">{{ $t("cancel") }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t("confirm") }}</el-button>
      <waysn-upload :successHandleCallback="successback" @refreshDataList="getDataList"></waysn-upload>
    </template>
  </el-dialog>
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";
import baseService from "@/service/baseService";
import { useDebounce } from "@/utils/utils";
import { IObject } from "@/types/interface";
import waysnUpload from "@/components/waysn-upload/src/waysn-upload.vue";
export default defineComponent({
  components: { waysnUpload },
  setup() {
    return reactive({
      visible: false,
      dataForm: {
        id: "",
        creator: "",
        createDate: "",
        imageType: "",
        imagePath: ""
      }
    });
  },
  computed: {
    dataRule() {
      return {
        imageType: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        imagePath: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }]
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
      baseService.get("/blog/blogimage/" + this.dataForm.id).then((res) => {
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
        (!this.dataForm.id ? baseService.post : baseService.put)("/blog/blogimage", this.dataForm).then((res) => {
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
    },
    successback(attachmentInfo: IObject) {
      this.dataForm.imagePath = attachmentInfo.attachPath;
    }
  }
});
</script>
