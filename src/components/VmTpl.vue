<template>
  <div>
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
      <el-table-column label="模板名称" prop="name" sortable></el-table-column>
      <el-table-column label="系统" prop="os" sortable></el-table-column>
      <el-table-column label="类型" prop="type" sortable></el-table-column>
      <el-table-column align="right">
        <template slot="header" slot-scope="scope">
          <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
        </template>
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="remove(scope.row.uuid)">删除</el-button>
          <el-button
            size="mini"
            type="info"
            icon="el-icon-document-copy"
            @click="clone(scope.row.uuid)"
          >克隆</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog title="克隆虚拟机" :visible.sync="dialogFormVisible" top="45px" width="650px">
      <el-form :model="form" :label-position="labelPosition">
        <el-form-item label="虚拟机名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="cpu" :label-width="formLabelWidth">
          <el-select v-model="form.cpu" placeholder="请选择cpu数量">
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
            <el-option label="4" value="4"></el-option>
            <el-option label="8" value="8"></el-option>
            <el-option label="16" value="16"></el-option>
            <el-option label="32" value="32"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="内存" :label-width="formLabelWidth">
          <el-select v-model="form.mem" placeholder="请选择内存大小">
            <el-option label="1" value="1048576"></el-option>
            <el-option label="2" value="2097152"></el-option>
            <el-option label="4" value="4194304"></el-option>
            <el-option label="8" value="8388608"></el-option>
            <el-option label="16" value="16777216"></el-option>
            <el-option label="32" value="33554432"></el-option>
          </el-select>
        </el-form-item>
        <div v-for="(net_card, index) in form.net_cards" :key="index">
          <el-form-item label="是否为DHCP" style="text-align: left" :label-width="formLabelWidth">
            <el-switch v-model="net_card.is_static"></el-switch>
          </el-form-item>
          <el-form-item label="ip地址" :label-width="formLabelWidth">
            <el-input
              v-model="net_card.ip_address"
              :disabled="net_card.is_static"
              autocomplete="off"
            ></el-input>
          </el-form-item>
          <el-form-item label="网关" :label-width="formLabelWidth">
            <el-input v-model="net_card.gateway" :disabled="net_card.is_static" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="网络名称" :label-width="formLabelWidth">
            <el-select v-model="net_card.network.name" placeholder="请选择网络">
              <el-option
                v-for="item in networks"
                :key="item.name"
                :label="item.name"
                :value="item.name"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item :label-width="formLabelWidth" style="text-align: left">
            <el-button icon="el-icon-delete" @click.prevent="removeNetCard(net_card)"></el-button>
          </el-form-item>
        </div>

        <div v-for="(disk, index) in form.disks" :key="index">
          <el-form-item label="name" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.name"></el-input>
          </el-form-item>
          <el-form-item label="type" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.type"></el-input>
          </el-form-item>
          <el-form-item label="device" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.device"></el-input>
          </el-form-item>
          <el-form-item label="driver_name" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.driver_name"></el-input>
          </el-form-item>
          <el-form-item label="driver_type" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.driver_type"></el-input>
          </el-form-item>
          <el-form-item label="source_file" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.source_file"></el-input>
          </el-form-item>
          <el-form-item label="size" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.size"></el-input>
          </el-form-item>
          <el-form-item label="dev" :label-width="formLabelWidth" style="text-align: left">
            <el-input v-model="disk.dev"></el-input>
          </el-form-item>
          <el-form-item :label-width="formLabelWidth" style="text-align: left">
            <el-button icon="el-icon-delete" @click.prevent="removeDisk(disk)"></el-button>
          </el-form-item>
        </div>
        <!-- <el-form-item label="网络名称" :label-width="formLabelWidth">
          <el-select v-model="form.network_name1" placeholder="请选择网络">
            <el-option
              v-for="item in networks"
              :key="item.name"
              :label="item.name"
              :value="item.name"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="是否为DHCP" :label-width="formLabelWidth">
          <el-switch v-model="form.is_static1"></el-switch>
        </el-form-item>

        <el-form-item label="ip地址" :label-width="formLabelWidth">
          <el-input v-model="form.ip_address1" :disabled="form.is_static1" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="网关" :label-width="formLabelWidth">
          <el-input v-model="form.gateway1" :disabled="form.is_static1" autocomplete="off"></el-input>
        </el-form-item>-->

        <el-form-item label="VNC" :label-width="formLabelWidth">
          <el-switch v-model="form.vnc"></el-switch>
        </el-form-item>
        <el-form-item label="描述" :label-width="formLabelWidth">
          <el-input type="textarea" v-model="form.desc"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addDisk">新增磁盘</el-button>
        <el-button @click="addNetCard">新增网卡</el-button>
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="btn_clone">确 定</el-button>
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

body {
  margin: 0;
}
</style>

<script>
export default {
  data() {
    return {
      networks: [],
      tableData: [],
      search: "",
      loading: true,
      dialogFormVisible: false,
      form: {
        name: "",
        tpl_uuid: "",
        mem: "",
        cpu: "",
        vnc: "",
        disks: [],
        net_cards: [
          {
            name: "网卡1",
            is_static: false,
            ip_address: "",
            mac_address: "",
            gateway: "",
            index: 0,
            network: { name: "" }
          }
        ],
        desc: ""
      },
      formLabelWidth: "150px",
      labelPosition: "left"
    };
  },
  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/vm_tpl/list")
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
            if (res.data.RespBody.Result.status == "SUCCESS") {
              self.$message({
                message: res.data.RespBody.Result.message,
                type: "success"
              });
            } else {
              self.$message.error(res.data.RespBody.Result.message);
            }
          } else {
            self.query_task(uuid);
          }
        })
        .catch(function(error) {
          // 请求失败处理
          self.$.message.error(error);
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
            .post(self.GLOBAL.MaxCloudUrl + "/vm_tpl/remove", data)
            .then(function(res) {
              var data = res.data;
              if (
                data.RespHead.ErrorCode == 0 &&
                data.RespHead.Message == "SUCCESS"
              ) {
                $.each(self.tableData, function(index, tpl_data) {
                  if (tpl_data && tpl_data["uuid"] == uuid) {
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

    clone(uuid) {
      this.dialogFormVisible = true;
      this.form.tpl_uuid = uuid;
      this.form.name = "";
      this.form.mem = "";
      this.form.cpu = "";
      this.form.vnc = true;
      this.form.disks = [];
      this.form.net_cards = [
        {
          name: "网卡1",
          is_static: false,
          ip_address: "",
          mac_address: "",
          gateway: "",
          index: 0,
          network: { name: "" }
        }
      ];
      this.form.desc = "";
    },

    btn_clone() {
      this.dialogFormVisible = false;
      var post_date = {
        name: this.form.name,
        tpl_uuid: this.form.tpl_uuid,
        vnc_enable: 1,
        desc: this.form.desc,
        cpu: this.form.cpu,
        memory: this.form.mem,
        net_cards: this.form.net_cards,
        disks: this.form.disks
      };
      if (!this.form.vnc) {
        post_date["vnc_enable"] = 0;
      }
      // var net_card1 = {
      //   name: this.form.net_card_name1,
      //   index: 0,
      //   network: { name: this.form.network_name1 },
      //   is_static: 0,
      //   ip_address: "",
      //   gateway: ""
      // };
      // if (!this.form.is_static1) {
      //   net_card1["is_static"] = 1;
      //   net_card1["ip_address"] = this.form.ip_address1;
      //   net_card1["gateway"] = this.form.gateway1;
      // }
      // post_date["net_cards"].push(net_card1);

      let self = this;

      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/clone", post_date)
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
    },

    handleEdit(data) {
      this.$message("功能正在开发中");
    },

    addNetCard() {
      this.form.net_cards.push({
        name: "",
        index: "",
        type: "network",
        mac_address: "",
        is_static: 0,
        ip_address: "",
        network: {
          name: ""
        }
      });
    },
    addDisk() {
      this.form.disks.push({
        name: "",
        type: "",
        device: "",
        driver_name: "",
        driver_type: "",
        source_file: "",
        size: "",
        dev: ""
      });
    },

    removeDisk(item) {
      var index = this.form.disks.indexOf(item);
      if (index !== -1) {
        this.form.disks.splice(index, 1);
      }
    },
    removeNetCard(item) {
      var index = this.form.net_cards.indexOf(item);
      if (index !== -1) {
        this.form.net_cards.splice(index, 1);
      }
    }
  }
};
</script>