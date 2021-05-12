<template>
  <div class="home">
    <ConditionEditor
      ref="childConditionEditor"
      :value="conditions"
      :fieldNameOptions="fieldNameOptions"
      :operatorOptions="operatorOptions"
      @onChange="handleLog"
      @onChangeField="handleChangeField"
    />
    <el-button type="primary" @click="handlerShow"
      >查看结果（控制台打印结果）</el-button
    >
  </div>
</template>

<script>
// @ is an alias to /src
import ConditionEditor from "@/components/ConditionEditor/ConditionEditor.vue";

export default {
  name: "Home",
  components: {
    ConditionEditor,
  },
  data() {
    return {
      // 可选属性并附带其内部的类型
      fieldNameOptions: [
        { label: "a1", value: "a1" },
        { label: "a2", value: "a2" },
        { label: "a3", value: "a3" },
        { label: "a4", value: "a4" },
      ],
      operatorOptions: [
        { label: "=", value: "==" },
        { label: ">", value: ">" },
        { label: "<", value: "<" },
        { label: ">=", value: ">=" },
      ],
      conditions: [
        // 树形数据
        {
          type: "any",
          condition: [
            {
              type: "all",
              condition: [
                {
                  type: "any",
                  condition: [
                    {
                      fieldName: "a1",
                      operator: "==",
                      value: "1",
                      fieldType: "string",
                    },
                    {
                      fieldName: "a1",
                      operator: "==",
                      value: "1",
                      fieldType: "number",
                    },
                  ],
                },
                {
                  fieldName: "a3",
                  operator: ">=",
                  value: "10",
                  fieldType: "date",
                },
              ],
            },
            {
              fieldName: "a4",
              operator: "<",
              value: "5",
              fieldType: "enum",
            },
          ],
        },
      ],
    };
  },
  methods: {
    handleLog(val) {
      console.log("触发三");
      console.log(val, "val");
    },
    // 改变内部属性时触发
    handleChangeField({ key, field, position, callback }) {
      console.log(key, field, position, callback, "内部属性已改变");
    },
    handlerShow() {
      var data = this.$refs.childConditionEditor.handleLogNow();
      console.log(data, "当前配置的规则结构");
    },
  },
};
</script>
