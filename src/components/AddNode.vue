<template>
  <el-form ref="add_node" :model="add_node" :rules="rules" label-width="80px" class="add_node">
    <el-form-item label="主机名称" prop="agent">
      <el-input v-model="add_node.agent" />
    </el-form-item>
    <el-form-item label="主机地址" prop="host">
      <el-input v-model="add_node.host" />
    </el-form-item>
    <el-form-item label="管理地址" prop="manage_ip">
      <el-input v-model="add_node.manage_ip" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="node_register('add_node')">添加</el-button>
      <el-button @click="resetForm">重置</el-button>
    </el-form-item>
  </el-form>
</template>
<style>
.add_node {
  text-align: center;
  width: 40%;
  color: #99a9bf;
  position: absolute;
  left: 30%;
}
</style>
<script>
  export default {
    data() {
      return {
        add_node: {
          host: '',
          agent: '',
          manage_ip: ''
        },
        rules: {
          host: [
            {required: true, message: '请输入host', trigger: 'blur'},
          ],
          agent: [
            {required: true, message: '请输入agent', trigger: 'blur'}
          ],
          manage_ip: [
            {required: true, message: '请输入manage_ip', trigger: 'blur'}
          ]
        }
      }
    },
    methods: {
      node_register(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let data = this.add_node;
            let self = this;
            axios
              .post(self.GLOBAL.MaxCloudUrl + '/node/register', data)
              .then(function (res) {
                var data = res.data;
                if (data.RespHead.ErrorCode === 0 && data.RespHead.Message === 'SUCCESS') {
                  this.$message("add node success !");
                }
              })
              .catch(function (error) { // 请求失败处理
                alert(error)
              })
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm() {
        this.$refs.add_node.resetFields();
        this.$message("数据已重置");
      },
    }
  }
</script>
