<template>
  <a-modal
      :title="title"
      :visible="modalFormVisible"
      :confirm-loading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
      cancelText="取消"
      okText="确定"
  >
    <a-form :form="form" :label-col="{ span: 4 }" :wrapper-col="{ span: 16 }">
      <a-form-item label="字段名称">
        <a-input v-model="form.name"/>
      </a-form-item>
      <a-form-item label="字段类型">
        <a-select
            v-model="form.type"
            @change="handleTypeSelectChange"
        >
          <a-select-option value="text">
            单行文本
          </a-select-option>
          <a-select-option value="date">
            日期
          </a-select-option>
          <a-select-option value="select">
            单选下拉框
          </a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="日期类型" v-if="form.type === 'date'">
        <a-radio-group @change="dateTypeChange">
          <a-radio :style="radioStyle" value="y-m">
            年-月
          </a-radio>
          <a-radio :style="radioStyle" value="y-m-d">
            年-月-日
          </a-radio>
          <a-radio :style="radioStyle" value="y-m-d h-m">
            年-月-日 时-分
          </a-radio>
        </a-radio-group>
      </a-form-item>
      <a-form-item label="选项" v-else-if="form.type === 'select'">
        <a-col class="select-item" v-for="(item,index) in selectOption" :key="index">
          <a-input  v-model="item.value"/>
          <a-button type="primary" class="select-item-btn" v-if="index === 0" @click="handleAdd()">添加</a-button>
          <a-button v-else type="danger" class="select-item-btn" @click="handleDelete(index)">删除</a-button>
        </a-col>
      </a-form-item>
      <a-form-item label="是否必填">
        <a-radio-group v-model="form.require">
          <a-radio :value="true">
            是
          </a-radio>
          <a-radio :value="false">
            否
          </a-radio>
        </a-radio-group>
      </a-form-item>
    </a-form>
  </a-modal>
</template>

<script>
export default {
  name: 'EditModal',
  components: {},
  props: {
    modalFormVisible: {
      type: Boolean,
      required: true
    },
    formData: {
      type: Object,
      required: true
    },
  },
  data() {
    return {
      title: '编辑',
      visible: false,
      confirmLoading: false,
      labelCol: {span: 4},
      wrapperCol: {span: 14},
      form: {},
      radioStyle: {
        display: 'block',
        height: '30px',
        lineHeight: '30px',
      },
      selectOption: [
        {
          value: '选项1'
        },
        {
          value: '选项2'
        }
      ],
      // 选项计数值
      selectOptionIndex: 0
    }
  },
  computed: {},
  watch: {},
  mounted() {
    // 此处必须要对传递过来的对象进行深度拷贝，否则会修改原对象的值（拷贝的是地址）
    this.form = JSON.parse(JSON.stringify(this.formData))
    this.selectOptionIndex = this.selectOption.length+1
  },
  methods: {
    handleCancel() {
      this.$emit('update:modalFormVisible', false)
      this.$emit('update:formData', null)
    },
    handleOk() {
      try {
        // TODO: 可以将form数据传给后台，传递成功则调用更新列表的方法获取最新数据
        if (this.form.type === 'select') {
          this.form.selectOption = this.selectOption
        }
        this.$emit('update:formData', this.form)
        this.$emit('update:modalFormVisible', false)
      } catch (e) {
        console.error(e)
      }
    },
    dateTypeChange(e) {
      this.form.dateType = e.target.value
    },
    handleTypeSelectChange() {
      // 恢复初始值
      this.form.dateType = ''
      this.selectOptionIndex = this.selectOption.length+1
      this.selectOption = [
        {
          value: '选项1'
        },
        {
          value: '选项2'
        }
      ]
    },
    handleAdd() {
      this.selectOption.push({
        value: `选项${this.selectOptionIndex}`
      })
      this.selectOptionIndex++
    },
    handleDelete(index) {
      this.selectOption.splice(index, 1)
    }
  }
}
</script>

<style scoped lang="scss">
.select-item {
  display: flex;
  margin-bottom: 10px;

  .select-item-btn {
    margin-left: 5px;
  }
}
</style>
