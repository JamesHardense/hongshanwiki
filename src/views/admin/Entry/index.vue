<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <a-descriptions title="词条信息">
        <a-descriptions-item label="词条名称">计算机</a-descriptions-item>
        <a-descriptions-item label="分类">科技/电子产品</a-descriptions-item>
        <a-descriptions-item label="创建时间">{{ entry.createDate }}</a-descriptions-item>
        <a-descriptions-item label="创建作者">作者1</a-descriptions-item>
        <a-descriptions-item label="修改者">修改者1、修改者2</a-descriptions-item>
        <a-descriptions-item label="词条状态">展示中</a-descriptions-item>
        <a-descriptions-item label="审核人">审核人1</a-descriptions-item>
      </a-descriptions>
      <a-divider style="margin-bottom: 32px"/>
      <a-row>
        <a-col :span="5">
          <h2 style="font-weight: bold;" >标题</h2>
        </a-col>
        <a-button type="primary" @click="handleLock" icon="lock" v-if="!visible"> 锁定</a-button>
        <a-button type="primary" @click="handleUnlock" icon="unlock" v-if="visible"> 解锁</a-button>
      </a-row>
      <h3>计算机</h3>
      <a-divider style="margin-bottom: 32px"/>
      <a-row>
        <a-col :span="5">
          <h3 style="font-weight: bold;" >摘要</h3>
        </a-col>
        <a-button type="primary" @click="handleLock" icon="lock" v-if="!visible"> 锁定</a-button>
        <a-button type="primary" @click="handleUnlock" icon="unlock" v-if="visible"> 解锁</a-button>
      </a-row>
      <h4>计算机（computer）俗称电脑，是现代一种用于高速计算的电子计算机器，可以进行数值计算，又可以进行逻辑计算，还具有存储记忆功能。是能够按照程序运行，自动、高速处理海量数据的现代化智能电子设备。</h4>
      <a-divider style="margin-bottom: 32px"/>
      <a-row>
        <a-col :span="5">
          <h3 style="font-weight: bold;">信息栏</h3>
        </a-col>
        <a-button type="primary" @click="handleLock" icon="lock" v-if="!visible"> 锁定</a-button>
        <a-button type="primary" @click="handleUnlock" icon="unlock" v-if="visible"> 解锁</a-button>
      </a-row>
      <a-descriptions title="">
        <a-descriptions-item label="中文名">计算机</a-descriptions-item>
        <a-descriptions-item label="外文名">computer</a-descriptions-item>
        <a-descriptions-item label="别名">电脑</a-descriptions-item>
        <a-descriptions-item label="第一台计算机">ENIAC（阿塔纳索夫-贝瑞计算机）</a-descriptions-item>
        <a-descriptions-item label="发明者">约翰·冯·诺依曼</a-descriptions-item>
        <a-descriptions-item label="研究时间">1889年</a-descriptions-item>
      </a-descriptions>
      <a-divider style="margin-bottom: 32px"/>
      <a-row>
        <a-col :span="5">
          <h3 style="font-weight: bold;"> 发展历史</h3>
        </a-col>
        <a-button type="primary" @click="handleLock" icon="lock" v-if="!visible"> 锁定</a-button>
        <a-button type="primary" @click="handleUnlock" icon="unlock" v-if="visible"> 解锁</a-button>
      </a-row>
      <h4>计算工具的演化经历了由简单到复杂、从低级到高级的不同阶段，例如从“结绳记事”中的绳结到算筹、算盘计算尺、机械计算机等。它们在不同的历史时期发挥了各自的历史作用，同时也启发了现代电子计算机的研制思想。
        1889年，美国科学家赫尔曼·何乐礼研制出以电力为基础的电动制表机，用以储存计算资料。
        1930年，美国科学家范内瓦·布什造出世界上首台模拟电子计算机。</h4>
      <a-divider style="margin-bottom: 32px"/>
      <div style="margin-left: 40%">
        <a-button type="primary" @click="handleOk" v-if="!visible"> 确定</a-button>
        <a-button @click="handleCancel" v-if="!visible" style="margin-left: 40px;">取消</a-button>
      </div>
    </a-card>
  </page-header-wrapper>
</template>

<script>
export default {
  data () {
    return {
      entry: {},
      visible: false
    }
  },
  computed: {
    title () {
      return this.$route.meta.title
    }
  },
  created () {
    this.entry = this.$route.params
  },
  methods: {
    handleLock () {
      this.visible = true
    },
    handleUnlock () {
      this.visible = false
    },
    handleOk () {
      this.$router.push({
      name: 'PostManagement'
    })
    }
  }

}
</script>

<style lang="less" scoped>
  .title {
    color: rgba(0,0,0,.85);
    font-size: 16px;
    font-weight: 500;
    margin-bottom: 16px;
  }
</style>
