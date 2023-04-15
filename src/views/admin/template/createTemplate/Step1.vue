<template>
  <div>
    <a-form :form="form" style="max-width: 500px; margin: 40px auto 0;">
      <a-form-item
        label="模板名称"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
        <a-input v-decorator="['templateName', { rules: [{required: true, message: '模板名称必须填写'}] }]"/>
      </a-form-item>
      <a-form-item
        label="分类"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
        <a-cascader
          :v-decorator="['channelId']"
          :options="options"
          :props="{emitPath: false}"
          :display-render="displayRender"
          expand-trigger="hover"
          placeholder="请选择"
          @change="onChange"
          v-model="channel"
        />
      </a-form-item>
      <a-form-item :wrapperCol="{span: 19, offset: 10}">
        <a-button type="primary" @click="nextStep">下一步</a-button>
      </a-form-item>
    </a-form>
    <a-divider />
    <!-- <div class="step-form-style-desc">
      <h3>说明</h3>
      <h4>转账到支付宝账户</h4>
      <p>如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。</p>
      <h4>转账到银行卡</h4>
      <p>如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。</p>
    </div> -->
  </div>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['templateName', 'channelId']
export default {
  name: 'Step1',
  data () {
    return {
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this),
      mdl: [],
      channel: [],
      options: [
        {
          value: '0',
          label: '科技',
          children: [
            {
              value: '1',
              label: '电子产品'
            },
            {
              value: '2',
              label: '网站'
            }
          ]
        },
        {
          value: '3',
          label: '社会',
          children: [
            {
              value: '4',
              label: '政府机构'
            },
            {
              value: '5',
              label: '学校'
            },
            {
              value: '6',
              label: '医院'
            }
          ]
        }
      ]
    }
  },
  mounted: function () {
    console.log(this.$route.params)
  },
  created () {
    console.log('custom modal created')

    // 防止表单未注册
    fields.forEach(v => this.form.getFieldDecorator(v))
    // console.log(this.$route.params)

    // 当 model 发生改变时，为表单设置值
    // this.$watch('model', () => {
    this.form.setFieldsValue(pick(this.$route.params, fields))
    this.channel = ['0', '1']
    // })
  },
  methods: {
    displayRender ({ labels }) {
      return labels[labels.length - 1]
    },
    onChange (val) {
      const { form: { validateFields } } = this
      // 先校验，通过表单校验后，才进入下一步
      validateFields((values) => {
        values.channelId = val[val.length - 1]
        console.log(values)
      })
    },
    nextStep () {
      const { form: { validateFields } } = this
      // 先校验，通过表单校验后，才进入下一步
      validateFields((err, values) => {
        if (!err) {
          this.$emit('nextStep')
        }
      })
    }
  }
}
</script>

<style lang="less" scoped>
.step-form-style-desc {
  padding: 0 56px;
  color: rgba(0,0,0,.45);

  h3 {
    margin: 0 0 12px;
    color: rgba(0,0,0,.45);
    font-size: 16px;
    line-height: 32px;
  }

  h4 {
    margin: 0 0 4px;
    color: rgba(0,0,0,.45);
    font-size: 14px;
    line-height: 22px;
  }

  p {
    margin-top: 0;
    margin-bottom: 12px;
    line-height: 22px;
  }
}
</style>
