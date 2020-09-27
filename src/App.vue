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

    <div id="volunteer-signedin" v-show="token!=''&&!resultDone">
      <h1>{{ header }}</h1>
      <label>0 deliveries completed</label>
      <div id="maps">
        <div id="column">
          <h1>Lets get started</h1>
          <h3>1. Search for people to help in your area.</h3>
          <h3>2. Match with an asignee.</h3>
          <h3>3. Pickup and carry out delivery!</h3>
          <button id="find" @click="findAsignee">Find an asignee</button>
          <button id="find" v-if="resultHere" @click="accept">Click to accept request</button>
          <button id="find" v-if="resultYes" @click="resultDone=true">Click once you have finished your delivery</button>
          
        </div>
        <div id="map"></div>
      </div>
    </div>

    <div id="done" v-show="resultDone==true">
        <h1>Thank you for completing your first delivery!</h1>
        <button>Volunteer again</button>
      </div>

    <img id="bg" src="./assets/phase2.svg">
    
    <div id="signin-wrapper" v-if="login || signup">
      <Signin v-on:signedIn="handleSignin" v-on:close="closeAll" :login="login"></Signin>
    </div>

  </div>
  
</template>

<script>
import Signin from './Signin.vue'
import leaflet from 'leaflet'
import jquery from 'jquery'
import axios from 'axios'
var $ = require( "jquery" );
export default {
  data () {
    return {
      login: false,
      signup:false,
      token: "",
      header: "Welcome, volunteer",
      map: null,
      results: [],
      resultMarker: null,
      resultHere:false,
      resultYes:false,
      resultDone:false,
    }
  },
  methods: {
    done(){

    },
    handleSignin(){
      this.token = localStorage.getItem('token');
      this.login = false;
      this.signup = false;
    },
    accept() {
      var formData = {
            "ticket": this.results[0].ticket_id
      }
      axios({
        url: "http://52.150.19.14:8080/users/accept_ticket",
        method: "GET",
        data: formData,
        headers: {
          'authorization': localStorage.getItem('token')
        }
      })
      this.resultYes=true;
    },
    async findAsignee() {
      $('#find').html('Looking...');
      //make fake ticket
      var formData = {
            "destinationAddress": "2031 NW 18th Ave, Miami, FL 33142",
            "orderNumber": "#312342132",
            "author": "16e9e08c-6a44-447f-bf22-7270af7a9ce6",
            "phone": "305-612-3212",
            "expireAt": "2021-08-24T14:15:22Z"
      }
      axios({
        url: "http://52.150.19.14:8080/users/create_ticket",
        method: "POST",
        data: formData,
        headers: {
          'authorization': localStorage.getItem('token')
        }
      })

      //get all tickets
      axios({
        url: "http://52.150.19.14:8080/users/all_tickets",
        method: "GET",
        data: formData,
        headers: {
        'authorization': localStorage.getItem('token')
        }
      })
      .then((response) => {
        $('#find').html('Found matches');
        this.results = response.data.tickets;
        this.makeResultMarkers()
      })


    },
    closeAll(){
      this.login = false;
      this.signup = false;
    },
    signOut(){
      this.token = "";
      localStorage.removeItem('token');
    },
    initializeMap() {
  
      mapboxgl.accessToken = 'pk.eyJ1IjoiY29sbGlucmlqb2NrIiwiYSI6ImNrZmtsdWVobTBjaHAyeHA3dzl3YmN3d20ifQ.8LfwYhV2w7d-Ln9XynIGnw';
      this.map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/mapbox/streets-v11', // stylesheet location
      center: [-80.16, 25.697], // starting position [lng, lat]
      zoom: 11 // starting zoom
      });
      var marker = new mapboxgl.Marker()
      .setLngLat([-80.16,25.697])
      .addTo(this.map);
    },
    makeResultMarkers(){
        this.resultMarker = new mapboxgl.Marker()
          .setLngLat([this.results[0].longitude,this.results[0].latitude])
          .addTo(this.map);
          this.map.panTo([this.results[0].longitude,this.results[0].latitude], {duration: 2000});
          this.resultHere=true;
    }
  },
  mounted() {
    this.initializeMap();
    $(window).trigger('resize');
    if(localStorage.getItem('token')) {
      this.token = localStorage.getItem('token');
    }
  },
  components: {
    Signin
  },
}
</script>

<style lang="scss">
@import "./helper.scss";
body{
  margin:0;
}

button {
  @extent .clear;
  background: $red1;
  border-radius: 5px;
  font-family:lato;
  font-size: 1.4rem;
  width: 300px;
  height: 2rem;
  color: white;
  cursor: pointer;
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
#volunteer-signedin {
  color: $red1;
  #maps{
    @extend .flex-row;
  }
  #column {
    width: 40%;
    height: 500px;
    color: $red1;
    padding-right: 30px;
    z-index: 10;
  }
  #map {
    position:relative;
    height: 500px !important;
    width: 700px !important;
    z-index: 10;

    

  }
  
}
#done {
    height: 50vh;
    width: 70vw;
    display: grid;
    place-items: center;
    h1 {
      font-family: 'fredoka one';
      color: $red1;
      text-align:center;
      font-size: 4rem;
      z-index: 2000;
    }
  }

</style>