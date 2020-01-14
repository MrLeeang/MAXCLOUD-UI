<template>
  <div>
    <el-table
      :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
      v-loading="loading"
      style="width: 100%"
      row-key="uuid"
      :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
    >
      <el-table-column label="防火墙名称" prop="firewall_name" sortable></el-table-column>
      <el-table-column label="接口名称" prop="name" sortable></el-table-column>
      <el-table-column label="is_static" prop="is_static" sortable></el-table-column>
      <el-table-column label="ip_address" prop="ip_address" sortable></el-table-column>
      <el-table-column label="net_mask" prop="net_mask" sortable></el-table-column>
      <el-table-column label="gateway" prop="gateway" sortable></el-table-column>
      <el-table-column label="network_uuid" prop="network_uuid" sortable></el-table-column>
      <el-table-column label="port_key" prop="port_key" sortable></el-table-column>
      <el-table-column align="right">
        <template slot="header" slot-scope="scope">
          <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
        </template>
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)" icon="el-icon-edit"></el-button>
          <el-button size="mini" type="danger" @click="remove(scope.row)" icon="el-icon-delete"></el-button>
          <el-button
            v-if="scope.row.zones"
            size="mini"
            type="danger"
            @click="add_interface(scope.row)"
            icon="el-icon-circle-plus-outline"
          ></el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog title="添加防火墙接口" :visible.sync="dialogFormVisible" top="45px" width="650px">
      <el-form :model="add_interface_data" :label-position="labelPosition">
        <el-form-item label="网络名称" :label-width="formLabelWidth">
          <el-select v-model="add_interface_data.network_uuid" placeholder="请选择网络">
            <el-option
              v-for="item in networks"
              :key="item.name"
              :label="item.name"
              :value="item.uuid"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="ip地址" :label-width="formLabelWidth">
          <el-input v-model="add_interface_data.ip_address" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="掩码" :label-width="formLabelWidth">
          <el-input v-model="add_interface_data.net_mask" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="网关" :label-width="formLabelWidth">
          <el-input
            v-model="add_interface_data.gateway"
            :disabled="add_interface_data.not_wan"
            autocomplete="off"
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="add_interface_btn">确 定</el-button>
      </div>
    </el-dialog>
  </div>
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
.el-dialog .el-form {
  width: 80%;
}

.el-dialog .el-form .el-select {
  width: 100%;
}

.el-dialog .el-form .el-form-item {
  text-align: left;
}
</style>

<script>
export default {
  data() {
    return {
      tableData: [],
      networks: [],
      search: "",
      dialogFormVisible: false,
      add_interface_data: {
        firewall_uuid: "",
        zones: [],
        not_wan: true,
        gateway: "",
        network_uuid: "",
        ip_address: "",
        net_mask: "",
        is_static: 1
      },
      loading: true,
      formLabelWidth: "150px",
      labelPosition: "left"
    };
  },
  mounted() {
    let self = this;
    self.tableData = [];
    self.networks = [];
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/firewall/query")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.Result = res.data.RespBody.Result;
          $.each(self.Result, function(index, firewall_data) {
            var firewall_childrens = [];
            $.each(firewall_data["zones"], function(index, zone_data) {
              zone_data["firewall_name"] = firewall_data["name"];
              zone_data["firewall_organization_uuid"] =
                firewall_data["firewall_organization_uuid"];
              zone_data["firewall_node_uuid"] =
                firewall_data["firewall_node_uuid"];
              firewall_childrens.push(zone_data);
            });
            firewall_data["children"] = firewall_childrens;
            firewall_data["firewall_name"] = firewall_data["name"];
            firewall_data["name"] = "";
            self.tableData.push(firewall_data);
          });
          self.loading = false;
        }
      })
      .catch(function(error) {
        // 请求失败处理
        console.log(error);
      });

    axios
      .get(self.GLOBAL.MaxCloudUrl + "/network/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.networks = res.data.RespBody.Result;
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
          // 先确认删除防火墙还是接口
          // let data = [{ uuid: uuid }];
          // let self = this;
          // axios
          //   .post(self.GLOBAL.MaxCloudUrl + "/vm/remove", data)
          //   .then(function(res) {
          //     var data = res.data;
          //     if (
          //       data.RespHead.ErrorCode == 0 &&
          //       data.RespHead.Message == "SUCCESS"
          //     ) {
          //       $.each(self.tableData, function(index, tmp_data) {
          //         if (tmp_data && tmp_data["uuid"] == uuid) {
          //           self.tableData.splice(index, 1);
          //           return true;
          //         }
          //       });
          //       self.query_task(data.RespBody.Result.task_id);
          //     } else {
          //       self.$message({
          //         message: data.RespHead.Message,
          //         type: "warning"
          //       });
          //     }
          //   })
          //   .catch(function(error) {
          //     // 请求失败处理
          //     console.log(error)
          //     self.$message.error("系统错误");
          //   });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },

    handleEdit(index, data) {
      console.log(data);
    },
    add_interface(firewall_data) {
      this.add_interface_data.firewall_uuid = firewall_data["uuid"];
      this.add_interface_data.network_uuid = "";
      this.add_interface_data.ip_address = "";
      this.add_interface_data.net_mask = "";
      this.add_interface_data.gateway = "";
      this.dialogFormVisible = true;
      if (firewall_data["children"].length == 1) {
        this.add_interface_data.not_wan = false;
      }
    },
    add_interface_btn() {
      let post_data = {
        firewall_uuid: this.add_interface_data.firewall_uuid,
        zones: [
          {
            network_uuid: this.add_interface_data.network_uuid,
            ip_address: this.add_interface_data.ip_address,
            net_mask: this.add_interface_data.net_mask,
            is_static: this.add_interface_data.is_static,
            gateway: this.add_interface_data.gateway
          }
        ]
      };
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/firewall/interface/add", post_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
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
      this.dialogFormVisible = false;
    }
  }
};
</script>