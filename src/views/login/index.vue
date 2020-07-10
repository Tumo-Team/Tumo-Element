<template>
  <div class="login-container">
    <div class="login-weaper animated bounceInDown">
      <div class="login-left">
        <div class="login-time">{{time}}</div>
        <img class="img" src="https://avuejs.com/images/logo.png" alt />
        <p class="title">Vue Element Admin</p>
      </div>
      <div class="login-border">
        <div class="login-main">
          <h4 class="login-title">Login</h4>
          <el-form
            class="login-form"
            status-icon
            :rules="loginRules"
            ref="loginForm"
            :model="loginForm"
            label-width="0"
          >
            <el-form-item prop="username">
              <el-input
                size="small"
                @keyup.enter.native="handleLogin"
                v-model="loginForm.username"
                auto-complete="off"
                placeholder="Username"
              >
                <i slot="prefix" class="icon-yonghu"></i>
              </el-input>
            </el-form-item>
            <el-form-item prop="password">
              <el-input
                size="small"
                @keyup.enter.native="handleLogin"
                type="password"
                v-model="loginForm.password"
                auto-complete="off"
                placeholder="Password"
              >
                <i class="el-icon-view el-input__icon" slot="suffix" @click="showPassword"></i>
                <i slot="prefix" class="icon-mima"></i>
              </el-input>
            </el-form-item>
            <el-form-item prop="code">
              <el-row :span="24">
                <el-col :span="16">
                  <el-input
                    size="small"
                    @keyup.enter.native="handleLogin"
                    :maxlength="code.len"
                    v-model="loginForm.code"
                    auto-complete="off"
                    :placeholder="$t('login.code')"
                  >
                    <i slot="prefix" class="icon-yanzhengma"></i>
                  </el-input>
                </el-col>
                <el-col :span="8">
                  <div class="login-code">
                    <span
                      class="login-code-img"
                      @click="refreshCode"
                      v-if="code.type == 'text'"
                    >{{code.value}}</span>
                    <img :src="code.src" class="login-code-img" @click="refreshCode" v-else />
                    <!-- <i class="icon-shuaxin login-code-icon" @click="refreshCode"></i> -->
                  </div>
                </el-col>
              </el-row>
            </el-form-item>

            <el-form-item>
              <el-button
                type="primary"
                size="small"
                @click.native.prevent="handleLogin"
                class="login-submit"
              >{{$t('login.submit')}}</el-button>
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";
import SocialSign from "./components/SocialSignin";

export default {
  name: "Login",
  components: { SocialSign },
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("Please enter the correct user name"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("The password can not be less than 6 digits"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "admin",
        password: "111111"
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", validator: validateUsername }
        ],
        password: [
          { required: true, trigger: "blur", validator: validatePassword }
        ]
      },
      passwordType: "password",
      capsTooltip: false,
      loading: false,
      showDialog: false,
      redirect: undefined,
      otherQuery: {}
    };
  },
  watch: {
    $route: {
      handler: function(route) {
        const query = route.query;
        if (query) {
          this.redirect = query.redirect;
          this.otherQuery = this.getOtherQuery(query);
        }
      },
      immediate: true
    }
  },
  created() {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {
    if (this.loginForm.username === "") {
      this.$refs.username.focus();
    } else if (this.loginForm.password === "") {
      this.$refs.password.focus();
    }
  },
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    checkCapslock(e) {
      const { key } = e;
      this.capsTooltip = key && key.length === 1 && key >= "A" && key <= "Z";
    },
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch("user/login", this.loginForm)
            .then(() => {
              this.$router.push({
                path: this.redirect || "/",
                query: this.otherQuery
              });
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    getOtherQuery(query) {
      return Object.keys(query).reduce((acc, cur) => {
        if (cur !== "redirect") {
          acc[cur] = query[cur];
        }
        return acc;
      }, {});
    }
    // afterQRScan() {
    //   if (e.key === 'x-admin-oauth-code') {
    //     const code = getQueryObject(e.newValue)
    //     const codeMap = {
    //       wechat: 'code',
    //       tencent: 'code'
    //     }
    //     const type = codeMap[this.auth_type]
    //     const codeName = code[type]
    //     if (codeName) {
    //       this.$store.dispatch('LoginByThirdparty', codeName).then(() => {
    //         this.$router.push({ path: this.redirect || '/' })
    //       })
    //     } else {
    //       alert('第三方登录失败')
    //     }
    //   }
    // }
  }
};
</script>

<style lang="scss">
@import "@/styles/login.scss";
</style>