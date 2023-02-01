<template>
  <page-header-wrapper :title="false">
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="4" :sm="24">
              <a-form-item label="车牌号码">
                <a-input v-model="queryParam.phone" placeholder="请输入车牌号码"/>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="24">
              <a-form-item label="来源信息">
<!--                <a-input v-model="queryParam.description" placeholder="请输入车牌号码"/>-->
                <a-select v-model="queryParam.description" placeholder="请选择" >
                <a-select-option v-for="(item, index) in laiOption" :key="index">{{ item.label }}</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="24">
              <a-form-item label="联系方式">
                <a-input v-model="queryParam.id" placeholder="请输入联系方式"/>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="24">
              <a-form-item label="状态">
                <a-select v-model="queryParam.status" placeholder="请选择" >
                  <a-select-option v-for='(item, index) in statusMap' :key="index">{{  item.label }}</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="24">
              <a-button type="primary" @click="handleClick">查询</a-button>
              <a-button style="margin-left: 8px" @click="() => this.queryParam = {}">重置</a-button>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
      </div>
      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data="loadData"
        :rowSelection="null"
        :scroll="{x: height + 'px'}"
      >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
        <span slot="status" slot-scope="text">
          <a-badge :color="text | statusColor" :text="text | statusFilter" />
        </span>
        <span slot="description" slot-scope="text">
          {{ text | laiFilter }}
        </span>
        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleEdit(record)">编辑</a>
            <a-divider type="vertical" />
            <a @click="handleSub(record)">删除</a>
          </template>
        </span>
      </s-table>
      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        :statusMap="statusMap"
        :laiOption="laiOption"
        @cancel="handleCancel"
        @ok="handleOk"
      />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'

import CreateForm from './modules/CreateForm'

const columns = [
  {
    title: '序号',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '车牌号码',
    dataIndex: 'no'
  },
  {
    title: '来源信息',
    dataIndex: 'description',
    scopedSlots: { customRender: 'description' }
  },
  {
    title: '联系方式',
    dataIndex: 'callNo'
  },
  {
    title: '状态',
    dataIndex: 'status',
    scopedSlots: { customRender: 'status' }
  },
  {
    title: '反馈问题',
    dataIndex: 'updatedAt'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]
const laiOption = [
  { value: '0', label: '电话' },
  { value: '1', label: '微信' },
  { value: '2', label: '12345' },
  { value: '3', label: '其他' }
]
const statusMap = [
  {
    value: '0',
    label: '已办结',
    color: '#f50'
  },
  {
    value: '1',
    label: '已受理',
    color: '#2db7f5'
  },
  {
    value: '2',
    label: '已驳回',
    color: '#87d068'
  },
  {
    value: '3',
    label: '异常',
    color: '#108ee9'
  }
]
export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm
  },
  data () {
    this.columns = columns
    return {
      statusMap: statusMap,
      laiOption: laiOption,
      height: '',
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
       loadData: (parameter) => this.getLoadData(parameter),
      selectedRowKeys: [],
      selectedRows: [],
      params: {}
    }
  },
  filters: {
    statusFilter (type) {
      return statusMap[type].label
    },
    statusColor (type) {
      return statusMap[type].color
    },
    laiFilter (type) {
      return laiOption[type].label
    }
  },
  created () {
    getRoleList({ t: new Date() })
    this.height = document.body.clientHeight - 49 - 64 - 48
    // const requestParameters = Object.assign({}, parameter, this.queryParam)
    // this.$getAjax(url, param, (res) => {})
    /* this.queryParam = {
      ...this.queryParam,
      pageNo: 1,
      pageSize: 10
    }
    getServiceList(this.queryParam).then(res => {
      console.log(333, res.result.data)
      this.loadData = res.result.data
    }) */
    /* parameter => {
      const requestParameters = Object.assign({}, parameter, this.queryParam)
      return getServiceList(requestParameters)
        .then(res => {
          return res.result
        })
    } */
  },
  computed: {
    rowSelection () {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange
      }
    }
  },
  methods: {
    // 获取表格列表
    getLoadData (parameter) {
      this.params = parameter
      parameter = { ...parameter, ...this.queryParam }
      const requestParameters = Object.assign({}, parameter)
      console.log(32323, requestParameters)
      return getServiceList(requestParameters)
        .then(res => {
          console.log(33, res)
          return res.result
        })
      /* parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters)
          .then(res => {
            return res.result
          })
      } */
    },
    handleAdd () {
      this.mdl = null
      this.visible = true
    },
    handleEdit (record) {
      this.visible = true
      this.mdl = { ...record }
      this.$refs.createModal.editRecord(record)
    },
    handleOk () {
      this.$refs.table.refresh()
       const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        console.log(44, errors, values)
        if (!errors) {
          console.log('values', values)
          if (values.id > 0) {
            // 修改 e.g.
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel () {
      this.visible = false
      const form = this.$refs.createModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    handleSub (record) {
      var that = this
      that.$confirm({
        title: '删除提示',
        content: `删除本数据不可恢复，是否确定？`,
        okText: '确定',
        okType: 'danger',
        cancelText: '取消',
        onOk () {
        },
        onCancel () {
        }
      })
    },
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    },
    async handleClick () {
      await this.getLoadData(this.params)
    }
  }
}
</script>
