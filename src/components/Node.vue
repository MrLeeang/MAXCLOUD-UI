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
          <el-form-item label="节点名称">
            <span>{{ props.row.agent }}</span>
          </el-form-item>
          <el-form-item label="主机">
            <span>{{ props.row.host }}</span>
          </el-form-item>
          <el-form-item label="管理ip">
            <span>{{ props.row.manage_ip }}</span>
          </el-form-item>
          <el-form-item label="状态">
            <span>{{ props.row.is_working }}</span>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column label="节点名称" prop="agent" sortable></el-table-column>
    <el-table-column label="主机" prop="host" sortable></el-table-column>
    <el-table-column label="管理ip" prop="manage_ip" sortable></el-table-column>
    <el-table-column label="状态" prop="is_working" sortable></el-table-column>
    <el-table-column align="right">
      <template slot="header" slot-scope="scope">
        <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
      </template>
      <template slot-scope="scope">
        <el-button size="mini" @click="handleEdit(scope.row)">编辑</el-button>
        <el-button size="mini" type="danger" @click="remove(scope.row.uuid)">删除</el-button>
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
  width: 49%;
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
      .get(self.GLOBAL.MaxCloudUrl + "/node/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.tableData = res.data.RespBody.Result;
          self.loading = false;
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
          let data = { uuid: uuid };
          let self = this;
          axios
            .post(self.GLOBAL.MaxCloudUrl + "/node/remove", data)
            .then(function(res) {
              var data = res.data;
              if (
                data.RespHead.ErrorCode == 0 &&
                data.RespHead.Message == "SUCCESS"
              ) {
                $.each(self.tableData, function(index, node_data) {
                  if (node_data && node_data["uuid"] == uuid) {
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