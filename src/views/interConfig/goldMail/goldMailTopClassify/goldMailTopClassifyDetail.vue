<template>
  <div class="indexIconClassifyDetail">
    <el-form ref="form" :model="form" label-width="120px" :rules='rules'>
      <el-form-item label='图片' required>
        <el-upload
          class="avatar-uploader"
          action="https://jsonplaceholder.typicode.com/posts/"
          :show-file-list="false"
          :before-upload="beforeAvatarUploadLogo">
          <img v-if="form.img" :src="form.img" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label='选择分类' prop='goods_category_id'>
        <el-select v-model='form.goods_category_id' placeholder="请选择" @change='selectChange'>
          <el-option v-for='item in businesscategories'
            :label="item.c_name"
            :value="item.cid" :key="item.cid">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label='名称' prop='name'>
        <el-input v-model="form.name" placeholder='请输入url' :maxlength='40'></el-input>
      </el-form-item>
      <el-form-item label='备注'>
        <el-input type='textarea' v-model="form.remark" placeholder='请输入备注' :maxlength='40'></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click='submit'>保存</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        form: {
          img: '',
          goods_category_id: '',
          name: '',
          remark: '',
          goods_category_name: ''
        },
        businesscategories: [],
        rules: {
          goods_category_id: [{ required: true, message: '请选择', trigger: 'blur' }],
          name: [{ required: true, message: '请输入', trigger: 'blur' }],
          goods_id: [{ required: true, message: '请填写', trigger: 'blur' }]
        }
      }
    },
    created () {
      this.$axios({
        type: 'post',
        url: '/goods/category/getinfo',
        data: {parent_id: 0},
        fuc: (res) => {
          if (res.code == 200) {
            this.businesscategories = res.data
          }
        }
      })
//      this.$qiyang()
      if (this.$route.query.goods_activity_category_id) {
        this.$axios({
          type: 'post',
          url: '/goods/activitycategorygetdetailed',
          data: {goods_activity_category_id: this.$route.query.goods_activity_category_id},
          fuc: (res) => {
            if (res.code == 200) {
              for (let val in this.form) {
                if (val == 'img') {
                  this.form.img = res.data.img_path + res.data.img
                } else if (res.data[val]) {
                  this.form[val] = res.data[val]
                }
              }
              this.form.goods_activity_category_id = res.data.goods_activity_category_id
              this.form.img_path = res.data.img_path
            }
          }
        })
      }
    },
    methods: {
      selectChange (data) {
        for (let val of this.businesscategories) {
          if (val.cid == data) {
            this.form.goods_category_name = val.c_name
          }
        }
      },
      submit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            if (this.form.image == '') {
              this.$message.warning('请上传图片')
              return
            }
            let _form = JSON.parse(JSON.stringify(this.form))
            if (_form.img.indexOf(_form.img_path) > -1) {
              _form.img = _form.img.split(_form.img_path)[1]
            }
            this.$axios({
              type: 'post',
              url: this.$route.query.goods_activity_category_id ? '/goods/activitycategoryedit' : '/goods/activitycategoryadd',
              data: {parent_id: 3, type: 2, goods_category_type: 1, ..._form},
              fuc: (res) => {
                console.log('res', res)
                if (res.code == 200) {
                  this.$message.success('操作成功')
                  this.$deleteOneTag('/interConfig/goldMail/goldMailTopClassifyList')
                }
              }
            })
          }
        })
      },
      beforeAvatarUploadLogo (file) {
        let _this = this
        var reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = function(e){ 
          _this.form.img = this.result // 这个就是base64编码了
        }
        return false
      }
    }
  }
</script>

<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
<style scoped="true">
  .indexIconClassifyDetail{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .indexIconClassifyDetail>.el-form .el-input{
    max-width: 400px;
  }
  .indexIconClassifyDetail>.el-form .el-textarea{
    max-width: 400px;
  }
</style>