<script>
export default {
  name: 'ConditionItem',
  props: {
    value: {
      type: Object,
      default: () => ({})
    },
    position: {
      type: Array,
      default: () => []
    },
    /**
    * 字段名下拉框的所有数据
    */
    fieldNameOptions: {
      type: Array,
      default: () => [{ label: "a1", value: 'a1' }, { label: "a2", value: 'a2' }, { label: "a3", value: 'a3' }, { label: "a4", value: 'a4' }]
    },
    /**
     * 数字关系运算符下拉框的所有数据
     */
    operatorOptions: {
      type: Array,
      default: () => []
    },
  },
  data () {
    return {
      dateOptions:[{ label: "=", value: '==' }, { label: ">", value: '>' }, { label: "<", value: '<' }, { label: ">=", value: '>=' }, { label: "<=", value: '<=' }, { label: "范围", value: '范围' }],
      enumOptions:[{ label: "=", value: '==' }, { label: ">", value: '>' }, { label: "<", value: '<' }, { label: ">=", value: '>=' }, { label: "<=", value: '<=' }]
    }
  },
  created(){
    console.log(this.fieldNameOptions,"fieldNameOptions2")
  },
  methods: {
    handleChange (e, field) {
      let key = typeof e === 'string' ? e : e.target.value
      console.log(key,"11111")
      this.$emit('changeField', { key, field, position: this.position })
    },
    /**
     * 渲染第三项
     */
    renderThird () {
      // 未用到customtag标签
      // if (this.value.customTag) {
      //   let { tag, props, on, style, domProps } = { ...this.value.customTag }
      //   return <this.value.customTag.tag {...
      //     {
      //       props: {
      //         value: this.value.value,
      //         ...props
      //       },
      //       on: {
      //         change: ($event) => { this.handleChange($event, 'value') },
      //         ...on
      //       },
      //       domProps: {
      //         ...domProps
      //       },
      //       style: {
      //         ...style
      //       }
      //     }} />
      // }
      if(this.value.operator==="范围"){
        return <span>
            <el-input
            style='width:120px;marginRight:5px'
            value={this.value.value}
            onInput={(e) => { 
              this.handleChange(e, 'value');
            }}/>
            <el-input
            style='width:120px;marginRight:5px'
            value={this.value.value2}
            onInput={(e) => { 
              this.handleChange(e, 'value2');
            }}/>
        </span>
      }
      return <el-input
        style='width:120px;marginRight:5px'
        value={this.value.value}
        onInput={(e) => { 
          this.handleChange(e, 'value');
        }}
      />
    },

    // 传可遍历的下拉数据
    renderSelectOption(list){
      return list.map((item) => (
          <el-option
            key={item.value}
            value={item.value}
          >{item.label}</el-option>
      ))
    },

    // 判断当前选择的属性类型
    renderOperatorByType(value){
      // 通过属性名判断属性的类型进而展示不同的选项 以下是假的一种，真实清况需实际去渠道属性名对应的值的类型，在去处理
      if(value === "a1" || value === 'a4'){
        return this.operatorOptions.map((item) => (
          <el-option
            key={item.value}
            value={item.value}
            >
            {item.label}
          </el-option>
        ))
      }else if(value ==='a3'){
        return this.dateOptions.map((item) => (
          <el-option
            key={item.value}
            value={item.value}
            >
            {item.label}
          </el-option>
        ))
      }else{
        return this.enumOptions.map((item) => (
          <el-option
            key={item.value}
            value={item.value}
            >
            {item.label}
          </el-option>
        ))
      }
    },

  },
  //        <a-select
  //           style='width:100px;marginRight:5px'
  //           options={this.fieldNameOptions}
  //           default-value={this.value.fieldName}
  //           onChange={($event) => { this.handleChange($event, 'fieldName') }}
  //         />
  //         
  //         <a-select
  //           style='width:100px;marginRight:5px'
  //           options={this.operatorOptions}
  //           default-value={this.value.operator}
  //           onChange={($event) => { this.handleChange($event, 'operator') }}
  //         />

  render () {
    return (
      <div class='condition-item'>
        {/**字段选择栏 */}
        <div>
          <el-select style='width:100px;marginRight:5px' value={this.value.fieldName} onChange={(value)=>{
              this.handleChange(value, 'fieldName')
              }}>
              {this.renderSelectOption(this.fieldNameOptions)}
          </el-select>
          <el-select style='width:100px;marginRight:5px' value={this.value.operator} onChange={(value)=>{
              this.handleChange(value, 'operator')
              }}>
              {this.renderOperatorByType(this.value.fieldName)}
          </el-select>
          {this.renderThird()}

        </div>
        {/**按钮栏 */}
        <div>
          <el-button onClick={() => { this.$emit('handleDelete', this.position) }} type='danger'>删除</el-button>
        </div>
      </div>
    )
  }
}
</script>
<style scoped>
.condition-item {
  background-color: white;
  display: flex;
  justify-content: space-between;
  margin: 10px;
}
</style>