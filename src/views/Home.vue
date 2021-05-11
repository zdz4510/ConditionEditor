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
  data(){
    return {
      // 可选属性并附带其内部的类型
      fieldNameOptions: [
        { label: "a1", value: "a1"},
        { label: "a2", value: "a2"},
        { label: "a3", value: "a3"},
        { label: "a4", value: "a4"},
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
          type: 'any',
          condition: [
            {
              type: 'all',
              condition: [
                {
                  type: 'any',
                  condition: [
                    {
                      fieldName: "a1",
                      operator: '==',
                      value: '1',
                      fieldType: "string"
                    },
                    {
                      fieldName: "a1",
                      operator: '==',
                      value: '1',
                      fieldType: "number"
                    }
                  ]
                },
                {
                  fieldName: "a3",
                  operator: '>=',
                  value: '10',
                  fieldType: "date"
                }
              ]
            },
            {
              fieldName: "a4",
              operator: '<',
              value: '5',
              fieldType: "enum"
            }
          ]
        }
      ],
    }
  }
};
</script>
