<template>
  <a-modal
    :title="record.id ? '编辑车辆处理信息' : '新建车辆处理信息'"
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
            <a-form-item label="车牌号">
              <a-input v-decorator="['no', {rules: [{required: true, message: '请输入车牌号'}]}]" placeholder="请输入车牌号"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="信息来源">
              <a-select v-decorator="['description', {rules: [{required: true, message: '请选择信息来源'}]}]" placeholder="请选择信息来源">
                <a-select-option v-for="(item, index) in laiOption" :key="index">{{ item.label }} </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="联系方式">
              <a-input onkeyup="value=value.replace(/[^\d]/g,'')" maxlength='11' v-decorator="['callNo', {rules: [{required: true, message: '请输入联系方式'},{ pattern:/^1[34578]\d{9}$/, message: '请输入正确的手机号'}]}]" placeholder="请输入联系方式"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="当前状态">
              <a-select v-decorator="['status', {rules: [{required: true, message: '请选择当前状态'}]}]" placeholder="请选择当前状态">
                <a-select-option v-for="(item, index) in statusMap" :key="index">{{ item.label }} </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item
              label="问题详情"
              :label-col="{ span: 3 }"
              :wrapper-col="{ span: 20 }">
              <a-textarea placeholder="请输入问题详情" :rows="3" v-decorator="['updatedAt', {rules: [{required: true, message: '请输入问题详情'}]}]" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item
              label="调查结果"
              :label-col="{ span: 3 }"
              :wrapper-col="{ span: 20 }"
            >
              <a-textarea :rows="3" v-decorator="['results', {rules: [{required: true, message: '请输入调查结果'}]}]" placeholder="请输入调查结果"/>
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

const fields = ['no', 'callNo', 'description', 'status', 'updatedAt', 'results']
export default {
  props: {
    statusMap: {
      type: Array,
      required: true
    },
    laiOption: {
      type: Array,
      required: true
    },
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
      form: this.$form.createForm(this)
    }
  },
  created () {},
  methods: {
    editRecord (record) {
      this.record = record
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
