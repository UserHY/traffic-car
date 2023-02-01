<template>
  <a-modal
    :title="record.id ? '编辑用户' : '新建用户'"
    :width="800"
    :visible="visible"
    :confirmLoading="loading"
    @ok="() => { $emit('ok') }"
    @cancel="() => { $emit('cancel') }"
  >
    <a-spin :spinning="loading">
      <a-form :form="form" :label-col="{ span: 5 }" :wrapper-col="{ span: 18 }">
        <a-row>
          <a-col :span="12">
            <a-form-item label="用户名">
              <a-input v-decorator="['user', {rules: [{required: true, message: '请输入用户名'}]}]" placeholder="请输入用户名"/>
            </a-form-item>
          </a-col>
<!--          <a-col :span="12" v-show="record.id ? false : true">
            <a-form-item label="密码">
              <a-input type="password" v-decorator="['password', {rules: [{required: true, message: '请输入密码'}]}]" placeholder="请输入密码"/>
            </a-form-item>
          </a-col>-->
          <a-col :span="12">
            <a-form-item label="姓名">
              <a-input v-decorator="['name', {rules: [{required: false, message: '请输入姓名'}]}]" placeholder="请输入姓名"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="联系方式">
              <a-input v-decorator="['callNo', {rules: [{required: false, message: '请输入联系方式'}]}]" placeholder="请输入联系方式"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item
              label="备注"
              :label-col="{ span: 3 }"
              :wrapper-col="{ span: 20 }"
            >
              <a-textarea :rows="3" v-decorator="['remark', {rules: [{required: false, message: '请输入备注'}]}]" placeholder="请输入备注"/>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
// import pick from 'lodash.pick'

// 表单字段

import pick from 'lodash.pick'

const fields = ['user', 'callNo', 'name', 'remark']
export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    loading: {
      type: Boolean,
      default: () => false
    },
    model: {
      type: Object,
      default: () => null
    }
  },
  data () {
    return {
      record: {},
      labelCol: { span: 8 },
      wrapperCol: { span: 16 },
      form: this.$form.createForm(this),
      laiOption: [
        { value: 0, label: '电话' },
        { value: 1, label: '微信' },
        { value: 2, label: '12345' },
        { value: 3, label: '其他' }
      ],
      statusRadio: [
        { value: 0, label: '在职' },
        { value: 1, label: '离职' }
      ]
    }
  },
  created () {},
  methods: {
    addvisible () {
      this.record = {}
    },
    editRecord (record) {
      this.record = record
      console.log(33, this.record)
      this.$nextTick(() => {
        record && this.form.setFieldsValue(pick(record, fields))
      })
    }
  }
}
</script>
<style>
.ant-form-inline .ant-form-item > .ant-form-item-control-wrapper, .ant-form-inline .ant-form-item > .ant-form-item-label {
  width: 100%;
}
</style>
