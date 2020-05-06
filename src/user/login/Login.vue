<template xmlns:v-on="http://www.w3.org/1999/xhtml">
  <div>
    <div class="login-wrap" v-show="showLogin">
         <h3>普通登录</h3>
         <p v-show="showTishi">{{tishi}}</p>
        <Form ref="form"  :label-width="100">
          <input type="text" v-model="loginForm.userName" placeholder="用户名"/>
          <input type="password" v-model="loginForm.passWord" placeholder="密码"/>
          <button @click="login()">登录</button>
          <span v-on:click="ToCkeck()">验证码登录?</span><br>
          <span v-on:click="ToRegister()">没有账号？马上注册</span>
        </Form>
      </div>

    <div class="register-wrap" v-show="showRegister">
      <h3>注册</h3>
      <p v-show="showTishi">{{tishi}}</p>
      <Form ref="form"  :label-width="100">
        <input type="text" placeholder="请输入用户名" v-model="loginForm.userName">
        <input type="password" placeholder="请输入密码" v-model="loginForm.passWord">
        <input type="password" placeholder="请您再次输入密码" v-model="toPassWord" />
        <input type="text" placeholder="请输入手机号 选填" v-model="loginForm.phoneNumber">
        <button v-on:click="register()">注册</button>
        <span v-on:click="ToLogin()">已有账号？马上登录</span>
      </Form>
    </div>

    <div class="check-wrap" v-show="showCheck">
      <h3>验证码登录</h3>
      <p v-show="showTishi">{{tishi}}</p>
      <Form ref="form"  :label-width="100">
        <input type="text" v-model="loginForm.phoneNumber" placeholder="手机号"/>
        <input type="password" v-model="loginForm.code" :disabled='flag' placeholder="验证码"/>
        <button @click="phoneLogin()">登录</button>
        <button @click="sendCode(loginForm.phoneNumber)">{{content}} </button>
        <span v-on:click="ToRegister()">没有账号？马上注册</span>
      </Form>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        content: '发送验证码',  // 按钮里显示的内容
        totalTime: 60,      //记录具体倒计时时间
        canClick: true,
        showLogin: true,
        showRegister: false,
        showCheck: false,
        showTishi: false,
        flag: false,
        tishi: '',
        number:false,
        toPassWord:'',
        loginForm: {
          userName: '',
          passWord: '',
          phoneNumber:''
        }
      };
    },

    methods: {
      login () {
        let then = this;
        if (then.loginForm.userName.length == '' && then.loginForm.passWord == '') {
          alert('用户名或密码为空请重新输入')
        }
        this.$post(process.env.API_USER+'/user/login',this.loginForm)
          .then((response) => {
            if(response.ok){
                localStorage.setItem("token",response.data);
                alert('验证通过,登录成功')
              this.$router.push('/home')
            }else {
              alert(response.message)
            }
          })
      },
      phoneLogin(){
        let then = this;
        let reg = /^[1][3,4,5,7,8,9][0-9]{9}$/;
        if (!reg.test(this.loginForm.phoneNumber)) {
          alert('请输入正确的手机号')
          return;
        } else {
          this.$post(process.env.API_USER+'/user/phoneLogin',this.loginForm)
            .then((response) => {
              if(response.ok){
                localStorage.setItem("token",response.data);
                alert('验证通过,登录成功')
                this.$router.push('/home')
              }else {
                alert(response.message)
              }
            })
        }
      },
      register () {
        let then = this;
        if (then.loginForm.userName.length <6 || then.loginForm.passWord.length >12) {
          alert('用户名长度不符合规范,应为6位到12位')
          return;
        }
        if (then.loginForm.passWord.length <6 || then.loginForm.passWord.length >16) {
          alert('用户密码长度不符合规范,应为6位到16位')
          return;
        }
        if(then.loginForm.passWord!= this.toPassWord){
          alert('两次输入的密码不一致')
          return;
        }

        if(then.loginForm.phoneNumber!= ''){
          let reg = /^[1][3,4,5,7,8,9][0-9]{9}$/;
          if (!reg.test(then.loginForm.phoneNumber)) {
            alert('请输入正确的手机号')
            return;
          }
        }

        this.$post(process.env.API_USER+'/user/register',this.loginForm)
          .then((response) => {
            if(response.ok){
              alert('注册成功');
              this.$router.push('/user/login')
            }else {
              alert(response.message)
            }

          })

      },
      sendCode(code){
        let reg = /^[1][3,4,5,7,8,9][0-9]{9}$/;
        if (!reg.test(code)) {
          alert('请输入正确的手机号')
          return;
        } else {
          if (!this.canClick) return  //改动的是这两行代码
          this.canClick = false
          this.content = this.totalTime + 's后重新发送'
          let clock = window.setInterval(() => {
            this.totalTime--
            this.content = this.totalTime + 's后重新发送'
            if (this.totalTime < 0) {
              window.clearInterval(clock)
              this.content = '重新发送验证码'
              this.totalTime = 60
              this.canClick = true  //这里重新开启
            }
          },1000)
          this.$post(process.env.API_USER+'/user/sendCode',this.loginForm)
            .then((response) => {
              if(response.ok){
                this.flag = false;
                alert(response.message)
              }else {
                alert(response.message)
              }
            })
        }
      },
      ToLogin() {
        this.showLogin = true
        this.showCheck = false
        this.showRegister = false
      },
      ToRegister() {
        this.showRegister = true
        this.showLogin = false
        this.showCheck = false
      },
      ToCkeck() {
        this.showCheck = true
        this.showLogin = false
        this.showRegister = false
      }
    }

  };
</script>

<style>
  .login-wrap {
    text-align: center;
  }
  input {
    display: block;
    width: 250px;
    height: 40px;
    line-height: 40px;
    margin: 0 auto;
    margin-bottom: 10px;
    outline: none;
    border: 1px solid #888;
    padding: 10px;
    box-sizing: border-box;
  }
  p {
    color: red;
  }
  button {
    display: block;
    width: 250px;
    height: 40px;
    line-height: 40px;
    margin: 0 auto;
    border: none;
    background-color: #41b883;
    color: #fff;
    font-size: 16px;
    margin-bottom: 5px;
  }
  span {
    cursor: pointer;
  }
  span:hover {
    color: #41b883;
  }
</style>
