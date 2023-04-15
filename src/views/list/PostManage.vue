<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="8" :sm="24">
              <a-form-item label="词条标题">
                <a-input v-model="queryParam.id" placeholder=""/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="词条状态">
                <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                  <a-select-option value="0">待审核</a-select-option>
                  <a-select-option value="1">展示中</a-select-option>
                  <a-select-option value="2">已废用</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="词条分类">
                <a-cascader
                  v-model="queryParam.channelId"
                  :options="options"
                  :display-render="displayRender"
                  expand-trigger="hover"
                  placeholder="请选择"
                  @change="onChange"
                />
              </a-form-item>
            </a-col>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="创建作者">
                  <a-input v-model="queryParam.userName" placeholder=""/>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="发表日期">
                  <a-date-picker v-model="queryParam.date" style="width: 100%" placeholder="请输入更新日期"/>
                </a-form-item>
              </a-col>
            </template>
            <a-col :md="!advanced && 8 || 24" :sm="24">
              <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
                <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
                <a-button style="margin-left: 8px" @click="() => this.queryParam = {}">重置</a-button>
                <a @click="toggleAdvanced" style="margin-left: 8px">
                  {{ advanced ? '收起' : '展开' }}
                  <a-icon :type="advanced ? 'up' : 'down'"/>
                </a>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>

      <!-- <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
        <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0">
          <a-menu slot="overlay">
            <a-menu-item key="1"><a-icon type="delete" />删除</a-menu-item> -->
      <!-- lock | unlock -->
      <!-- <a-menu-item key="2"><a-icon type="lock" />锁定</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作 <a-icon type="down" />
          </a-button>
        </a-dropdown>
      </div> -->

      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data="loadData"
        showPagination="auto"
      >
        <!-- <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }} -->
        <!-- </span> -->
        <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
        </span>
        <span slot="channelId" slot-scope="text">
          <!-- <ellipsis :length="4" tooltip>{{ text }}</ellipsis> -->
          <a-badge :status="text | statusTypeFilter" :text="text | channelFilter" />
        </span>

        <span slot="action" slot-scope="text, record">
          <template>
            <a-row>
              <div v-if="roleId > 1" >
                <a style="color: #73d13d;" @click="handlelock(record)">锁词条</a>
                <a-divider type="vertical" />
                <a style="color: #faad14;" @click="handleSub(record)">日志</a>
                <a-divider type="vertical" />
                <a style="color: #ff4d4f;" @click="handleSub(record)">删除</a>
              </div>
              <div v-if="roleId == 1" >
                <a style="color: #73d13d;" @click="handleEdit(record)">审核</a>
                <a-divider type="vertical" />
                <a style="color: #4096ff;" @click="handleSub(record)">查重</a>
              </div>
            </a-row>
          </template>
        </span>
      </s-table>

      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
      />
      <step-by-step-modal ref="modal" @ok="handleOk"/>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'

const columns = [
  {
    title: '词条标题',
    dataIndex: 'postTitle',
    scopedSlots: { customRender: 'postTitle' }
  },
  {
    title: '词条分类',
    dataIndex: 'channelId',
    scopedSlots: { customRender: 'channelId' }
  },
  {
    title: '创建作者',
    dataIndex: 'author',
    scopedSlots: { customRender: 'author' }
  },
  // {
  //   title: '',
  //   dataIndex: 'callNo',
  //   sorter: true,
  //   needTotal: true,
  //   customRender: (text) => text + ' 次'
  // },
  {
    title: '词条状态',
    dataIndex: 'status',
    scopedSlots: { customRender: 'status' }
  },
  {
    title: '发表时间',
    dataIndex: 'createDate',
    sorter: true
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '220px',
    scopedSlots: { customRender: 'action' }
  }
]

const statusMap = {
  0: {
    status: 'default',
    text: '已弃用'
  },
  1: {
    status: 'success',
    text: '展示中'
  },
  2: {
    status: 'error',
    text: '待审核'
  }
}

const channelMap = {
  0: {
    text: '医院'
  },
  1: {
    text: '政府机构'
  },
  2: {
    text: '网站'
  },
  3: {
    text: '学校'
  }
}

export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    StepByStepModal
  },
  data () {
    this.columns = columns
    return {
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      roleId: '',
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters)
          .then(res => {
            this.roleId = 2
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: [],
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
  filters: {
    statusFilter (type) {
      return statusMap[type].text
    },
    statusTypeFilter (type) {
      return statusMap[type].status
    },
    channelFilter (type) {
      return channelMap[type].text
    }
  },

  // created () {
  //   // getRoleList({ t: new Date() })
  //   getRole
  // },
  computed: {
    rowSelection () {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange
      }
    }
  },
  methods: {
    handleAdd () {
      this.mdl = null
      this.visible = true
    },
    handleEdit (record) {
      this.visible = true
      this.mdl = { ...record }
    },
    handlelock (record) {
      this.$router.push({
      name: 'LockEntry',
      params: record
    })
    },
    handleOk () {
      const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
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
      if (record.status !== 0) {
        this.$message.info(`${record.no} 订阅成功`)
      } else {
        this.$message.error(`${record.no} 订阅失败，规则已关闭`)
      }
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
    displayRender ({ labels }) {
      return labels[labels.length - 1]
    }
  }
}
</script>
