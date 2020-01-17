<template>
  <div>
    <el-row :gutter="20">
      <el-col :span="8" :offset="8">
        <el-form ref="form" :model="form" label-width="120px">
          <el-form-item label="模板名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="描述" label-width="120px">
            <el-input type="textarea" v-model="form.desc"></el-input>
          </el-form-item>
          <el-form-item label="虚拟化类型" label-width="120px">
            <el-select v-model="form.vir_type" placeholder="虚拟化类型">
              <el-option label="KVM" value="KVM"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="文件格式" label-width="120px">
            <el-select v-model="form.type" placeholder="文件格式">
              <el-option label="qcow2" value="qcow2"></el-option>
              <el-option label="raw" value="raw"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="系统类型">
            <el-input v-model="form.os"></el-input>
          </el-form-item>
          <el-form-item label="系统用户">
            <el-input v-model="form.login_user"></el-input>
          </el-form-item>
          <el-form-item label="用户密码">
            <el-input v-model="form.login_pass"></el-input>
          </el-form-item>
          <el-form-item label="节点名称" label-width="120px">
            <el-select v-model="form.node_uuid" placeholder="选择节点上传" @change="node_change">
              <el-option
                v-for="item in nodes"
                :key="item.agent"
                :label="item.agent"
                :value="item.uuid"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="存储名称" label-width="120px">
            <el-select v-model="form.storage_pool_uuid" placeholder="选择存储上传">
              <el-option
                v-for="item in storage_pool"
                :key="item.name"
                :label="item.name"
                :value="item.uuid"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="模板文件">
            <el-upload
              class="upload-demo"
              drag
              ref="upload"
              :action="up_url"
              :on-success="handleAvatarSuccess"
              :on-error="handleAvatarError"
              :before-upload="beforeAvatarUpload"
              :before-remove="before_remove"
              :limit="1"
              :on-exceed="handleExceed"
              :data="form"
              :auto-upload="false"
              :with-credentials="true"
            >
              <i class="el-icon-upload"></i>
              <div class="el-upload__text">
                将文件拖到此处，或
                <em>点击上传</em>
              </div>
              <div class="el-upload__tip" slot="tip">只能上传iso文件，且不超过5G</div>
            </el-upload>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit" :disabled="create">立即创建</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>
<style>
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
      nodes: [],
      storage_pool: [],
      up_url: "",
      iso_list: "",
      create: false,
      form: {
        name: "",
        vir_type: "",
        type: "",
        os: "",
        login_user: "",
        login_pass: "",
        node_uuid: "",
        storage_pool_uuid: "",
        desc: ""
      }
    };
  },

  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    self.up_url = self.MaxCloudUrl + "/vm_tpl/upload";
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/node/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.nodes = res.data.RespBody.Result;
        }
      })
      .catch(function(error) {
        // 请求失败处理
        console.log(error);
      });
  },

  methods: {
    onSubmit() {
      if (
        this.form.name &&
        this.form.vir_type &&
        this.form.type &&
        this.form.os &&
        this.form.login_user &&
        this.form.login_pass &&
        this.form.node_uuid &&
        this.form.storage_pool_uuid &&
        this.form.desc
      ) {
        this.$refs.upload.submit();
      } else {
        this.$message.error("以上全为必填项");
      }
    },

    handleAvatarSuccess(res, file) {
      let self = this;
      if (res.RespHead.ErrorCode == 0) {
        self.$message({
          message: res.RespHead.Message,
          type: "success"
        });
        self.create = true;
      } else {
        self.$message.error(res.RespHead.Message);
      }
    },

    handleAvatarError() {
      this.$message.error("镜像上传失败");
    },

    handleExceed() {
      this.$message.error("最多上传一个镜像");
    },

    before_remove() {
      this.form.file_name = "";
      this.form.file_path = "";
      this.create = true;
    },

    beforeAvatarUpload(file) {
      this.create = true;
      // const iso = file.type === "iso";
      const isLt3G = file.size / 1024 / 1024 / 1024 < 5;

      // if (!iso) {
      //   this.$message.error("上传文件只能是 iso 格式!");
      // }
      if (!isLt3G) {
        this.$message.error("上传文件大小不能超过 5G!");
      }
      return isLt3G;
    },

    node_change(uuid) {
      let self = this;
      $.each(self.nodes, function(index, node) {
        if (node && node["uuid"] == uuid) {
          self.up_url =
            "http://" +
            node["host"] +
            ":" +
            self.GLOBAL.MaxCloudPort +
            "/vm_tpl/upload";

          self.storage_pool = [];
          axios
            .post(self.GLOBAL.MaxCloudUrl + "/storage/query", {
              node_uuid: uuid
            })
            .then(function(res) {
              var data = res.data;
              if (
                data.RespHead.ErrorCode == 0 &&
                data.RespHead.Message == "SUCCESS"
              ) {
                $.each(data.RespBody.Result, function(key, value) {
                  self.storage_pool.push(value);
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
          return true;
        }
      });
    }
  }
};
</script>