<template>
  <div class="goodsList">
    <div class="searchForm">
      <p @click='showFormBool = !showFormBool'>筛选查询<i v-if='showFormBool' class="el-icon-arrow-down"></i><i v-else class="el-icon-arrow-up"></i></p>
      <el-form :inline="true" :model="formInline" class="demo-form-inline" v-if='showFormBool'>
        <el-form-item label="输入搜索">
          <el-input v-model="formInline.search" placeholder="用户ID/用户名称"></el-input>
        </el-form-item>
        <el-form-item label="商品名称">
          <el-input v-model="formInline.produc_name" placeholder="商品名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
      </el-form>
    </div>

    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        label="列表">
        <el-table-column
          prop="create_time"
          label="参与时间"
          min-width="120" align='center'>
          <template slot-scope='scope'>
            {{scope.row.create_time}}
          </template>
        </el-table-column>
        <el-table-column
          prop="user_id"
          label="ID"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="nickname"
          label="用户名称"
          min-width="140" align='center'>
        </el-table-column>
        <el-table-column
          prop="nums"
          label="抢购数"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="success_nums"
          label="助力成功数"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="receive_num"
          label="领取数"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="goods_name"
          label="抢购商品"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          label="操作"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <el-button @click="checkUser(scope.row.user_id)">查看</el-button>
          </template>
        </el-table-column>
      </el-table-column>
    </el-table>

    <el-pagination
      layout="prev, pager, next"
      :total="total" :page-size="20" @current-change="handleCurrentChange"
      :current-page.sync="start">
    </el-pagination>
  </div>
</template>

<script>
  export default {
    name: 'goodsList',
    data () {
      return {
        tableData: [],
        formInline: {
          search: '',
          produc_name: ''
        },
        start: 1,
        total: 0,
        showFormBool: true, // 是否显示过滤框
      }
    },
    created () {
      this.getTableData()
    },
    mounted () {},
    methods: {
      getTableData () {
        this.$axios({
            type: 'post',
            url: '/onebuy/onpurseUserCount',
            data: {page: this.start, limit: 20, ...this.formInline},
          fuc: (res) => {
          if (res.code == 200) {
            this.tableData = res.data.data
            this.total = res.data.total
          }
        }
      })
      },
      handleCurrentChange (val) {
        this.start = val
        this.getTableData()
      },
      checkUser (id) {
        this.$router.push({path: '/oneBuy/oneBuyUserDetail', query: {user_id: id}})
      },
      onSubmit () {
        this.getTableData()
      }
    }
  }
</script>
<style scoped="true">
  .goodsList{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .searchForm{
    margin: 10px 0;
  }
  .searchForm>p{
    width: 100%;
    background-color: #f5f7fa;
    padding: 10px;
    color: #909399;
    font-size: 14px;
    font-weight: bold;
    cursor: pointer;
  }
  .searchForm>p>i{
    margin-left: 20px;
  }
</style>
