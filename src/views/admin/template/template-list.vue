<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input v-model="listQuery.name" placeholder="请输入模板名称" style="width: 300px;" class="filter-item" />
      <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleCreate">
        添加
      </el-button>
    </div>
    <SimpleTable :list-loading="listLoading" :data="list" :columns="columns">
      <el-table-column label="操作" align="center" width="160px" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button type="primary" size="mini" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button type="danger" size="mini" @click="handleDelete(row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </SimpleTable>
    <pagination v-show="total>0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getList" />
  </div>
</template>

<script>
import waves from '@/directive/waves'
import SimpleTable from '@/components/SimpleTable'
import Pagination from '@/components/Pagination'
import { getData, delData } from '@/api/template'
export default {
  name: 'TemplateList',
  components: { SimpleTable, Pagination },
  directives: { waves },
  data() {
    return {
      list: [],
      total: 0,
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 10,
        state: '',
        name: ''
      },
      couponStateOptions: [
        { key: '0', display_name: '全部' },
        { key: '1', display_name: '未开始' },
        { key: '2', display_name: '进行中' },
        { key: '3', display_name: '已结束' }
      ],
      columns: [
        { header: '序号', type: 'index', width: '100px', dataIndex: '' },
        { header: '模板图片', type: 'templateImage', width: '', dataIndex: '' },
        { header: '模板名称', type: 'data', width: '', dataIndex: 'name' },
        { header: '使用人数', type: 'data', width: '', dataIndex: 'useNum' },
        { header: '更新时间', type: 'datetime', width: '', dataIndex: 'updatetime' }
      ]
    }
  },
  created: function() {
    this.getList()
  },
  methods: {
    getList() {
      this.listLoading = true
      getData(this.listQuery).then(response => {
        this.list = response.data.records
        this.total = response.data.total
        this.listLoading = false
      })
    },
    handleFilter() {
      this.listQuery.page = 1
      this.getList()
    },
    handleCreate() {
      this.$router.push({
        name: 'AdminTemplateEdit',
        params: {
          opt: 'add'
        }
      })
    },
    handleUpdate(data) {
      this.$router.push({
        name: 'AdminTemplateEdit',
        params: {
          opt: 'edit',
          id: data._id
        }
      })
    },
    handleDelete(data) {
      this.$confirm('是否删除该模板？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const param = { 'id': data._id }
        delData(param).then((res) => {
          if (res.code === 200) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
            this.handleFilter()
          } else {
            this.$message({
              message: res.msg,
              type: 'warning'
            })
          }
        })
      })
    }
  }
}
</script>
