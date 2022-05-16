<template>
  <el-checkbox-group v-model="value" @change="$emit('update:modelValue', $event)">
    <el-checkbox :label="data.dictValue" v-for="data in dataList" :key="data.dictValue">{{ data.dictLabel }}</el-checkbox>
  </el-checkbox-group>
</template>
<script lang="ts">
import { getDictDataList } from "@/utils/utils";
import { computed, defineComponent } from "vue";
import { useStore } from "vuex";
export default defineComponent({
  name: "RenCheckBoxGroup",
  props: {
    modelValue: [Number, String],
    dictType: String
  },
  setup(props) {
    const store = useStore();
    return {
      value: computed(() => `${props.modelValue}`),
      dataList: getDictDataList(store.state.dicts, props.dictType)
    };
  }
});
</script>
