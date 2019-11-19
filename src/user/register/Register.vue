<template>
    <div id="app">
      <title id="注册"></title>
      <Form ref="form"  :label-width="100">
        <input type="text" v-model="loginForm.userName" placeholder="用户名"/>
        <input type="text" v-model="loginForm.passWord" placeholder="密码"/>
        <button @click="register()">注册</button>
      </Form>
    </div>
</template>

<script>
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
      register () {
        let then = this;
        if (then.loginForm.userName.length <6 || then.loginForm.passWord.length >12) {
          alert('用户名长度不符合规范,应为6位到12为')
        }
        if (then.loginForm.passWord.length <6 || then.loginForm.passWord.length >16) {
          alert('用户密码长度不符合规范,应为6位到16为')
        }
          this.$post('http://122.51.2.48:1001/user/register',this.loginForm)
            .then((response) => {
              if(response.ok){
                alert('注册成功');
                this.$router.push('/user/login')
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
