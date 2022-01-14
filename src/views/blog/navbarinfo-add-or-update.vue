<template>
  <el-dialog v-model="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataFormRef" @keyup.enter="dataFormSubmitHandle()" label-width="120px">
      <el-form-item label="节点编码" prop="navbarCode">
        <el-input v-model="dataForm.navbarCode" placeholder="节点编码"></el-input>
      </el-form-item>
      <el-form-item label="导航名称" prop="navbarName">
        <el-input v-model="dataForm.navbarName" placeholder="导航名称"></el-input>
      </el-form-item>
      <el-form-item label="连接" prop="navbarLink">
        <el-input v-model="dataForm.navbarLink" placeholder="连接"></el-input>
      </el-form-item>
      <el-form-item label="图标" prop="navbarIconClass">
        <el-input v-model="dataForm.navbarIconClass" placeholder="图标"></el-input>
      </el-form-item>
      <el-form-item label="父节点" prop="parentCode">
        <el-input v-model="dataForm.parentCode" placeholder="父节点"></el-input>
      </el-form-item>

      <el-form-item label="是否启用" prop="isUsed">
        <el-checkbox v-model="dataForm.isUsed"> </el-checkbox>
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
        navbarCode: "",
        navbarName: "",
        navbarLink: "",
        navbarIconClass: "",
        parentCode: "",
        isUsed: true,
        creator: "",
        createDate: ""
      }
    });
  },
  computed: {
    dataRule() {
      return {
        navbarCode: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        navbarName: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        navbarIconClass: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }],
        isUsed: [{ required: true, message: this.$t("validate.required"), trigger: "blur" }]
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
      baseService.get("/blog/navbarinfo/" + this.dataForm.id).then((res) => {
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
        (!this.dataForm.id ? baseService.post : baseService.put)("/blog/navbarinfo", this.dataForm).then((res) => {
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
