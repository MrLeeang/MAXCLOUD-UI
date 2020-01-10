<template>
    <el-row>
      <el-col :span="4" v-for="i in result" :key="i">
        <div class="vm_box" :id="i.uuid">
          <el-row>
            <el-col :span="24">
              <div class="grid-content bg-purple-dark vm_title">{{i.name}}</div>
            </el-col>
            <el-col :span="12">
              <el-link :href="MaxCloudUrl+'/vnc?token='+i.vnc_token" target="_blank" icon="el-icon-connection" :underline="false" style="height: 25px;">VNC</el-link>
            </el-col>
            <el-col :span="12">
              <el-button type="danger" size="mini" icon="el-icon-delete" style="height: 25px;" @click="remove_vm(i.uuid)">删除</el-button>
            </el-col>
          </el-row>
        </div>
      </el-col>
    </el-row>
</template>

<script>
export default {
  data () {
    return {
        result: [],
        MaxCloudUrl: ""
    }
  },
  mounted () {
    let self = this
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl
    axios
      .get(self.GLOBAL.MaxCloudUrl+'/vm/list')
      .then(function (res){
          if (res.data.RespHead.ErrorCode==1004){
            self.$router.push({path: "/login"})
          }else{
            self.result = res.data.RespBody.Result
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

    remove_vm(vm_uuid) {
      if(confirm("确定要删除吗？")){
        let data = [{"uuid": vm_uuid}]
      let self = this
      axios
        .post(self.GLOBAL.MaxCloudUrl+'/vm/remove', data)
        .then(function (res){
          var data = res.data;
          if (data.RespHead.ErrorCode==0 && data.RespHead.Message=="SUCCESS"){
            $("#"+vm_uuid).hide();
            self.query_task(data.RespBody.Result.task_id)
          }else{
            alert(data.RespHead.Message);
          }
        })
        .catch(function (error) { // 请求失败处理
          alert(error);
        });
      }
    }
  },

}
</script>
<style>
.vm_box {
  width: 300px;
  height: 300px;
  border:1px solid rgba(193, 187, 187, 0.5);
  margin-bottom: 15px;
  margin-right: 5px;
}

.vm_title {
  background-color: aquamarine;
  margin-top: 0px;
}
</style>