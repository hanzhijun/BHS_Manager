<template>
  <div class="goodsList">

    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        label="列表">
        <el-table-column
          prop="create_time"
          label="参与时间"
          min-width="120" align='center'>
          <!--<template slot-scope='scope'>-->
            <!--{{$formatTime(scope.row.create_time)}}-->
          <!--</template>-->
        </el-table-column>
        <el-table-column
          prop="goods_name"
          label="商品名称"
          min-width="140" align='center'>
        </el-table-column>
        <el-table-column
          prop="status"
          label="是否助力成功"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <span v-if="scope.row.status == 1 ||scope.row.status == 2 || scope.row.status == 4">是</span>
            <span v-else>否</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="status"
          label="是否领取"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <span v-if="scope.row.status == 2">是</span>
            <span v-else>否</span>
          </template>
        </el-table-column>
      </el-table-column>
    </el-table>

    <!--<el-pagination-->
      <!--layout="prev, pager, next"-->
      <!--:total="total" :page-size="20" @current-change="handleCurrentChange"-->
      <!--:current-page.sync="start">-->
    <!--</el-pagination>-->
  </div>
</template>

<script>
  export default {
    name: 'goodsList',
    data () {
      return {
        tableData: [],
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
            url: '/onebuy/onpurseDetail',
            data: {page: this.start, limit: 20, user_id: this.$route.query.user_id},
          fuc: (res) => {
          if (res.code == 200) {
            this.tableData = res.data
//            this.total = res.data.total
          }
        }
      })
      },
      handleCurrentChange (val) {
        this.start = val
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
