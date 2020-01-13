<template>
  <el-table
    :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
    v-loading="loading"
    style="width: 100%">
    <el-table-column type="expand">
      <template slot-scope="props">
        <el-form label-position="left" inline class="demo-table-expand">
          <el-form-item label="id">
            <span>{{ props.row.id }}</span>
          </el-form-item>
          <el-form-item label="uuid">
            <span>{{ props.row.uuid }}</span>
          </el-form-item>
          <el-form-item label="模板名称">
            <span>{{ props.row.name }}</span>
          </el-form-item>
          <el-form-item label="系统">
            <span>{{ props.row.os }}</span>
          </el-form-item>
          <el-form-item label="类型">
            <span>{{ props.row.type }}</span>
          </el-form-item>
          <el-form-item label="login_user">
            <span>{{ props.row.login_user }}</span>
          </el-form-item>
          <el-form-item label="login_pass">
            <span>{{ props.row.login_pass }}</span>
          </el-form-item>
          <el-form-item label="node_uuid">
            <span>{{ props.row.node_uuid }}</span>
          </el-form-item>
          <el-form-item label="organization_uuid">
            <span>{{ props.row.organization_uuid }}</span>
          </el-form-item>
          <el-form-item label="storage_pool_uuid">
            <span>{{ props.row.storage_pool_uuid }}</span>
          </el-form-item>
          <el-form-item label="描述">
            <span>{{ props.row.desc }}</span>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column
      label="模板名称"
      prop="name"
      sortable>
    </el-table-column>
    <el-table-column
      label="系统"
      prop="os"
      sortable>
    </el-table-column>
   <el-table-column
      label="类型"
      prop="type"
      sortable>
    </el-table-column>
    <el-table-column align="right">
      <template slot="header">
        <el-input
          v-model="search"
          size="mini"
          placeholder="输入关键字搜索"/>
      </template>
      <template slot-scope="scope">
        <el-button
          size="mini"
          @click="handleEdit(scope.row)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="remove(scope.row.uuid)">删除</el-button>
        <el-button
          size="mini"
          type="info"
          icon="el-icon-document-copy"
          @click="clone(scope.row.uuid)">克隆</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<style>
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 135px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
body {
    margin: 0;
  }
</style>

<script>
  export default {
    data() {
      return {
        tableData: [],
        search: '',
        loading: true
      }
    },
    mounted () {
    let self = this
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl
    axios
      .get(self.GLOBAL.MaxCloudUrl+'/vm_tpl/list')
      .then(function (res){
          if (res.data.RespHead.ErrorCode==1004){
            self.$router.push({path: "/login"})
          }else{
            self.tableData = res.data.RespBody.Result
            self.loading = false
          }
      })
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  },
   methods:{
    query_task(uuid) {
      let self = this
      axios.get(self.GLOBAL.MaxCloudUrl+'/task?uuid='+uuid).then(function (res){
        if (res.data.RespBody.Result.status != 'PENDING' && res.data.RespBody.Result.status !="DEFAULT"){
          self.$message({
              message: res.data.RespBody.Result.message,
              type: 'success'
            });
        }else{
          self.query_task(uuid)
        }
        }).catch(function (error) { // 请求失败处理
          self.$.message.error(error)
        });
    },

    remove(uuid) {
      if(confirm("确定要删除吗？")){
        let data = [{"uuid": uuid}]
      let self = this
      axios
        .post(self.GLOBAL.MaxCloudUrl+'/vm_tpl/remove', data)
        .then(function (res){
          var data = res.data;
          if (data.RespHead.ErrorCode==0 && data.RespHead.Message=="SUCCESS"){
            $.each(self.tableData, function(index, tpl_data){
              if (tpl_data &&tpl_data["uuid"] == uuid){
                self.tableData.splice(index, 1)
                return true
              }
            });
            self.query_task(data.RespBody.Result.task_id)
          }else{
            self.$message({
              message: data.RespHead.Message,
              type: 'warning'
            });
          }
        })
        .catch(function (error) { // 请求失败处理
          self.$message.error(error);
        });
      }
    },

    clone() {
      this.$prompt('请输入邮箱', '克隆虚拟机', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        inputPattern: /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/,
        inputErrorMessage: '邮箱格式不正确'
      }).then(({ value }) => {
        this.$message({
          type: 'success',
          message: '你的邮箱是: ' + value
        });
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '取消任务'
        });       
      });
    },

    handleEdit(data){
      alert("功能正在开发中")
    }
  },
  }
</script>