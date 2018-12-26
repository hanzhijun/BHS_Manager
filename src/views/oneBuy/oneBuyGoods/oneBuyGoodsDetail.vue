<template>
  <div class="indexSlideshowDetail">
    <el-form ref="form" :model="form" label-width="140px" :rules='rules'>
      <el-form-item label='商品编码' prop='goods_no'>
        <el-input placeholder='请输入商品编码' v-model='form.goods_no' :disabled="editBool"></el-input>
        <p>{{name}}</p>
        <el-button @click='goods_noSure' :disabled='editBool'>确认</el-button>
      </el-form-item>
      <el-form-item label='排序' prop='sort' v-if='businessSureBool'>
        <el-input placeholder='请输入排序' v-model='form.sort'></el-input>
      </el-form-item>
      <el-form-item label='抢购价' prop='discount_price' v-if='businessSureBool'>
        <el-input placeholder='请输入抢购价' v-model='form.discount_price'></el-input>
      </el-form-item>
      <el-form-item label='助力人数' prop='num' v-if='businessSureBool'>
        <el-input placeholder='请输入助力人数' v-model='form.num'></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click='submit' v-if='businessSureBool'>保存</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        form: {
          sort: '',
          discount_price: '',
          num: '',
          goods_no: ''
        },
        name: '',
        businessSureBool: false,
        editBool: false,
        rules: {
          goods_no: [{ required: true, message: '请输入', trigger: 'blur' }],
          sort: [{ required: true, message: '请输入', trigger: 'blur' }],
          discount_price: [{ required: true, message: '请输入', trigger: 'blur' }],
          num: [{ required: true, message: '请输入', trigger: 'blur' }],
        }
      }
    },
    created () {
      if (this.$route.query.id) {
        this.editBool = true
        this.$axios({
            type: 'post',
            url: '/onebuy/detail',
            data: {id: this.$route.query.id},
            fuc: (res) => {
            for (let val in this.form) {
              if (val == 'discount_price') {
                this.form[val] = res.data[val] / 100
              } else {
                this.form[val] = res.data[val] + ''
              }
        }
        this.businessSureBool = true
        this.form.id = this.$route.query.id
        console.log(this.form)
      }
      })
      }
    },
    methods: {
      goods_noSure () {
        if (this.form.goods_no == '') {
          this.$message.warning('请先输入商品编码')
          return
        }
        this.$axios({
          type: 'post',
          url: '/game/goodsconfirm',
          data: {goods_no: this.form.goods_no},
          fuc: (res) => {
          this.businessSureBool = true
        this.name = res.data
      }
      })
      },
      submit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            let _form = JSON.parse(JSON.stringify(this.form))
            this.$axios({
                type: 'post',
                url: '/onebuy/goods',
                data: _form,
                fuc: (res) => {
                if (res.code == 200) {
              this.$message.success('操作成功')
              this.$deleteOneTag('/oneBuy/oneBuyGoodsList')
            }
          }
          })
          }
        })
      }
    }
  }
</script>

<style scoped="true">
  .indexSlideshowDetail{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .indexSlideshowDetail>.el-form .el-input{
    max-width: 400px;
  }
  .indexSlideshowDetail>.el-form .el-textarea{
    max-width: 400px;
  }
</style>
