<template>
  <div class="app-container">

<!--模糊查询-->
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item>
        <el-input  v-model="searchObj.hosname" placeholder="医院名称"/>
      </el-form-item>
      <el-form-item>
        <el-input v-model="searchObj.hoscode" placeholder="医院编号"/>
      </el-form-item>
      <el-button type="primary" icon="el-icon-search" @click="getList()">查询</el-button>
    </el-form>

    <!-- 工具条 -->
    <div>
      <el-button type="danger" size="mini" @click="removeRows()">批量删除</el-button>
    </div>


    <!-- banner列表 -->
    <el-table
      :data="list" stripe style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"/>


      <el-table-column type="index" width="50"/>
      <el-table-column prop="hosname" label="医院名称"/>
      <el-table-column prop="hoscode" label="医院编号"/>
      <el-table-column prop="apiUrl" label="api基础路径"width="200"/>
      <el-table-column prop="contactsName" label="联系人姓名"/>
      <el-table-column prop="contactsPhone" label="联系人手机"/>
      <el-table-column label="状态" width="80">
        <template slot-scope="scope">
          {{ scope.row.status === 1 ? '可用' : '不可用' }}
        </template>
      </el-table-column>


      <el-table-column label="操作" width="280" align="center">
        <template slot-scope="scope">
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.id)"> </el-button>
          <el-button v-if="scope.row.status==1" type="primary" size="mini" icon="el-icon-setting" @click="lockHospitalSet(scope.row.id,0)">锁定</el-button>
          <el-button v-if="scope.row.status==0" type="danger" size="mini" icon="el-icon-setting" @click="lockHospitalSet(scope.row.id,1)">取消锁定</el-button>

          <router-link :to="'/hospSet/edit/'+scope.row.id">
            <el-button  type="primary" size="mini" icon="el-icon-setting">编辑</el-button>
          </router-link>


        </template>
      </el-table-column>

    </el-table>



    <!-- 分页 -->
    <el-pagination
      :current-page.sync="current"
      :page-size="limit"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="getList"
    />


  </div>

</template>
<script>

    import hospSet from "../../api/hospSet";

    export default {
        name: "List",

      data(){
        return{
          current:1, //当前页
          limit:3,   //每页个数
          searchObj:{},  //条件封装对象
          list:[],    //每页数据集合
          total:0, //总记录数
          multipleSelection:[],
        }
      },
      created() {
        this.getList()
      },
      methods:{

        //医院设置列表
        getList(page=1){
          this.current=page
          hospSet.getHospSetList(this.current,this.limit,this.searchObj)
          .then(res=>{
            this.list=res.data.records
            //总记录数
            this.total = res.data.total

          })
          .catch(error=>{
            console.log(error)
          })
        },

        //删除
        removeDataById(id){
          this.$confirm('此操作将永久删除医院是设置信息, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => { //确定执行then方法
            //调用接口
            hospSet.deleteHospSet(id)
              .then(response => {
                //提示
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                })
                //刷新页面
                this.getList(1)
              })
          })

        },

        //批量删除
        removeRows(selection){
          this.$confirm('此操作将永久删除医院是设置信息, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => { //确定执行then方法
            var idList = []
            //遍历数组得到每个id值，设置到idList里面
            for(var i=0;i<this.multipleSelection.length;i++) {
              var obj = this.multipleSelection[i]
              var id = obj.id
              idList.push(id)
            }
            //调用接口
            hospSet.batchRemoveHospitalSet(idList)
              .then(response => {
                //提示
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                })
                //刷新页面
                this.getList(1)
              })
          })


        },

        //获取列数
        handleSelectionChange(selection){
          this.multipleSelection=selection
        },


        //锁定和取消锁定
        lockHospitalSet(id,status){
          hospSet.lockHospSet(id,status).then(res=>{
            this.getList(1)
          })
        },

      }


    }
</script>

<style scoped>

</style>
