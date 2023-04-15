<template>
  <a-modal
    title="新建/编辑分类"
    :width="640"
    :visible="visible"
    :confirmLoading="loading"
    @ok="() => { $emit('ok') }"
    @cancel="() => { $emit('cancel') }"
  >
    <a-spin :spinning="loading">
      <a-form :form="form" v-bind="formLayout">
        <a-form-item v-show="model && model.id > 0" label="分类ID">
          <a-input v-decorator="['id', { initialValue: 0 }]" disabled />
        </a-form-item>
        <a-form-item label="名称">
          <a-input v-decorator="['name', {rules: [{required: true, min: 5, message: '请输入至少五个字符！'}]}]" />
        </a-form-item>
        <a-form-item label="唯一标识">
          <a-input v-decorator="['key', {rules: [{required: true, min: 5, message: '请输入至少五个字符！'}]}]" />
        </a-form-item>
        <a-form-item label="一级分类">
          <a-select placeholder="请选择" @change="handleChange" v-decorator="['parentId', {rules: [{required: true, message: '请输入一级分类'}]}]">
            <a-select-option v-for="channel in channels" :key="channel.parentId" :value="channel.parentId + ''" :label="channel.parentName">
              {{ channel.parentName }}
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="审核人">
          <a-select placeholder="请选择" @change="handleChange" v-decorator="['auditorId', {rules: [{required: true, message: '请输入审核人'}]}]">
            <a-select-option v-for="auditor in auditors" :key="auditor.id" :value="auditor.id + ''" :label="auditor.userName">
              {{ auditor.userName }}
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item v-show="model && model.id > 0" label="状态">
          <a-select placeholder="请选择" @change="handleChange" v-decorator="['status', {rules: [{required: true, message: '请输入审核人'}]}]">
            <a-select-option value="0">已停用</a-select-option>
            <a-select-option value="1">使用中</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item v-show="model && model.id > 0" label="创建时间">
          <a-date-picker
            v-decorator="['createDate', config]"
            show-time
            format="YYYY-MM-DD HH:mm:ss"
            disabled
          />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['key', 'id', 'parentId', 'name', 'auditorId', 'status', 'createDate']
const channels = [
            {
              parentId: 0,
              parentName: '/'
            },
            {
              parentId: 1,
              parentName: '科技'
            },
            {
              parentId: 2,
              parentName: '社会'
            }
  ]
const auditors = [
  {
    id: 0,
    userName: '审核人1'
  },
  {
    id: 1,
    userName: '审核人2'
  },
  {
    id: 2,
    userName: '审核人3'
  },
  {
    id: 3,
    userName: '审核人4'
  },
  {
    id: 4,
    userName: '审核人5'
  }
]
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
    this.formLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      }
    }
    return {
      form: this.$form.createForm(this),
      channels,
      auditors
    }
  },
  created () {
    console.log('custom modal created')

    // 防止表单未注册
    fields.forEach(v => this.form.getFieldDecorator(v))

    // 当 model 发生改变时，为表单设置值
    this.$watch('model', () => {
      this.model && this.form.setFieldsValue(pick(this.model, fields))
    })
  },
  methods: {
    handleChange (value) {
      this.$message.info(value)
    }
  }
}
</script>
