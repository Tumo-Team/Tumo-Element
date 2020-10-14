<template>
  <div class="app-container">
    <el-card>
      <el-table
        ref="table"
        v-loading="loading"
        :data="list"
        element-loading-text="拼命加载中"
        element-loading-spinner="el-icon-loading"
      >
        <el-table-column prop="username" label="用户名" min-width="110" />
        <el-table-column prop="roles" label="角色" min-width="100">
          <template slot-scope="scope">
            <el-tag v-for="role in scope.row.roles" :key="role" type="success">{{ role }}</el-tag>
          </template>
        </el-table-column>
      </el-table>
      <pagination
        v-show="pageConf.total>0"
        :total="pageConf.total"
        :page.sync="pageConf.page"
        :limit.sync="pageConf.limit"
        @pagination="fetchData"
      />
    </el-card>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { getUserList } from '@/api/user'

export default {
  name: 'Index',
  components: { Pagination },
  data() {
    return {
      list: [],
      pageConf: {
        page: 1,
        limit: 10,
        total: 0
      },
      loading: true
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.loading = true
      getUserList(this.pageConf, this.query).then(res => {
        this.list = res.data.rows
        this.pageConf.total = res.data.total
        this.loading = false
      })
    }
  }
}
</script>
