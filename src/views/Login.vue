<template>
  <el-form id="login_form">
      <div>
        <el-input v-model="email" placeholder="请输入用户名"></el-input>
      </div>
      <br>
      <div>
        <el-input placeholder="请输入密码" v-model="password" show-password></el-input>
      </div>
      <br>
      <div>
        <el-button type="primary" @click="login">登录</el-button>
      </div>
    </el-form>
</template>

<script>
export default {
    name: "Login",
    data() {
      return {
        email: '',
        password: ''
      }
    },
    created() {
    document.title="login";
    },
    methods: {
      login() {
        if(this.email && this.password){
            let data = {"email": this.email, "password": this.password}
            let self = this
            axios
            .post(self.GLOBAL.MaxCloudUrl+'/auth/login', data)
            .then(function (res){
              var data = res.data;
              if (data.RespHead.ErrorCode==0 && data.RespHead.Message=="SUCCESS"){
                self.$router.push({path: "/"})
              }
            })
            .catch(function (error) { // 请求失败处理
              alert(error);
            });
          } else {
            alert("please input user and password");
          }
        }
    }
}
</script>

<style>
#login_form {
  position: absolute;
  margin-top: 120px;
  width: 400px;
  left: 50%;
  margin-left: -250px;
  border: 1px solid #dedede;
  padding: 50px;
  background-color: #f7f7f7;
}
</style>