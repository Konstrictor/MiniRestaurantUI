<template>
  <div id="app">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <div v-if="user != null">
      <TopBar :user="user"/>
      <router-view/>
      <button @click="logout" class="logout">
        logout
      </button>
    </div>
    <div v-else>
      <AppLogin @connected="logIn"/>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import AppLogin from "./components/login";
import TopBar from "./components/topbar";

export default {
  name: 'app',
  components:{ AppLogin, TopBar},
  data () {
    return {
      user:this.$store.state.user,
    }
  },
  computed:{
    host(){
      return this.$store.state.host
    }
  },
  created(){
    var state = JSON.parse(localStorage.getItem('state'));
    if (state) {
      this.$store.state.user = state.user;
      axios.post(this.host+'/refresh/', {
          "refresh": state.user.refresh
        })
        .then((response) => {
          this.$store.state.user.access = response.data.access;
        }).catch((error) => {
          return;
        });
      this.$store.state.serveurs = state.serveurs; 
      this.$store.state.recettes = state.recettes; 
      this.$store.state.tables = state.tables;
      this.user = state.user;
    } else {
      console.warn("il y'a pas de session");
    }
  },
  watch:{
    "$store.state":{
      deep: true,
      handler(new_state){
        localStorage.setItem('state', JSON.stringify(new_state));  
      }
    },
  },
  methods:{
    logIn(user){
      if (user != null && user.access.length>20) {
        this.user = user;
        this.fetchData();
      }
    },
    logout(){
      localStorage.setItem('state', null);
      this.$store.state.user = null;
      this.$store.state.serveurs = null;
      this.$store.state.recettes = null;
      this.$store.state.tables = null;
      this.user = null;
    },
    fetchData(){
      let headers = {
        headers: {
          "Authorization": "Bearer " + this.user.access
        }
      }
      axios.get(this.host+'/serveur/', headers)
        .then((response) => {
          this.$store.state.serveurs = response.data;
        }).catch((error) => {
          console.error(error);
        });

      axios.get(this.host+'/table/', headers)
        .then((response) => {
          this.$store.state.tables = response.data;
        }).catch((error) => {
          console.error(error);
        });

      axios.get(this.host+'/recette/', headers)
        .then((response) => {
          this.$store.state.recettes = response.data;
        }).catch((error) => {
          console.error(error);
        });
    }

  }
};
</script>
<style src="./style.css">
</style>
