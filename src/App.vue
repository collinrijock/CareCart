<template>
  <div id="site">
    <nav>
      <img id="logo" src="./assets/logo.svg">
      <button v-show="token==''" @click="login=true">Log in</button>
      <button v-show="token==''" @click="signup=true">Sign up</button>
      <button v-show="token!=''" @click="signOut">Sign out</button>
    </nav>

    <section v-show="token==''">
      <h1>Safely delivering to those in need</h1>
      <p>We connect volunteers with people who cant go out to retrieve their groceries. We realize these times can prevent disenfranchised people from getting groceries, toiletries or other essentials.</p>
    </section>
    
    <section v-show="token==''">
      <h1>We keep our volunteers safe</h1>
      <p>Volunteers are needed now more than ever so we enable them to find more people to help. Earn community service hours and give back to your community.</p>
    </section>

    <div id="volunteer-signedin" v-show="token!=''">
      <h1>{{ header }}</h1>
      <label>0 deliveries completed</label>
      <div id="map"></div>

    </div>

    <img id="bg" src="./assets/phase2.svg">
    
    <div id="signin-wrapper" v-if="login || signup">
      <Signin v-on:signin="handleSignin" v-on:close="login=false,signup=false" :login="login"></Signin>
    </div>

  </div>
  
</template>

<script>
import Signin from './Signin.vue'
import leaflet from 'leaflet'
export default {
  data () {
    return {
      login: false,
      signup:false,
      token: "",
      header: "Welcome, volunteer",
    }
  },
  methods: {
    handleSignin(){
      this.token = localStorage.getItem('token');
      this.login = false;
      this.signup = false;
      this.mymap = null;
    },
    signOut(){
      this.token = "";
      localStorage.removeItem('token');
    },
    initializeMap() {
      this.mymap = L.map('map').setView([39.82, -98.58], 3);
      L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}', {
          attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          maxZoom: 16,
          id: 'mapbox/streets-v11',
          tileSize: 512,
          zoomOffset: -1,
          minZoom:1,
          accessToken: 'pk.eyJ1IjoiY29sbGlucmlqb2NrIiwiYSI6ImNrZXhid3ZyeTBmcnozMGpyNTI0ZG44Y2EifQ.YDJ8q0HT8qjypartMULhFA',
      }).addTo(this.mymap);
    },
  },
  mounted() {
    if(localStorage.getItem('token')) {
      this.token = localStorage.getItem('token');
    }
  },
  components: {
    Signin
  }
}
</script>

<style lang="scss">
@import "./helper.scss";
body{
  margin:0;
}


#site {
  @extend .clear;
  font-family:lato;
  background: $off;
  overflow: hidden;

  min-height: 100vh;
  width: 100vw;
  
  padding: 6vh 11vw;
  box-sizing: border-box;
  @media (max-width: 700px) {
    padding: 4vw 11vh;
  }

  nav {
    @extend .flex-row;
    justify-content: flex-end;
    #logo {
      height: 66px;
      margin-right: auto;
    }
    button {
      z-index: 1;
      background: transparent;
      font-family: Fredoka One;
      @extend .clear;
      color: $red1;
      font-size: 1.2rem;
      margin-left: 70px;
      transition: transform 0.1s;
      cursor: pointer;
      &:hover {
        transform: scale(1.3);
        transition: transform 0.1s;
      }
    }
  }
  
  section {
    z-index: 10;
    text-align: left;
    h1{
      color: $red1;
      font-size: 80px;
      font-weight: 900;
    }
    p{
      color: $red1;
      font-size: 1.6rem;
      font-weight: 500;
      width: 80%;
    }
  }

  #bg{
      position: absolute;
      top: 20%;
      left: 0;
      min-height: 40vh;
      width: 100vw;
      z-index: 0;
  }

  section + section {
    position: relative;
    margin-top: 60vh;
    height: 70vh;

    h1 {
      color: white;
      text-align: right;
    }
    p {
      color: white;
      text-align: right;
      margin-left: auto;
    }
  }
  #signin-wrapper {
    width: 100vw;
    height: 100vh;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 20;
  }
}

</style>