tenants<template>
  <a-card>
    <a-row :gutter="24">
      <a-col :span="7">
        <span style="margin-right:5%">关键字:</span>
        <a-input style="width:50%" v-model="query.key" placeholder="请输入搜索关键字" />
      </a-col>
      <a-col :span="12"></a-col>
      <a-col :span="5" :style="{ textAlign: 'right' }">
        <a-button type="primary" html-type="submit" @click="()=>this.$refs.table.refresh()">查询</a-button>
        <a-divider type="vertical" />
        <a-button type="primary" html-type="submit" @click="showEdit()">新增</a-button>
      </a-col>
    </a-row>

    <s-table
      ref="table"
      :rowSelection="rowSelection"
      row-key="id"
      :columns="columns"
      :data="loadData"
      style="margin-top:12px"
    >
      <template slot="action" slot-scope="text,record">
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item key="1" @click="showEdit(record.id)">
              <a-icon type="edit" />编辑
            </a-menu-item>
            <a-menu-item key="2">
              <a-icon type="hdd" />功能
            </a-menu-item>
            <a-menu-item key="3" @click="delRecord(record.id)">
              <a-icon type="delete" />删除
            </a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            <a-icon type="setting" />操作
            <a-icon type="down" />
          </a-button>
        </a-dropdown>
      </template>
      <template slot="footer" slot-scope="currentPageData">
        <a-button type="default">批量删除</a-button>
      </template>
    </s-table>
    <edit ref="edit" @close="refresh" />
  </a-card>
</template>
<style lang="less" scoped>
</style>
<script>
/* eslint-disable */
const columns = [
  {
    title: '版本名称',
    dataIndex: 'name'
  },

  { title: '操作', dataIndex: '', key: 'x', scopedSlots: { customRender: 'action' }, align: 'right' }
]

import edit from './edition-edit'
import { STable } from '@/components'
import moment from 'moment'
import { all, del } from '@/api/administration/editions'

export default {
  data() {
    return {
      columns,
      query: {
        key: '',
        State: '',
        maxResultCount: 10,
        skipCount: 0
      },
      loadData: parameter => {
        return all(Object.assign(this.query, parameter)).then(res => {
          return res
        })
      },
      model: {},
      selectedRowKeys: [] // Check here to configure the default column,
    }
  },
  computed: {
    rowSelection() {
      const { selectedRowKeys } = this
      return {
        selectedRowKeys,
        onChange: this.onSelectChange,
        hideDefaultSelections: true,
        onSelection: this.onSelection
      }
    }
  },
  components: {
    STable,
    edit
  },
  methods: {
    showEdit(id) {
      this.$refs.edit.show(id)
    },
    delRecord(id) {
      var _this = this
      _this.$confirm({
        title: '敏感操作',
        content: '确认删除该版本记录吗？该操作不可恢复',
        onOk() {
          del(id).then(res => {
            _this.$message.success("删除成功");
            _this.refresh();
          })
        }
      })
    },
    refresh() {
      this.$refs.table.refresh()
    },
    onSelectChange(selectedRowKeys) {
      console.log('selectedRowKeys changed: ', selectedRowKeys)
      this.selectedRowKeys = selectedRowKeys
    },
    stateChange(record) {
      console.log(record)
    }
  }
}
</script>



