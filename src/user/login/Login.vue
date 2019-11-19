<template>
    <div id="app">
      <title id="登录"></title>
      <Form ref="form"  :label-width="100">
        <input type="text" v-model="loginForm.userName" placeholder="用户名"/>
        <input type="text" v-model="loginForm.passWord" placeholder="密码"/>
        <button @click="login1()">登录</button>
      </Form>
    </div>
</template>

<script>
  //import { login } from "@/api/user/user"
  export default {
    data () {
      return {
        loginForm: {
          userName: '',
          passWord: ''
        }
      };
    },

    methods: {
      login1 () {
        let then = this;
        if (then.loginForm.userName.length == '' && then.loginForm.passWord == '') {
          alert('用户名或密码为空请重新输入')
        }

  /*      login(this.loginForm).then(v => {
            alert(123)
        })*/
        this.$post('http://122.51.2.48:1001/user/login',this.loginForm)
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
    }

  };
</script>

<style type="text/css" type="text/stylus">
  #vue{
    color: green;
    font-size: 28px;
  }
</style>
