<template>
  <el-table
    :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
    v-loading="loading"
    style="width: 100%"
     row-key="uuid"
    :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
    >
    <el-table-column
      label="防火墙名称"
      prop="firewall_name"
      sortable>
    </el-table-column>
    <el-table-column
      label="接口名称"
      prop="name"
      sortable>
    </el-table-column>
    <el-table-column
      label="is_static"
      prop="is_static"
      sortable>
    </el-table-column>
    <el-table-column
      label="ip_address"
      prop="ip_address"
      sortable>
    </el-table-column>
    <el-table-column
      label="net_mask"
      prop="net_mask"
      sortable>
    </el-table-column>
    <el-table-column
      label="gateway"
      prop="gateway"
      sortable>
    </el-table-column>
    <el-table-column
      label="network_uuid"
      prop="network_uuid"
      sortable>
    </el-table-column>
    <el-table-column
      label="port_key"
      prop="port_key"
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
          @click="handleEdit(scope.$index, scope.row)"
          icon="el-icon-edit"></el-button>
        <el-button
          size="mini"
          type="danger"
          @click="remove(scope.row)"
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
        search: '',
        loading: true
      }
    },
    mounted () {
    let self = this
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl
    axios
      .get(self.GLOBAL.MaxCloudUrl+'/firewall/query')
      .then(function (res){
          if (res.data.RespHead.ErrorCode==1004){
            self.$router.push({path: "/login"})
          }else{
            self.Result = res.data.RespBody.Result
             $.each(self.Result, function(index, firewall_data){
              var firewall_childrens = []
              $.each(firewall_data["zones"], function(index, zone_data){
                zone_data["firewall_name"] = firewall_data["name"]
                zone_data["firewall_organization_uuid"] = firewall_data["firewall_organization_uuid"]
                zone_data["firewall_node_uuid"] = firewall_data["firewall_node_uuid"]
                firewall_childrens.push(zone_data)
            });
            firewall_data['children']=firewall_childrens
            firewall_data['firewall_name']=firewall_data["name"]
            firewall_data['name']=''
            self.tableData.push(firewall_data)
            });
            self.loading = false
          }
          console.log(self.tableData)
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
          self.$message.error(error)
        });
    },

    remove(data) {
      if(confirm("确定要删除吗？")){
        // 先判断是删除防火墙还是防火墙接口
      //   let data = [{"uuid": vm_uuid}]
      // let self = this
      // axios
      //   .post(self.GLOBAL.MaxCloudUrl+'/vm/remove', data)
      //   .then(function (res){
      //     var data = res.data;
      //     if (data.RespHead.ErrorCode==0 && data.RespHead.Message=="SUCCESS"){
      //       $.each(self.tableData, function(index, firewall_data){
      //         if (firewall_data && firewall_data["uuid"] == vm_uuid){
      //           self.tableData.splice(index, 1)
      //         }
      //       });
      //       self.query_task(data.RespBody.Result.task_id)
      //     }else{
      //       self.$message({
      //         message: data.RespHead.Message,
      //         type: 'warning'
      //       });
      //     }
      //   })
      //   .catch(function (error) { // 请求失败处理
      //     self.$message.error(error);
      //   });
      }
    },
    
    handleEdit(index, data){
      console.log(data)
    }

}
  }
</script>