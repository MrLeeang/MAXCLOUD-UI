<template>
  <div class="upload_page">
    <el-row :gutter="20">
      <el-col :span="6" :offset="8">
        <el-form ref="form" :model="form" :label-width="formLabelWidth">
          <el-form-item label="网络名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="vlan">
            <el-input v-model="form.vlan_id"></el-input>
          </el-form-item>
          <el-form-item label="cidr">
            <el-input v-model="form.cidr"></el-input>
          </el-form-item>
          <el-form-item label="开启DHCP" :label-width="formLabelWidth">
            <el-switch v-model="form.is_dhcp"></el-switch>
          </el-form-item>
          <el-form-item label="起始ip地址" :label-width="formLabelWidth">
            <el-input v-model="form.dhcp.start_ip" :disabled="!form.is_dhcp" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="结束ip地址" :label-width="formLabelWidth">
            <el-input v-model="form.dhcp.end_ip" :disabled="!form.is_dhcp" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="掩码" :label-width="formLabelWidth">
            <el-input v-model="form.dhcp.netmask" :disabled="!form.is_dhcp" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="网关" :label-width="formLabelWidth">
            <el-input v-model="form.dhcp.gateway" :disabled="!form.is_dhcp" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">立即创建</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>
<style>
.upload_page .el-form {
  margin-top: 65px;
}

.el-form .el-upload__input {
  display: none;
}

.el-form .el-upload__tip {
  font-size: 12px;
  color: #606266;
  margin-top: 7px;
}

.el-form-item__content {
  line-height: 20px;
  position: relative;
  font-size: 14px;
}

.el-form .el-form-item__content {
  line-height: 20px;
}

.el-form .el-form-item__label {
  line-height: 20px;
}

.el-form .el-form-item {
  text-align: left;
}
</style>
<script>
export default {
  data() {
    return {
      MaxCloudUrl: "",
      formLabelWidth: "120px",
      form: {
        name: "",
        vlan_id: "",
        cidr: "",
        is_dhcp: false,
        dhcp: {
          start_ip: "",
          end_ip: "",
          gateway: "",
          netmask: ""
        }
      }
    };
  },

  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
  },

  methods: {
    onSubmit() {
      let self = this;
      axios
        .post(this.GLOBAL.MaxCloudUrl + "/network/add", this.form)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            self.$message({
              message: data.RespHead.Message,
              type: "success"
            });
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
  }
};
</script>