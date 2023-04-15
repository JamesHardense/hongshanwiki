<template>
  <a-modal
    title="新建/编辑用户"
    :width="640"
    :visible="visible"
    :confirmLoading="loading"
    @ok="() => { $emit('ok') }"
    @cancel="() => { $emit('cancel') }"
  >
    <a-spin :spinning="loading">
      <a-form :form="form" v-bind="formLayout">
        <a-form-item v-show="model && model.id > 0" label="用户ID">
          <a-input v-decorator="['id', { initialValue: 0 }]" disabled />
        </a-form-item>
        <a-form-item label="用户名">
          <a-input v-decorator="['userName', {rules: [{required: true, min: 5, message: '请输入至少五个字符！'}]}]" />
        </a-form-item>
        <a-form-item label="角色">
          <a-select placeholder="请选择" @change="handleChange" v-decorator="['roleId', {rules: [{required: true, message: '请输入角色'}]}]">
            <a-select-option v-for="role in roles" :key="role.id" :value="role.id + ''" :label="role.name">
              {{ role.name }}
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item v-show="model && model.id > 0" label="状态">
          <a-select placeholder="请选择" @change="handleChange" v-decorator="['status', {rules: [{required: true, message: '请输入状态'}]}]">
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
const fields = ['id', 'userName', 'roleId', 'status', 'createDate']
const roles = [
  {
    id: '0',
    name: '普通用户'
  },
  {
    id: '1',
    name: '大类审核员'
  },
  {
    id: '2',
    name: '小类审核员'
  },
  {
    id: '3',
    name: '超级管理员'
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
      roles
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
