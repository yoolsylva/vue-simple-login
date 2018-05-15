<template>
  <div class="login-wrapper border border-light">
    <form class="form-signin" @submit.prevent="register">
      <h2 class="form-signin-heading">Register</h2>
      <div class="alert alert-danger" v-if="error">{{error}}</div>
      <label for="inputEmail" class="sr-only">Email address</label>
      <input v-model="email" type="email" id="inputEmail" class="form-control" placeholder="Email address" required
             autofocus>
      <label for="inputPassword" class="sr-only">Password</label>
      <input v-model="password" type="password" id="inputPassword" class="form-control" placeholder="Password" maxlength="32" required>
      <label for="confirmPassword" class="sr-only">Confirm</label>
      <input v-model="confirmPassword" type="password" id="confirmPassword" class="form-control" placeholder="Confirm Password" maxlength="32" required>
      <button class="btn btn-lg btn-primary btn-block" type="submit">Register</button>
    </form>
  </div>
</template>

<script>
    export default{
        name: 'Register',
        data() {
            return{
                email: '',
                password: '',
                confirmPassword: '',
                error: false
            }
        },
        created(){
            console.log('register created')
            this.checkCurrentLogin()
        },
        updated(){
            console.log('register updated')
            this.checkCurrentLogin()
        },
        methods:{
            checkCurrentLogin(){
                if(localStorage.token){
                    this.$router.replace(this.$route.query.redirect || '/home')
                }
            },
            register(){
                if(!this.isValidPass()){
                    return
                }
                this.$http.post('/users', {email: this.email, password: this.password, confirmPassword: this.confirmPassword})
                .then(request => this.registerSuccessfull(request))
                .catch(() => this.registerFailed())
            },
            registerSuccessfull(req){
              if(!req.data.token){
                this.registerFailed()
                return
              }

              localStorage.token = req.data.token;
              this.error = false
              this.$router.replace(this.$route.query.redirect || '/home')
            },
            registerFailed(){
              this.error = 'Register failed!'
              delete localStorage.token
            },
            isValidPass(){
                if(this.password !== this.confirmPassword){
                    this.error = 'Password not match!'
                    return false
                }
                const passRegex = new RegExp("^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#\$%\^&\*])(?=.{5,})");
                if(!passRegex.test(this.password)){
                    this.error = 'Password must contain at least 1 uppercase, 1 lowercase, 1 numeric, 1 special character and must be five characters or longer '
                    return false
                }
                return true
            }
        }
    }
</script>

<style lang="css">
  body {
    background: #605B56;
  }

  .login-wrapper {
    background: #fff;
    width: 70%;
    margin: 12% auto;
  }

  .form-signin {
    max-width: 330px;
    padding: 10% 15px;
    margin: 0 auto;
  }

  .form-signin .form-signin-heading,
  .form-signin .checkbox {
    margin-bottom: 10px;
  }

  .form-signin .checkbox {
    font-weight: normal;
  }

  .form-signin .form-control {
    position: relative;
    height: auto;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    padding: 10px;
    font-size: 16px;
  }

  .form-signin .form-control:focus {
    z-index: 2;
  }

  .form-signin input[type="email"] {
    margin-bottom: -1px;
    border-bottom-right-radius: 0;
    border-bottom-left-radius: 0;
  }

  .form-signin input[type="password"] {
    margin-bottom: 10px;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
  }
</style>
