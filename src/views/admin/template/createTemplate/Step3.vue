<template>
  <div>
    <a-form>
      <a-result title="创建模板成功" status="success" style="max-width: 560px; margin: 40px auto 0;">
        <div class="information">
          <a-row>
            <a-col :sm="8" :xs="24">模板名称：</a-col>
            <a-col :sm="16" :xs="24">模板1</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">分类：</a-col>
            <a-col :sm="16" :xs="24">科技/网站</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">审核人：</a-col>
            <a-col :sm="16" :xs="24">审核人1</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">目录：</a-col>
            <a-col :sm="16" :xs="24">目录1、目录2</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">信息栏：</a-col>
            <a-col :sm="16" :xs="24">中文名、外文名</a-col>
          </a-row>
        </div>
        <template #extra>
          <a-button type="primary" @click="finish">返回</a-button>
        </template>
      </a-result>
    </a-form>
  </div>
</template>

<script>
export default {
  name: 'Step3',
  data () {
    return {
      loading: false
    }
  },
  methods: {
    finish () {
      this.$router.push({ name: 'TemplateManagement' })
    }
  }
}
</script>
<style lang="less" scoped>
  .information {
    line-height: 22px;

    .ant-row:not(:last-child) {
      margin-bottom: 24px;
    }
  }
  .money {
    font-family: "Helvetica Neue",sans-serif;
    font-weight: 500;
    font-size: 20px;
    line-height: 14px;
  }
</style>
