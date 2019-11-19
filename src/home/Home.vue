<template>
    <div id="home">
      <title id="登录"></title>
      <h1>Welcome to Jason Home</h1>
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
      login1 () {
        let then = this;
        if (then.loginForm.userName.length <6 || then.loginForm.passWord.length >12) {
          alert('用户名长度不符合规范,应为6位到12位')
        }
        this.$post('api/user/login',this.loginForm)
          .then((response) => {
            if(response.ok){
                localStorage.setItem("token",response.data);
              this.$router.push('/')
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
