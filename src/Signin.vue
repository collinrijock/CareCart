<template>
     <div id="big">
          <div id="signin-component">
               <div id="login" v-if="isLogin==true">
                    <h1>Log in</h1>
                    <input v-model="lemail" id="lemail" type="email" placeholder="Email">
                    <input v-model="lpassword" id="lpassword" placeholder="Password" type="password">
                    <button @click="requestLogin" id="lsubmit">Submit</button>
                    <button @click="isLogin=false">Dont have an account?</button>
                    <span id="error"></span>
               </div>
               <div id="signup" v-else>
                    <h1>Sign up</h1>
                    <input v-model="email" id="email" type="email" placeholder="Email">
                    <input v-model="password" id="lpassword" placeholder="Password" type="password">
                    <input v-model="first" id="first" placeholder="First name">
                    <input v-model="last" id="last" placeholder="Last name">
                    <input v-model="address" id="address" placeholder="Address">
                    <button @click="requestSignup" id="submit">Submit</button>
                    <button @click="isLogin=true">Already have an account?</button>
                    <span id="error"></span>
               </div>
          </div>
          <div @click="$emit('close')" id="overlay"></div>
     </div>
</template>

<script>
import axios from "axios";
import jquery from 'jquery';
var $ = require( "jquery" );
export default {
     props: ['login'],
     data () {
          return {
               isLogin: this.login,
               lemail: "",
               lpassword: "",
               email: "",
               password: "",
               first:"",
               last:"",
               address: "",
          }
     },
     methods: {

          loginValid() {

               if (this.lemail == "" || (!$("#lemail").prop("validity").valid)) {

                    $("#error").text("Error: please enter a valid email.");
                    return false;
               } else if (this.lpassword.length < 6) {

                    $("#error").text("Error: passwords must be at least 10 characters long.");
                    return false;

               } else {
                    
                    return true;
               }
          },

          requestLogin() {

               if (this.loginValid()) {

                    $("#error").text("Please wait...");

                    var formData = {};
                    formData.email = this.lemail;
                    formData.password = this.lpassword

                    axios({
                         method: "post",
                         url: "https://shellhacks-2020.herokuapp.com/users/login",
                         data: formData,
                    })

                    .then((response) => {

                         let token = response.data.token;
                         localStorage.setItem('token',token)
                         this.$emit('signedIn');

                    })

                    .catch((error) => {
                         $("#error").text("Error: " + error.response.data.detail);
                         
                    });

               }
          },

          signupValid() {

               if (this.email == "" || (!$("#email").prop("validity").valid)) {

                    $("#error").text("Error: please enter a valid email.");
                    return false;
               } else if (this.password.length < 6) {

                    $("#error").text("Error: passwords must be at least 10 characters long.");
                    return false;

               } else {
                    
                    return true;
               }
          },

          requestSignup() {

               if (this.signupValid()) {

                    $("#error").text("Please wait...");

                    var formData = {};
                    formData.email = this.email;
                    formData.password = this.password;
                    formData.first = this.first;
                    formData.last = this.last;
                    formData.address = this.address;
                    formData.volunteer = true;

                    axios({
                         method: "post",
                         url: "https://shellhacks-2020.herokuapp.com/users/register",
                         data: formData,
                    })

                    .then((response) => {

                         console.log('ewwq');
                         let token = response.data.token;
                         localStorage.setItem('token',token);
                         this.$emit('signedIn');

                    })

                    .catch((error) => {
                         $("#error").text("Error: " + error.response.data.detail);
                         
                    });

               }
          },
     },
     mounted() {
  
     },
}
</script>

<style lang="scss" scoped>
@import "./helper.scss";
#big {
     width: 100vw;
     height: 100vh;
     position: relative;
}
     #signin-component {
          z-index: 20;
          position: fixed;
          height: 50vh;
          width: 50vw;
          background: white;
          top:50%;
          left: 50%;
          transform: translate(-50%,-50%);
          padding: 40px;
          border-radius: 30px;
          @extend .flex-column;

          #login {
               @extend .flex-column;
          }

          #signup {
               @extend #login;
          }
          
     }
     #overlay {
          position: fixed;
          top: 0;
          left: 0;
          z-index: 15;
          width: 100vw;
          height: 100vh;
          background: rgba(44, 41, 54, 0.288);
          backdrop-filter: blur(25px);
     }
     
</style>