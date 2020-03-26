<template>
  <el-form ref="add_firwall_form" :model="add_firwall_form" label-width="100px" class="add_firwall">
    <el-form-item label="防火墙名称" :label-width="formLabelWidth" prop="name">
      <el-input v-model="add_firwall_form.name"></el-input>
    </el-form-item>
    <div v-for="(zone, index) in add_firwall_form.zones" :key="'nc'+index">
      <el-form-item label="接口名称" style="text-align: left" :label-width="formLabelWidth">
        <el-input v-model="zone.name" :readonly="true"></el-input>
      </el-form-item>
      <el-form-item label="网络名称" :label-width="formLabelWidth">
        <el-select v-model="zone.network_uuid" placeholder="请选择网络">
          <el-option
            v-for="item in networks"
            :key="item.name"
            :label="item.name"
            :value="item.uuid"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="是否为静态" style="text-align: left" :label-width="formLabelWidth">
        <el-switch v-model="zone.is_static"></el-switch>
      </el-form-item>
      <el-form-item label="ip地址" :label-width="formLabelWidth">
        <el-input v-model="zone.ip_address" :disabled="!zone.is_static" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="掩码" :label-width="formLabelWidth">
        <el-input v-model="zone.net_mask" :disabled="!zone.is_static" autocomplete="off"></el-input>
      </el-form-item>
      <div v-if="index == 1">
        <el-form-item label="网关" :label-width="formLabelWidth">
          <el-input v-model="zone.gateway" :disabled="!zone.is_static" autocomplete="off"></el-input>
        </el-form-item>
      </div>
      <el-form-item :label-width="formLabelWidth" style="text-align: left">
        <el-button icon="el-icon-delete" @click.prevent="removeZone(zone)"></el-button>
      </el-form-item>
    </div>
    <el-form-item>
      <el-button @click="addZone">新增网卡</el-button>
      <el-button @click="resetForm">重置</el-button>
      <el-button type="primary" @click="addFirewall">创建</el-button>
    </el-form-item>
  </el-form>
</template>
<style>
.add_firwall {
  text-align: center;
  width: 40%;
  color: #99a9bf;
  position: absolute;
  left: 30%;
}

.el-select {
  width: 100%;
}
</style>
<script>
export default {
  data() {
    return {
      formLabelWidth: "120px",
      networks: [],
      add_firwall_form: {
        name: "",
        zones: [
          {
            network_uuid: "",
            name: "LAN",
            is_static: false,
            ip_address: "",
            net_mask: "",
            gateway: ""
          }
        ]
      }
    };
  },
  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
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

    addFirewall() {
      let data = this.add_firwall_form;
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/firewall/add", data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode === 0 &&
            data.RespHead.Message === "SUCCESS"
          ) {
            self.query_task(data.RespBody.Result.task_id);
          }else{
            self.$message.error(data.RespHead.Message);
          }
        })
        .catch(function(error) {
          console.log(error);
          self.$message.error("系统错误");
        });
    },
    addZone() {
      this.add_firwall_form.zones.push({
        network_uuid: "",
        is_static: true,
        ip_address: "",
        net_mask: "",
        gateway: "",
        name: ""
      });
      let zonenamelist = ["LAN", "WAN", "OPT"];
      let num = 1;
      $.each(this.add_firwall_form.zones, function(index, zone) {
        if (index > 1) {
          zone.name = "OPT" + num;
          num += 1;
        } else {
          zone.name = zonenamelist[index];
        }
      });
    },

    removZone(item) {
      var index = this.add_firwall_form.net_cards.indexOf(item);
      if (index !== -1) {
        this.add_firwall_form.net_cards.splice(index, 1);
      }
      let zonenamelist = ["LAN", "WAN", "OPT"];
      let num = 1;
      $.each(this.add_firwall_form.zones, function(index, zone) {
        if (index > 1) {
          zone.name = "OPT" + num;
          num += 1;
        } else {
          zone.name = zonenamelist[index];
        }
      });
    },
    resetForm() {
      this.add_firwall_form = {
        name: "",
        zones: [
          {
            network_uuid: "",
            is_static: true,
            ip_address: "",
            net_mask: "",
            gateway: "",
            name: "LAN"
          }
        ]
      };
      this.$message("数据已重置");
    }
  }
};
</script>
