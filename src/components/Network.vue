<template>
  <el-table
    :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
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
          <el-form-item label="网络名称">
            <span>{{ props.row.name }}</span>
          </el-form-item>
          <el-form-item label="主机数量">
            <span>{{ props.row.count }}</span>
          </el-form-item>
          <el-form-item label="vlan_id">
            <span>{{ props.row.vlan_id }}</span>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column
      label="网络名称"
      prop="name"
      sortable>
    </el-table-column>
    <el-table-column
      label="主机数量"
      prop="count"
      sortable>
    </el-table-column>
    <el-table-column
      label="vlan_id"
      prop="vlan_id"
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
          @click="handleEdit(scope.row)"
          icon="el-icon-edit"></el-button>
        <el-button
          size="mini"
          type="danger"
          @click="remove(scope.row.uuid, scope.row.vlan_id)"
          icon="el-icon-delete"></el-button>
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
</style>

<script>
  export default {
    data() {
      return {
        tableData: [],
        search: ''
      }
    },
    mounted () {
    let self = this
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl
    axios
      .get(self.GLOBAL.MaxCloudUrl+'/network/list')
      .then(function (res){
          if (res.data.RespHead.ErrorCode==1004){
            self.$router.push({path: "/login"})
          }else{
            self.tableData = res.data.RespBody.Result
            console.log(self.tableData)
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
          alert(res.data.RespBody.Result.message);
        }else{
          self.query_task(uuid)
        }
        }).catch(function (error) { // 请求失败处理
          alert(error);
        });
    },

    remove(uuid, vlan_id) {
      if(confirm("确定要删除吗？")){
        let data = [{"uuid": vm_uuid}]
      if (vlan_id == 0){
        alert("无法删除默认网络")
        return
      }
      let self = this
      axios
        .post(self.GLOBAL.MaxCloudUrl+'/network/remove', data)
        .then(function (res){
          var data = res.data;
          if (data.RespHead.ErrorCode==0 && data.RespHead.Message=="SUCCESS"){
            $.each(self.tableData, function(index, vm_data){
              if (vm_data["uuid"] == uuid){
                self.tableData.splice(index, 1)
              }
            });
            self.query_task(data.RespBody.Result.task_id)
          }else{
            alert(data.RespHead.Message);
          }
        })
        .catch(function (error) { // 请求失败处理
          alert(error);
        });
      }
    },

    handleEdit(data){
      alert("暂不支持编辑")
    }
  },
  }
</script>