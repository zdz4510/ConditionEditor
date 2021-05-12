<script>
import ConditionItem from "./childCamps/ConditionItem.vue";
// import { cloneDeep } from "lodash";
// 用本地的深拷贝，减少依赖导入
import { deepClone } from "@/utils/common.js";

export default {
  name: "ConditionEditor",
  props: {
    value: {
      type: Array,
      default: () => [],
    },
    /**
     * 字段名下拉框的所有数据
     */
    fieldNameOptions: {
      type: Array,
      default: () => [
        { label: "a1", value: "a1", type: "string" },
        { label: "a2", value: "a2", type: "number" },
        { label: "a3", value: "a3", type: "date" },
        { label: "a4", value: "a4", type: "enum" },
      ],
    },
    /**
     * 关系运算符下拉框的所有数据
     */
    operatorOptions: {
      type: Array,
      default: () => [
        { label: "=", value: "==" },
        { label: ">", value: ">" },
        { label: "<", value: "<" },
        { label: ">=", value: ">=" },
      ],
    },
  },
  components: {
    ConditionItem,
  },
  data() {
    return {
      conditionValue: [],
    };
  },
  methods: {
    // 组件内部打印当前数据
    handleLogNow() {
      // 返回规则配置的最终结果父组件
      return this.conditionValue;
    },
    /**
     * 修改条件的逻辑类型
     */
    // changeType ({ key }, position) {
    //   console.log(key,position,"11111")
    //   this.conditionValue = this.literator(this.conditionValue, position, 'type', key)
    //   this.$emit('change', this.conditionValue);
    // },
    changeType(key, position) {
      this.conditionValue = this.literator(
        this.conditionValue,
        position,
        "type",
        key
      );
      this.$emit("change", this.conditionValue);
    },
    /**
     * 修改条件字段
     * @param {*} key 字段的值
     * @param {*} field 修改的字段的名字 fieldName| operator |value
     * @param {Array} position 修改的节点位于整个树节点的位置
     */
    changeField({ key, field, position }) {
      this.conditionValue = this.literator(
        this.conditionValue,
        position,
        field,
        key
      );
      // 下面的方法不触发也可以
      this.$emit("changeField", {
        key,
        field,
        position,
        callback: (customTag) => {
          this.conditionValue = this.literator(
            this.conditionValue,
            position,
            "customTag",
            customTag
          );
        },
      });
      this.$emit("change", this.conditionValue);
    },
    /**
     * 修改条件的方法
     */
    literator(value, position, field, key) {
      let cacheValue = deepClone(value);
      // 通过position进行递归调用去设置新的值
      if (position.length > 1) {
        let cachePosition = deepClone(position);
        cachePosition.shift();
        cacheValue[position[0]].condition = this.literator(
          cacheValue[position[0]].condition,
          cachePosition,
          field,
          key
        );
      } else {
        console.log("已经是最后一层");
        cacheValue[position[0]][field] = key;
      }
      return cacheValue;
    },
    /**
     * 删除条件
     */
    handleDelete(position) {
      this.conditionValue = this.deleteLiterator(this.conditionValue, position);
      if (this.conditionValue.length === 0) {
        this.conditionValue = [
          {
            type: "all",
            condition: [],
          },
        ];
      }
      this.$emit("change", this.conditionValue);
    },
    /**
     * 删除条件/条件组的方法
     */
    deleteLiterator(value, position) {
      let cacheValue = deepClone(value);
      if (position.length > 1) {
        let cachePosition = deepClone(position);
        cachePosition.shift();
        let child = this.deleteLiterator(
          cacheValue[position[0]].condition,
          cachePosition
        );
        // 如果条件组下没有条件，则删除组
        if (child.length === 0) {
          cacheValue.splice(position[0], 1);
        } else {
          cacheValue[position[0]].condition = child;
        }
      } else {
        // 删除指定的条件
        cacheValue.splice(position[0], 1);
      }
      return cacheValue;
    },
    /**
     * 新增条件
     */
    handleAdd({ position, type }) {
      this.conditionValue = this.addLiterator(
        this.conditionValue,
        position,
        type
      );
    },
    /**
     * 添加条件/条件组的方法
     */
    addLiterator(value, position, type) {
      let cacheValue = deepClone(value);
      if (position.length > 0) {
        let cachePosition = deepClone(position);
        cachePosition.shift();
        cacheValue[position[0]].condition = this.addLiterator(
          cacheValue[position[0]].condition,
          cachePosition,
          type
        );
      } else {
        if (!type) {
          // 添加一行条件
          cacheValue.push({
            fieldName: "",
            operator: "",
            value: "",
          });
        } else {
          // 添加一行条件
          cacheValue.push({
            type: "all",
            condition: [
              {
                fieldName: "",
                operator: "",
                value: "",
              },
            ],
          });
        }
      }
      return cacheValue;
    },
    /**
     * arr转string
     */
    transferValue() {
      let valueFormatter = this.conditionLiterator(this.conditionValue);
      console.log(valueFormatter);
    },
    /**
     * 条件编辑器格式化 - arr转string
     */
    conditionLiterator(arr, type = "all") {
      return deepClone(arr)
        .map((item) => {
          if (item.type) {
            return ` ( ${this.conditionLiterator(
              item.condition,
              item.type
            )} ) `;
          } else {
            return ` ${item.fieldName} ${item.operator} ${item.value} `;
          }
        })
        .join(type === "all" ? "&&" : "||");
    },
    renderGroup(condition, type = "all", position) {
      return (
        <div class="condition-group">
          {/**按钮栏 */}
          <div class="condition-group-controller">
            <el-select
              style="width:100px;"
              value={type}
              onChange={(value) => {
                console.log(value, "12344");
                this.changeType(value, position);
              }}
            >
              <el-option key="all" value="all">
                all
              </el-option>
              <el-option key="any" value="any">
                any
              </el-option>
            </el-select>
            <div>
              <el-button
                type="primary"
                style="marginRight:5px"
                onClick={() => {
                  this.handleAdd({ position });
                }}
              >
                添加条件
              </el-button>
              <el-button
                type="primary"
                onClick={() => {
                  this.handleAdd({ position, type: "group" });
                }}
              >
                添加一组条件
              </el-button>
            </div>
          </div>
          {/** 内容栏 */}
          <div>
            {condition.map((item, index) => {
              if (item.type) {
                return this.renderGroup(item.condition, item.type, [
                  ...position,
                  index,
                ]);
              }
              return (
                <ConditionItem
                  value={item}
                  fieldNameOptions={this.fieldNameOptions}
                  operatorOptions={this.operatorOptions}
                  position={[...position, index]}
                  onChangeField={this.changeField}
                  onHandleDelete={this.handleDelete}
                ></ConditionItem>
              );
            })}
          </div>
        </div>
      );
    },
  },
  mounted() {},
  created() {
    console.log(this.fieldNameOptions, "fieldNameOptions");
  },
  watch: {
    value: {
      handler(val) {
        if (val.length > 0) {
          this.conditionValue = val;
        }
      },
      immediate: true,
      deep: true,
    },
  },
  render() {
    return (
      <div>
        {this.conditionValue.map((item, index) => {
          return this.renderGroup(item.condition, item.type, [index]);
        })}
      </div>
    );
  },
};
</script>

<style scoped>
.condition-group {
  background-color: rgba(253, 248, 234, 0.4);
  border: 1px solid #ede3ca;
  padding: 10px;
  margin: 10px;
}
.condition-group-controller {
  display: flex;
  margin: 10px;
  justify-content: space-between;
}
</style>
