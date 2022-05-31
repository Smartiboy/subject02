<template>
  <div class="container">
    <table>
      <tbody>
      <tr>
        <th v-for="item in columns" :key="item.key">{{ item.title }}</th>
      </tr>
      <tr v-for="item in data" :key="item.id">
        <td>{{ item.name }}</td>
        <td>{{ transformCh(item.type) }}</td>
        <td>{{ item.require ? '是' : '否' }}</td>
        <td>
          <div v-if="item.dateType !== ''">
            {{ transformDate(item.dateType) }}
          </div>
          <div v-else-if="item.selectOption.length > 0">
            {{item.selectOption.map(item => item.value).join('、')}}
          </div>
          <div v-else>
            无
          </div>
        </td>
        <td>
          <a-button type="link" @click="handleEdit(item)">编辑</a-button>
        </td>
      </tr>
      </tbody>
    </table>
    <EditModal v-if="showEditModal" :modal-form-visible.sync="showEditModal" :form-data.sync="formData"/>
  </div>
</template>
<script>
import EditModal from "@/views/ListData/EditModal";

export default {
  name: 'DataList',
  components: {
    EditModal
  },
  data() {
    return {
      showEditModal: false,
      formData: {},
      columns: [
        {
          title: '字段名称',
          key: 'name',
        },
        {
          title: '类型',
          key: 'age',
        },
        {
          title: '是否必填',
          key: 'require',
        },
        {
          title: '字段选项',
          key: 'option',
        },
        {
          title: '操作',
          key: 'action',
        },
      ],
      data: [
        {
          id: 1,
          name: '字段一',
          type: 'text',
          require: false,
          dateType: '',
          selectOption: []
        },
        {
          id: 2,
          name: '字段一',
          type: 'date',
          require: false,
          dateType: '',
          selectOption: []
        },
        {
          id: 3,
          name: '字段一',
          type: 'select',
          require: true,
          dateType: '',
          selectOption: []
        }
      ]
    }
  },
  computed: {},
  watch: {
    // TODO：实际开发中需要删除
    // 这边做监听只是为了能让修改的数据会更新视图，在实际开发中使用接口来获取数据时无需此操作
    formData(newvalue) {
      if (newvalue) {
        Object.assign(this.data.filter(item => {
          return item.id === newvalue.id
        })[0], newvalue)
      }

    }
  },
  mounted() {
  },
  methods: {
    handleEdit(item) {
      this.formData = item
      this.showEditModal = true
    },
    transformCh(val) {
      switch (val) {
        case 'text':
          return '单行文本'
        case 'date':
          return '日期'
        case 'select':
          return '单选下拉框'
        default:
          return '未知'
      }
    },
    transformDate(val) {
      switch (val) {
        case 'y-m':
          return '年-月'
        case 'y-m-d':
          return '年-月-日'
        case 'y-m-d h-m':
          return '年-月-日 时-分'
        default:
          return '未知'
      }
    }
  }
}
</script>

<style scoped lang="scss">
.container {
  display: flex;
  justify-content: center;

  table {
    width: 800px;
    border-collapse: collapse;
    text-align: center;
    margin-top: 40px;

    td, th {
      border: 1px solid #000;
    }
  }
}

</style>
