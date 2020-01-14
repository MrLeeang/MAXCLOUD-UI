<template>
  <div class="container">
    <div class="row">
      <div class="col-md-4 col-md-offset-4" style="margin-top: 60px;">
        <form class="form-signin">
          <h2 class="form-signin-heading" style="text-align: left">Cloudcc Login</h2>
          <input
            name="email"
            type="text"
            id="inputEmail"
            class="form-control"
            placeholder="Email"
            required
            autofocus
          />
          <br />
          <input
            name="password"
            type="password"
            id="inputPassword"
            class="form-control"
            placeholder="Password"
            required
          />
          <div class="checkbox" style="text-align: left">
            <label>
              <input type="checkbox" value="remember-me" /> Remember me
            </label>
          </div>
          <button class="btn btn-lg btn-primary btn-block" type="button" @click="login">Sign in</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      email: "",
      password: ""
    };
  },
  created() {
    document.title = "login";
  },
  methods: {
    login() {
      let email = $("#inputEmail").val();
      let password = $("#inputPassword").val();
      if (email && password) {
        let data = { email: email, password: password };
        let self = this;
        axios
          .post(self.GLOBAL.MaxCloudUrl + "/auth/login", data)
          .then(function(res) {
            var data = res.data;
            if (
              data.RespHead.ErrorCode == 0 &&
              data.RespHead.Message == "SUCCESS"
            ) {
              self.$router.push({ path: "/" });
            }else{
              self.$message.error("用户名或密码错误")
            }
          })
          .catch(function(error) {
            // 请求失败处理
            console.log(error);
            self.$message.error("系统错误")
          });
      } else {
        self.$message.error("please input user and password");
      }
    }
  }
};
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