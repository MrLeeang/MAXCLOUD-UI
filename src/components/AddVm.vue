<template>
  <el-form ref="add_vm_form" :model="add_vm_form" label-width="100px" class="add_vm">
    <el-form-item label="虚拟机名称" :label-width="formLabelWidth" prop="name">
      <el-input v-model="add_vm_form.name"></el-input>
    </el-form-item>
    <el-form-item label="ISO镜像" :label-width="formLabelWidth" prop="iso">
      <el-select v-model="add_vm_form.iso" placeholder="请选择网络">
        <el-option v-for="iso in iso_list" :key="iso.name" :label="iso.name" :value="iso.name"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="VNC" :label-width="formLabelWidth" style="text-align: left" prop="vnc_enable">
      <el-switch v-model="add_vm_form.vnc_enable"></el-switch>
    </el-form-item>
    <el-form-item label="VNC验证" :label-width="formLabelWidth" prop="vnc_pass">
      <el-input v-model="add_vm_form.vnc_pass" :disabled="!add_vm_form.vnc_enable" autocomplete="off"></el-input>
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
    <el-form-item label="虚拟机描述" :label-width="formLabelWidth" prop="desc">
      <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 6}" v-model="add_vm_form.desc"></el-input>
    </el-form-item>
    <div v-for="(net_card, index) in add_vm_form.net_cards" :key="index">
      <el-form-item label="name">
        <el-input v-model="net_card.name"></el-input>
      </el-form-item>
      <el-form-item label="type" prop="type">
        <el-input v-model="net_card.type"></el-input>
      </el-form-item>
      <el-form-item label="index">
        <el-input v-model="net_card.index"></el-input>
      </el-form-item>
      <el-form-item label="mac_address">
        <el-input v-model="net_card.mac_address"></el-input>
      </el-form-item>
      <el-form-item label="is_static" style="text-align: left">
        <el-switch v-model="net_card.is_static"></el-switch>
      </el-form-item>
      <el-form-item label="ip_address">
        <el-input v-model="net_card.ip_address" :disabled="!net_card.is_static" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="network.name">
        <el-input v-model="net_card.network.name"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button icon="el-icon-delete" @click.prevent="removeNetCard(net_card)"></el-button>
      </el-form-item>
    </div>

    <div v-for="(disk, index) in add_vm_form.disks" :key="index">
      <el-form-item label="name">
        <el-input v-model="disk.name"></el-input>
      </el-form-item>
      <el-form-item label="type">
        <el-input v-model="disk.type"></el-input>
      </el-form-item>
      <el-form-item label="device">
        <el-input v-model="disk.device"></el-input>
      </el-form-item>
      <el-form-item label="driver_name">
        <el-input v-model="disk.driver_name"></el-input>
      </el-form-item>
      <el-form-item label="driver_type">
        <el-input v-model="disk.driver_type"></el-input>
      </el-form-item>
      <el-form-item label="source_file">
        <el-input v-model="disk.source_file"></el-input>
      </el-form-item>
      <el-form-item label="size">
        <el-input v-model="disk.size"></el-input>
      </el-form-item>
      <el-form-item label="dev">
        <el-input v-model="disk.dev"></el-input>
      </el-form-item>
      <el-form-item>
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
  export default {
    data() {
      return {
        formLabelWidth: "120px"
        iso_list: [],
        add_vm_form: {
          name: '',
          iso: '',
          vnc_enable: 1,
          vnc_pass: '',
          cpu: '',
          memory: '',
          desc: '',
          net_cards: [],
          disks: []
        }
      }
    },
    mounted() {
      let self = this;
      self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
      axios
        .get(self.GLOBAL.MaxCloudUrl + "/iso/list")
        .then(function (res) {
          if (res.data.RespHead.ErrorCode === 1004) {
            self.$router.push({path: "/login"});
          } else {
            self.iso_list = res.data.RespBody.Result;
          }
        })
        .catch(function (error) {
          // 请求失败处理
          console.log(error);
        });
    },
    methods: {
      addVM() {
        let data = this.add_vm_form;
        let self = this;
        axios
          .post(self.GLOBAL.MaxCloudUrl + '/vm/create', data)
          .then(function (res) {
            var data = res.data;
            if (data.RespHead.ErrorCode === 0 && data.RespHead.Message === 'SUCCESS') {
              this.$message("提交成功");
            }
          })
          .catch(function (error) {
            alert(error)
          })
      },
      addNetCard() {
        this.add_vm_form.net_cards.push({
          name: '',
          index: '',
          type: '',
          mac_address: '',
          is_static: 0,
          ip_address: '',
          network: {
            name: ''
          },
        })
      },
      addDisk() {
        this.add_vm_form.disks.push({
          name: '',
          type: '',
          device: '',
          driver_name: '',
          driver_type: '',
          source_file: '',
          size: '',
          dev: '',
        })
      },
      removeDisk(item) {
        var index = this.add_vm_form.disks.indexOf(item);
        if (index !== -1) {
          this.add_vm_form.disks.splice(index, 1)
        }
      },
      removeNetCard(item) {
        var index = this.add_vm_form.net_cards.indexOf(item);
        if (index !== -1) {
          this.add_vm_form.net_cards.splice(index, 1)
        }
      },
      resetForm() {
        this.$refs.add_vm_form.resetFields();
        this.add_vm_form.net_cards.splice(0);
        this.add_vm_form.disks.splice(0);
        this.$message("数据已重置");
      },
    }
  }
</script>
