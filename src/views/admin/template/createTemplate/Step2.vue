<template>
  <div>
    <a-form :form="form" style="max-width: 700px; margin: 40px auto 0;">
      <a-row :gutter="48">
        <a-col :md="12" :sm="10">
          <a-form-item
            label="模板名称"
            :labelCol="labelCol"
            :wrapperCol="wrapperCol"
            class="stepFormText"
          >
            模板1
          </a-form-item>
        </a-col>
        <a-col :md="12" :sm="10">
          <a-form-item
            label="分类"
            :labelCol="labelCol"
            :wrapperCol="wrapperCol"
            class="stepFormText"
          >
            暂无
          </a-form-item>
        </a-col>
      </a-row>
      <a-row>
        <a-col :md="12" :sm="10">
          <a-form-item
            label="目录设置"
            :labelCol="labelCol"
            :wrapperCol="wrapperCol"
            class="stepFormText"
          >
            <div
              @mousedown="e => {
                e.preventDefault()
                this.selectOpen = true
              }">
              <a-select style="width: 300px" :open="selectOpen" @select="handleSelect" mode="multiple">
                <div slot="dropdownRender" slot-scope="menu">
                  <div style="padding: 4px 8px; cursor: pointer;display: flex">
                    <a-input @click="e=>e.target.focus()" v-model="value" />
                    <a-button icon="plus" type="link" @click="addItem">Add</a-button>
                  </div>
                  <a-divider style="margin: 4px 0;" />
                  <v-nodes :vnodes="menu" />
                </div>
                <a-select-option v-for="item in items" :key="item" :value="item">
                  {{ item }}
                </a-select-option>
              </a-select>
            </div>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row>
        <a-col :md="12" :sm="10">
          <a-form-item
            label="信息栏设置"
            :labelCol="labelCol"
            :wrapperCol="wrapperCol"
            class="stepFormText"
          >
            <div
              @mousedown="e => {
                e.preventDefault()
                this.infoOpen = true
              }">
              <a-select style="width: 300px" :open="infoOpen" @select="handleInfoSelect" mode="multiple">
                <div slot="dropdownRender" slot-scope="menu">
                  <div style="padding: 4px 8px; cursor: pointer;display: flex">
                    <a-input @click="e=>e.target.focus()" v-model="infoItem" />
                    <a-button icon="plus" type="link" @click="addInfoItem">Add</a-button>
                  </div>
                  <a-divider style="margin: 4px 0;" />
                  <v-nodes :vnodes="menu" />
                </div>
                <a-select-option v-for="item in infoitems" :key="item" :value="item">
                  {{ item }}
                </a-select-option>
              </a-select>
            </div>
          </a-form-item>
          <a-form-item :wrapperCol="{span: 19, offset: 6}">
            <a-button :loading="loading" type="primary" @click="nextStep">提交</a-button>
            <a-button style="margin-left: 8px" @click="prevStep">上一步</a-button>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>
  </div>
</template>

<script>
export default {
  components: {
        VNodes: {
            functional: true,
            render: (h, ctx) => ctx.props.vnodes
        }
    },
  name: 'Step2',
  data () {
    return {
      items: [],
      infoitems: [],
      selectOpen: false,
      clickHandle: null,
      infoItem: '',
      infoOpen: false,
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this),
      loading: false,
      timer: 0,
      value: ''
    }
  },
  methods: {
    addItem () {
      console.log(this.value)
      this.items.push(this.value)
    },
    addInfoItem () {
      console.log(this.infoItem)
      this.infoitems.push(this.infoItem)
    },
    handleSelect (value) {
      if (value) {
        this.selectOpen = false
      }
    },
    handleInfoSelect (infoItem) {
      if (infoItem) {
        this.infoOpen = false
      }
    },
    nextStep () {
      const that = this
      const { form: { validateFields } } = this
      that.loading = true
      validateFields((err, values) => {
        if (!err) {
          console.log('表单 values', values)
          that.timer = setTimeout(function () {
            that.loading = false
            that.$emit('nextStep')
          }, 1500)
        } else {
          that.loading = false
        }
      })
    },
    prevStep () {
      this.$emit('prevStep')
    }
  },
  beforeDestroy () {
    clearTimeout(this.timer)
  }
}
</script>

<style lang="less" scoped>
  .stepFormText {
    margin-bottom: 24px;

    .ant-form-item-label,
    .ant-form-item-control {
      line-height: 22px;
    }
  }

</style>
