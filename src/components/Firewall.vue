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
          <el-dropdown size="mini" split-button type="danger" @click="remove(scope.row)">
            删除
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>
                <el-button
                  v-if="scope.row.zones"
                  icon="el-icon-circle-plus"
                  size="mini"
                  type="text"
                  @click="add_interface(scope.row)"
                >添加接口</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  type="text"
                  icon="el-icon-edit"
                  size="mini"
                  @click="handleEdit(scope.row)"
                >编辑</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  v-if="scope.row.zones"
                  type="text"
                  icon="el-icon-circle-plus"
                  size="mini"
                  @click="add_nat(scope.row)"
                >添加NAT规则</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  v-if="scope.row.zones"
                  type="text"
                  icon="el-icon-circle-plus"
                  size="mini"
                  @click="add_rule(scope.row)"
                >添加防火墙规则</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  v-if="scope.row.zones"
                  type="text"
                  icon="el-icon-circle-plus"
                  size="mini"
                  @click="add_router(scope.row)"
                >添加路由规则</el-button>
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
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

    <el-dialog title="添加NAT规则" :visible.sync="dialogFormVisiblenat" top="45px" width="650px">
      <el-form :model="add_nat_data" :label-position="labelPosition">
        <el-form-item label="rule_type" :label-width="formLabelWidth">
          <el-select v-model="add_nat_data.zones[0].nats[0].rule_type" placeholder="请选择nat类型">
            <el-option key="入口规则" label="入口规则" value="入口规则"></el-option>
            <el-option key="出口规则" label="出口规则" value="出口规则"></el-option>
          </el-select>
        </el-form-item>
        <div v-if="add_nat_data.zones[0].nats[0].rule_type == '出口规则'">
          <el-form-item label="inside_ip" :label-width="formLabelWidth">
            <el-input v-model="add_nat_data.zones[0].nats[0].inside_ip" autocomplete="off"></el-input>
          </el-form-item>
        </div>
        <div v-if="add_nat_data.zones[0].nats[0].rule_type == '入口规则'">
          <el-form-item label="inside_ip" :label-width="formLabelWidth">
            <el-input v-model="add_nat_data.zones[0].nats[0].inside_ip" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="start_nat_port" :label-width="formLabelWidth">
            <el-input v-model="add_nat_data.zones[0].nats[0].start_nat_port" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="end_nat_port" :label-width="formLabelWidth">
            <el-input v-model="add_nat_data.zones[0].nats[0].end_nat_port" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="inside_port" :label-width="formLabelWidth">
            <el-input v-model="add_nat_data.zones[0].nats[0].inside_port" autocomplete="off"></el-input>
          </el-form-item>
        </div>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisiblenat = false">取 消</el-button>
        <el-button type="primary" @click="add_nat_btn">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog title="添加防火墙规则" :visible.sync="dialogFormVisiblerule" top="45px" width="650px">
      <el-form :model="add_rule_data" :label-position="labelPosition">
        <el-form-item label="防火墙接口" :label-width="formLabelWidth">
          <el-select v-model="add_rule_data.zones[0].zone_uuid" placeholder="请选择接口">
            <el-option
              v-for="item in this_firewall.zones"
              :key="item.name"
              :label="item.name"
              :value="item.uuid"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="动作" :label-width="formLabelWidth">
          <el-select v-model="add_rule_data.zones[0].rules[0].action_type" placeholder="请选择允许类型">
            <el-option key="允许" label="允许" value="允许"></el-option>
            <el-option key="拒绝" label="拒绝" value="拒绝"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="规则" :label-width="formLabelWidth">
          <el-select v-model="add_rule_data.zones[0].rules[0].rule_type" placeholder="请选择规则类型">
            <el-option key="入口规则" label="入口规则" value="入口规则"></el-option>
            <el-option key="出口规则" label="出口规则" value="出口规则"></el-option>
            <el-option key="转发规则" label="转发规则" value="转发规则"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="来源ip类型" :label-width="formLabelWidth">
          <el-select v-model="add_rule_data.zones[0].rules[0].source_type" placeholder="源地址类型">
            <el-option key="单个主机" label="单个主机" value="host"></el-option>
            <el-option key="网络" label="网络" value="network"></el-option>
            <el-option key="any" label="any" value="any"></el-option>
          </el-select>
        </el-form-item>
        <div v-if="add_rule_data.zones[0].rules[0].source_type=='host'">
          <el-form-item label="源ip" :label-width="formLabelWidth">
            <el-input v-model="add_rule_data.zones[0].rules[0].source_ip" autocomplete="off">
              <template slot="append">/32</template>
            </el-input>
          </el-form-item>
        </div>
        <div v-if="add_rule_data.zones[0].rules[0].source_type=='network'">
          <el-form-item label="源ip" :label-width="formLabelWidth">
            <el-input v-model="add_rule_data.zones[0].rules[0].source_ip" autocomplete="off"></el-input>
          </el-form-item>
        </div>
        <el-form-item label="源端口" :label-width="formLabelWidth">
          <el-row>
            <el-col :span="10" class="port_class">
              <el-select
                v-model="add_rule_data.zones[0].rules[0].source_port_begin"
                placeholder="源端口"
              >
                <el-option key="ftp" label="ftp" value="21"></el-option>
                <el-option key="ssh" label="ssh" value="22"></el-option>
                <el-option key="telnet" label="telnet" value="22"></el-option>
                <el-option key="smtp" label="telnet" value="25"></el-option>
                <el-option key="dns" label="dns" value="53"></el-option>
                <el-option key="http" label="http" value="80"></el-option>
                <el-option key="https" label="https" value="443"></el-option>
                <el-option key="pop3" label="pop3" value="110"></el-option>
                <el-option key="imap" label="imap" value="143"></el-option>
                <el-option key="其他" label="其他" value></el-option>
              </el-select>
            </el-col>
            <el-col :span="10">
              <el-input
                v-model="add_rule_data.zones[0].rules[0].source_port_begin"
                autocomplete="off"
              ></el-input>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item label="目标ip类型" :label-width="formLabelWidth">
          <el-select v-model="add_rule_data.zones[0].rules[0].destination_type" placeholder="源地址类型">
            <el-option key="单个主机" label="单个主机" value="host"></el-option>
            <el-option key="网络" label="网络" value="network"></el-option>
            <el-option key="any" label="any" value="any"></el-option>
          </el-select>
        </el-form-item>
        <div v-if="add_rule_data.zones[0].rules[0].destination_type=='network'">
          <el-form-item label="目标ip" :label-width="formLabelWidth">
            <el-input v-model="add_rule_data.zones[0].rules[0].destination_ip" autocomplete="off"></el-input>
          </el-form-item>
        </div>
        <div v-if="add_rule_data.zones[0].rules[0].destination_type=='host'">
          <el-form-item label="目标ip" :label-width="formLabelWidth">
            <el-input v-model="add_rule_data.zones[0].rules[0].destination_ip" autocomplete="off">
              <template slot="append">/32</template>
            </el-input>
          </el-form-item>
        </div>
        <el-form-item label="目标端口" :label-width="formLabelWidth">
          <el-row>
            <el-col :span="10" class="port_class">
              <el-select
                v-model="add_rule_data.zones[0].rules[0].destination_port_begin"
                placeholder="目标端口"
              >
                <el-option key="ftp" label="ftp" value="21"></el-option>
                <el-option key="ssh" label="ssh" value="22"></el-option>
                <el-option key="telnet" label="telnet" value="22"></el-option>
                <el-option key="smtp" label="telnet" value="25"></el-option>
                <el-option key="dns" label="dns" value="53"></el-option>
                <el-option key="http" label="http" value="80"></el-option>
                <el-option key="https" label="https" value="443"></el-option>
                <el-option key="pop3" label="pop3" value="110"></el-option>
                <el-option key="imap" label="imap" value="143"></el-option>
                <el-option key="其他" label="其他" value></el-option>
              </el-select>
            </el-col>
            <el-col :span="10">
              <el-input
                v-model="add_rule_data.zones[0].rules[0].destination_port_begin"
                autocomplete="off"
              ></el-input>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisiblerule = false">取 消</el-button>
        <el-button type="primary" @click="add_rule_btn">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog title="添加路由规则" :visible.sync="dialogFormVisiblerouter" top="45px" width="650px">
      <el-form :model="add_router_data" :label-position="labelPosition">
        <el-form-item label="网络" :label-width="formLabelWidth">
          <el-input v-model="add_router_data.router_rules[0].network" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="下一跳网关" :label-width="formLabelWidth">
          <el-input v-model="add_router_data.router_rules[0].gateway" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisiblerouter = false">取 消</el-button>
        <el-button type="primary" @click="add_router_btn">确 定</el-button>
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
  width: 49%;
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
.port_class {
  padding-right: 10px;
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
      dialogFormVisiblenat: false,
      dialogFormVisiblerule: false,
      dialogFormVisiblerouter: false,
      this_firewall: { zones: [] },
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
      add_router_data: {
        firewall_uuid: "",
        router_rules: [
          {
            network: "192.168.20.0/24",
            gateway: "192.168.10.1",
            desc: ""
          }
        ]
      },
      add_rule_data: {
        firewall_uuid: null,
        zones: [
          {
            zone_uuid: null,
            rules: [
              {
                action_type: "允许",
                rule_type: "入口规则",
                protocol: "tcp",
                source_type: "any",
                source_ip: null,
                source_port_begin: null,
                destination_type: "any",
                destination_ip: null,
                destination_port_begin: null
              }
            ]
          }
        ]
      },
      add_nat_data: {
        firewall_uuid: null,
        zones: [
          {
            zone_uuid: null,
            nats: [
              {
                rule_type: "入口规则",
                inside_ip: null,
                protocol: "tcp",
                start_nat_port: null,
                end_nat_port: null,
                inside_port: null
              }
            ]
          }
        ]
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

    remove(row) {
      let self = this;
      this.$confirm("此操作将永久删除该实例, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          // 先确认删除防火墙还是接口
          if (row.port_key) {
            // 接口
            let data = { interface_uuid: row.uuid };
            axios
              .post(
                self.GLOBAL.MaxCloudUrl + "/firewall/interface/remove",
                data
              )
              .then(function(res) {
                var data = res.data;
                if (
                  data.RespHead.ErrorCode == 0 &&
                  data.RespHead.Message == "SUCCESS"
                ) {
                  $.each(self.tableData, function(index, tmp_data) {
                    if (tmp_data && tmp_data["uuid"] == row.firewall_uuid) {
                      $.each(tmp_data, function(i, t) {
                        if (t && t["uuid"] == row.uuid) {
                          self.tableData[index].splice(i, 1);
                          return true;
                        }
                      });
                    }
                  });
                  self.query_task(data.RespBody.Result.task_id);
                } else {
                  self.$message({
                    message: data.RespHead.Message,
                    type: "warning"
                  });
                }
              });
          } else {
            // 防火墙
            let data = { firewall_uuid: row.uuid };
            axios
              .post(self.GLOBAL.MaxCloudUrl + "/firewall/remove", data)
              .then(function(res) {
                var data = res.data;
                if (
                  data.RespHead.ErrorCode == 0 &&
                  data.RespHead.Message == "SUCCESS"
                ) {
                  $.each(self.tableData, function(index, tmp_data) {
                    if (tmp_data && tmp_data["uuid"] == row.uuid) {
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
          }
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
      this.$message("功能开发中");
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
    },
    add_nat(firewall_data) {
      if (firewall_data["children"].length == 1) {
        self.$message.error("没有WAN口");
        return;
      }
      this.add_nat_data = {
        firewall_uuid: firewall_data.uuid,
        zones: [
          {
            zone_uuid: firewall_data.zones[1].uuid,
            nats: [
              {
                rule_type: "入口规则",
                inside_ip: null,
                protocol: "tcp",
                start_nat_port: null,
                end_nat_port: null,
                inside_port: null
              }
            ]
          }
        ]
      };
      this.dialogFormVisiblenat = true;
    },
    add_nat_btn() {
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/firewall/nat/add", self.add_nat_data)
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
      self.dialogFormVisiblenat = false;
    },
    add_rule(firewall_data) {
      this.this_firewall = firewall_data;
      this.add_rule_data = {
        firewall_uuid: firewall_data.uuid,
        zones: [
          {
            zone_uuid: null,
            rules: [
              {
                action_type: "允许",
                rule_type: "入口规则",
                protocol: "tcp",
                source_type: "host",
                source_ip: null,
                source_port_begin: null,
                destination_type: "any",
                destination_ip: null,
                destination_port_begin: null
              }
            ]
          }
        ]
      };
      this.dialogFormVisiblerule = true;
      console.log(this.add_rule_data);
    },
    add_rule_btn() {
      let self = this;
      console.log(self.add_rule_data);
      axios
        .post(
          self.GLOBAL.MaxCloudUrl + "/firewall/rule/add",
          self.add_rule_data
        )
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
      self.dialogFormVisiblerule = false;
    },
    add_router(firewall_data){
      this.add_router_data = {
        firewall_uuid: firewall_data.uuid,
        router_rules: [
          {
            network: "192.168.20.0/24",
            gateway: "192.168.10.1",
            desc: ""
          }
        ]
      };
      this.dialogFormVisiblerouter = true;
    },
    add_router_btn(){
      let self = this;
      axios
        .post(
          self.GLOBAL.MaxCloudUrl + "/firewall/router_rule/add",
          self.add_router_data
        )
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
      self.dialogFormVisiblerouter = false;
    }
  }
};
</script>