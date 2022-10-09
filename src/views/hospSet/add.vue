<template>

  <div style="margin: 20px">
    <el-form  label-position="right" label-width="120px">
      <el-form-item  label="医院名称">
        <el-input style="width: 300px" v-model="hospitalSet.hosname" ></el-input>
      </el-form-item>


      <el-form-item label="医院编号">
        <el-input style="width: 300px" v-model="hospitalSet.hoscode"></el-input>
      </el-form-item>
      <el-form-item label="api基础路径">
        <el-input style="width: 300px" v-model="hospitalSet.apiUrl"></el-input>
      </el-form-item>
      <el-form-item label="联系人姓名">
        <el-input style="width: 300px" v-model="hospitalSet.contactsName" ></el-input>
      </el-form-item>
      <el-form-item label="联系人手机">
        <el-input style="width: 300px" v-model="hospitalSet.contactsPhone" ></el-input>
      </el-form-item>

      <el-button type="primary" icon="el-icon-circle-plus" @click="saveHospitalSet">保存</el-button>
    </el-form>

  </div>

</template>
<script>

  import hospSet from "../../api/hospSet";
export default {
  name: 'Add',

  data(){
    return{

      hospitalSet:{},    //每页数据集合

    }
  },
  created() {
    //页面渲染前，获取路由传递过来的id值
    if (this.$route.params && this.$route.params.id) {
      const id=this.$route.params.id
      this.getHospitalSet(id)
    }
  },

  methods:{
    saveHospitalSet(){

      this.$confirm('是否确认保存?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => { //确定执行then方法
        //调用接口
        hospSet.saveHospitalSet(this.hospitalSet)
          .then(res=>{

            this.$message({
              type:'success',
              message:'保存成功'
            })

            this.$router.push({path:'/hospSet/list'})
          })
      })


    },

    //根据id查询
    getHospitalSet(id){


      hospSet.getHospitalSet(id)
      .then(res=>{

          this.hospitalSet=res.data
      })
    }



  }
}
</script>

<style scoped>

</style>
