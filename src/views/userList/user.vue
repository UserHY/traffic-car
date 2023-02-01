<template>
  <page-header-wrapper :title="false">
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="6" :sm="24">
              <a-form-item label="用户名">
                <a-input v-model="queryParam.user" placeholder="请输入用户名"/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="24">
              <a-form-item label="姓名">
                <a-input v-model="queryParam.name" placeholder="请输入姓名"/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="24">
              <a-button type="primary" @click="handleClick">查询</a-button>
              <a-button style="margin-left: 8px" @click="handleReset">重置</a-button>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
      </div>
<!--      表格-->
      <a-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data-source="loadData"
        :rowSelection="null"
      >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
        <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
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
      </a-table>
      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
      />
    </a-card>
  </page-header-wrapper>
</template>
<script>
import { Ellipsis, STable } from '@/components'
import CreateForm from '@/views/userList/modules/CreateForm'
import { getUserData } from '@/api/manage'
const columns = [
  {
    title: '序号',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '用户名',
    dataIndex: 'user'
  },
  {
    title: '姓名',
    dataIndex: 'name'
  },
  {
    title: '联系方式',
    dataIndex: 'callNo'
  },
  {
    title: '备注',
    dataIndex: 'remark'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]
export default {
  components: {
    STable,
    Ellipsis,
    CreateForm
  },
  data () {
    return {
      loadData: [],
      columns: columns,
      // 查询参数
      queryParam: {
        pageSize: 10,
        pageNo: 1
      },
      visible: false,
      confirmLoading: false,
      mdl: {},
      selectedRowKeys: [],
      selectedRows: []
    }
  },
  computed: {
    rowSelection () {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange
      }
    }
  },
  created () {
    this.getLoadData()
  },
  methods: {
    getLoadData (params) {
      console.log(339, params)
      if (params) {
        this.$nextTick(() => {
          getUserData(params).then(res => {
            this.loadData = this.loadData.filter((_) => _.user === params.user)
          })
        })
      } else {
        this.$nextTick(() => {
          getUserData(params).then(res => {
            this.loadData = res.result
          })
        })
      }
    },
    handleCancel () {
      this.visible = false
      const form = this.$refs.createModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    handleOk () {},
    handleEdit (record) {
      this.visible = true
      this.mdl = { ...record }
      this.$refs.createModal.editRecord(record)
    },
    handleAdd () {
      this.mdl = null
      this.$refs.createModal.addvisible()
      this.visible = true
    },
    handleClick () {
      console.log(33, this.queryParam)
      this.getLoadData(this.queryParam)
    },
    handleReset () {
      this.queryParam = {}
      this.getLoadData()
    }
  }
}
</script>
<style lang="less" scoped>
</style>
