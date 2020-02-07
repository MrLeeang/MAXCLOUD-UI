<template>
  <el-form ref="add_vm_form" :model="add_vm_form" label-width="100px" class="add_vm">
    <el-form-item label="虚拟机名称" :label-width="formLabelWidth" prop="name">
      <el-input v-model="add_vm_form.name"></el-input>
    </el-form-item>
    <el-form-item label="虚拟机描述" :label-width="formLabelWidth" prop="desc">
      <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 6}" v-model="add_vm_form.desc"></el-input>
    </el-form-item>
    <el-form-item
      label="VNC"
      :label-width="formLabelWidth"
      style="text-align: left"
      prop="vnc_enable"
    >
      <el-switch v-model="add_vm_form.vnc_enable"></el-switch>
    </el-form-item>
    <el-form-item label="VNC验证" :label-width="formLabelWidth" prop="vnc_pass">
      <el-input
        v-model="add_vm_form.vnc_pass"
        :disabled="!add_vm_form.vnc_enable"
        autocomplete="off"
      ></el-input>
    </el-form-item>
    <el-form-item label="cpu" :label-width="formLabelWidth" prop="cpu">
      <el-select v-model="add_vm_form.cpu" placeholder="请选择cpu数量">
        <el-option label="1" value="1"></el-option>
        <el-option label="2" value="2"></el-option>
        <el-option label="4" value="4"></el-option>
        <el-option label="8" value="8"></el-option>
        <el-option label="16" value="16"></el-option>
        <el-option label="32" value="32"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="内存" :label-width="formLabelWidth" prop="memory">
      <el-select v-model="add_vm_form.memory" placeholder="请选择内存大小">
        <el-option label="1G" value="1048576"></el-option>
        <el-option label="2G" value="2097152"></el-option>
        <el-option label="4G" value="4194304"></el-option>
        <el-option label="8G" value="8388608"></el-option>
        <el-option label="16G" value="16777216"></el-option>
        <el-option label="32G" value="33554432"></el-option>
      </el-select>
    </el-form-item>
    <div v-for="(net_card, index) in add_vm_form.net_cards" :key="'nc'+index">
      <el-form-item label="是否为DHCP" style="text-align: left" :label-width="formLabelWidth">
        <el-switch v-model="net_card.is_static"></el-switch>
      </el-form-item>
      <el-form-item label="ip地址" :label-width="formLabelWidth">
        <el-input v-model="net_card.ip_address" :disabled="net_card.is_static" autocomplete="off"></el-input>
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

    <div v-for="(disk, index) in add_vm_form.disks" :key="index">
      <el-form-item label="设备编号" :label-width="formLabelWidth" style="text-align: left">
        <el-input v-model="disk.dev"></el-input>
      </el-form-item>
      <el-form-item label="设备类型" :label-width="formLabelWidth" style="text-align: left">
        <el-select v-model="disk.device" placeholder="选择设备类型">
          <el-option label="光盘" value="cdrom"></el-option>
          <el-option label="磁盘" value="disk"></el-option>
        </el-select>
      </el-form-item>
      <div v-if="disk.device=='disk'">
        <el-form-item label="磁盘大小" :label-width="formLabelWidth" style="text-align: left">
          <el-input v-model="disk.size"></el-input>
        </el-form-item>
        <el-input type="hidden" v-model="disk.driver_type" value="qcow2"></el-input>
      </div>
      <div v-if="disk.device=='cdrom'">
        <el-form-item label="ISO镜像" :label-width="formLabelWidth">
          <el-select v-model="disk.source_file" placeholder="请选择镜像">
            <el-option
              v-for="iso in iso_list"
              :key="iso.name"
              :label="iso.name"
              :value="iso.source"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-input type="hidden" v-model="disk.driver_type" value="raw"></el-input>
      </div>
      <el-form-item :label-width="formLabelWidth" style="text-align: left">
        <el-button icon="el-icon-delete" @click.prevent="removeDisk(disk)"></el-button>
      </el-form-item>
    </div>
    <el-form-item>
      <el-button @click="addNetCard">新增网卡</el-button>
      <el-button @click="addDisk">新增磁盘</el-button>
      <el-button @click="resetForm">重置</el-button>
      <el-button type="primary" @click="addVM">创建</el-button>
    </el-form-item>
  </el-form>
</template>
<style>
.add_vm {
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
import uuidv1 from "uuid/v1";
export default {
  data() {
    return {
      formLabelWidth: "120px",
      iso_list: [],
      networks: [],
      add_vm_form: {
        name: "",
        iso: "",
        vnc_enable: 1,
        vnc_pass: "",
        cpu: "",
        memory: "",
        desc: "",
        net_cards: [
          {
            name: "网卡1",
            type: "network",
            index: 0,
            is_static: false,
            ip_address: "",
            mac_address: "",
            network: { name: "" }
          }
        ],
        disks: [
          {
            name: "disk1",
            type: "file",
            device: "disk",
            driver_name: "qemu",
            driver_type: "qcow2",
            source_file: uuidv1(),
            size: "40",
            dev: "hda"
          }
        ]
      }
    };
  },
  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/iso/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode === 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.iso_list = res.data.RespBody.Result;
          console.log(self.iso_list);
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
    addVM() {
      let data = this.add_vm_form;
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/create", data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode === 0 &&
            data.RespHead.Message === "SUCCESS"
          ) {
            this.$message("提交成功");
          }
        })
        .catch(function(error) {
          alert(error);
        });
    },
    addNetCard() {
      this.add_vm_form.net_cards.push({
        name: "",
        index: "",
        type: "network",
        mac_address: "",
        is_static: false,
        ip_address: "",
        network: {
          name: ""
        }
      });
    },
    addDisk() {
      this.add_vm_form.disks.push({
        name: "",
        type: "file",
        device: "disk",
        driver_name: "qemu",
        driver_type: "qcow2",
        source_file: uuidv1(),
        size: "",
        dev: ""
      });
    },
    removeDisk(item) {
      var index = this.add_vm_form.disks.indexOf(item);
      if (index !== -1) {
        this.add_vm_form.disks.splice(index, 1);
      }
    },
    removeNetCard(item) {
      var index = this.add_vm_form.net_cards.indexOf(item);
      if (index !== -1) {
        this.add_vm_form.net_cards.splice(index, 1);
      }
    },
    resetForm() {
      this.add_vm_form = {
        name: "",
        iso: "",
        vnc_enable: 1,
        vnc_pass: "",
        cpu: "",
        memory: "",
        desc: "",
        net_cards: [
          {
            name: "网卡1",
            type: "network",
            index: 0,
            is_static: false,
            ip_address: "",
            mac_address: "",
            network: { name: "" }
          }
        ],
        disks: [
          {
            name: "disk1",
            type: "file",
            device: "disk",
            driver_name: "qemu",
            driver_type: "qcow2",
            source_file: uuidv1(),
            size: "40",
            dev: "hda"
          }
        ]
      };
      this.$message("数据已重置");
    }
  }
};
</script>
