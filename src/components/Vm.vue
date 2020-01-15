<template>
  <el-table
    :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
    v-loading="loading"
    style="width: 100%"
  >
    <el-table-column type="expand">
      <template slot-scope="props">
        <el-form label-position="left" inline class="demo-table-expand">
          <el-form-item label="id">
            <span>{{ props.row.id }}</span>
          </el-form-item>
          <el-form-item label="uuid">
            <span>{{ props.row.uuid }}</span>
          </el-form-item>
          <el-form-item label="服务器名称">
            <span>{{ props.row.name }}</span>
          </el-form-item>
          <el-form-item label="系统">
            <span>{{ props.row.os }}</span>
          </el-form-item>
          <el-form-item label="系统内存">
            <span>{{ props.row.memory }}</span>
          </el-form-item>
          <el-form-item label="系统cpu">
            <span>{{ props.row.cpu }}</span>
          </el-form-item>
          <el-form-item label="是否注册">
            <span>{{ props.row.on_define }}</span>
          </el-form-item>
          <el-form-item label="快速启动">
            <span>{{ props.row.quick_start }}</span>
          </el-form-item>
          <el-form-item label="状态">
            <span>{{ props.row.run_state }}</span>
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
          <el-form-item label="parent_uuid">
            <span>{{ props.row.parent_uuid }}</span>
          </el-form-item>
          <el-form-item label="this_snapshot">
            <span>{{ props.row.this_snapshot }}</span>
          </el-form-item>
          <el-form-item label="vnc_enable">
            <span>{{ props.row.vnc_enable }}</span>
          </el-form-item>
          <el-form-item label="vnc_pass">
            <span>{{ props.row.vnc_pass }}</span>
          </el-form-item>
          <el-form-item label="vnc_port">
            <span>{{ props.row.vnc_port }}</span>
          </el-form-item>
          <el-form-item label="vnc_token">
            <span>{{ props.row.vnc_token }}</span>
          </el-form-item>
          <el-form-item label="xml_name">
            <span>{{ props.row.xml_name }}</span>
          </el-form-item>
          <el-form-item label="xml_path">
            <span>{{ props.row.xml_path }}</span>
          </el-form-item>
          <el-form-item label="描述">
            <span>{{ props.row.desc }}</span>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column label="服务器名称" prop="name" sortable></el-table-column>
    <el-table-column label="系统" prop="os" sortable></el-table-column>
    <el-table-column label="系统内存" prop="memory" sortable></el-table-column>
    <el-table-column label="系统cpu" prop="cpu" sortable></el-table-column>
    <el-table-column label="是否注册" prop="on_define" sortable></el-table-column>
    <el-table-column label="快速启动" prop="quick_start" sortable></el-table-column>
    <el-table-column label="状态" prop="run_state" sortable></el-table-column>
    <el-table-column align="right">
      <template slot="header" slot-scope="scope">
        <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
      </template>
      <template slot-scope="scope">
        <el-button size="mini" @click="handleEdit(scope.row)" icon="el-icon-edit"></el-button>
        <el-button size="mini" type="danger" @click="remove(scope.row.uuid)" icon="el-icon-delete"></el-button>
        <el-link
          style="margin-left: 10px;"
          :href="MaxCloudUrl+'/vnc?token='+scope.row.vnc_token"
          target="_blank"
          icon="el-icon-connection"
          :underline="false"
        >VNC</el-link>
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
  width: 45%;
}
</style>

<script>
export default {
  data() {
    return {
      tableData: [],
      search: "",
      loading: true
    };
  },
  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/vm/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.tableData = res.data.RespBody.Result;
          self.loading = false;
          console.log(self.tableData)
        }
      })
      .catch(function(error) {
        // 请求失败处理
        console.log(error);
      });
  },
  methods: {
    query_task(uuid) {
      let self = this;
      axios
        .get(self.GLOBAL.MaxCloudUrl + "/task?uuid=" + uuid)
        .then(function(res) {
          if (
            res.data.RespBody.Result.status != "PENDING" &&
            res.data.RespBody.Result.status != "DEFAULT"
          ) {
            self.$message({
              message: res.data.RespBody.Result.message,
              type: "success"
            });
          } else {
            self.query_task(uuid);
          }
        })
        .catch(function(error) {
          // 请求失败处理
          self.$message.error(error);
        });
    },

    remove(uuid) {
      this.$confirm("此操作将永久删除该实例, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          let data = [{ uuid: uuid }];
          let self = this;
          axios
            .post(self.GLOBAL.MaxCloudUrl + "/vm/remove", data)
            .then(function(res) {
              var data = res.data;
              if (
                data.RespHead.ErrorCode == 0 &&
                data.RespHead.Message == "SUCCESS"
              ) {
                $.each(self.tableData, function(index, vm_data) {
                  if (vm_data && vm_data["uuid"] == uuid) {
                    self.tableData.splice(index, 1);
                    return true;
                  }
                });
                self.query_task(data.RespBody.Result.task_id);
              } else {
                self.$message({
                  message: data.RespHead.Message,
                  type: "warning"
                });
              }
            })
            .catch(function(error) {
              // 请求失败处理
              console.log(error);
              self.$message.error("系统错误");
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },

    handleEdit(data) {
      this.$message("功能正在开发中");
    }
  }
};
</script>