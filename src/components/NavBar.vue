<template>
  <div v-if="this.$route.path !== '/'" class="nav-container">
      <div class="wrapper">
          <!--
          <nav v-if="this.$route.path !== '/'">
          -->
          <nav v-if="loggedIn">
              <div class="logo">
                  Ainran
              </div>
              <p>Logged in as {{ this.currentUser }}</p>
              <ul>
                <li><router-link to="/about" exact>About</router-link></li>
                <li><router-link to="/marketplace" exact>Marketplace</router-link></li>
                <li><router-link to="/community" exact>Community</router-link></li>
                <li><router-link to="/profile" exact>Profile</router-link></li>
                <li><button @click="signOut">Sign out</button></li>
              </ul>
          </nav>

          <nav v-else>
              <div class="logo">
                  Ainran
              </div>
                <ul>
                    <li><router-link to="/" exact>Home</router-link></li>
                    <li><router-link to="/about" exact>About</router-link></li>
                    <li><router-link to="/register" exact>Register</router-link></li>
                    <li><router-link to="/login" exact> Log In</router-link></li>
                </ul>
          </nav>
      </div>
  </div>
</template>

<script>
import firebase from "firebase/app";
import "firebase/auth";

export default {
    name: "navi",
    mounted() {
        this.setupFirebase();
    },
    data() {
        return {
            loggedIn: false,
            currentUser: false
        }
    },
    methods: {
        setupFirebase() {
            firebase.auth().onAuthStateChanged(user => {
                if (user) {
                    // User is signed in.
                    this.loggedIn = true;
                    this.currentUser = firebase.auth().currentUser.email;
                } else {
                    // No user is signed in.
                    this.loggedIn = false;
                    this.currentUser = false;
                }
            });
        },
        signOut() {
            firebase
                .auth()
                .signOut()
                .then(() => {
                this.$router.replace({ name: "login" });
                });
        }
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.nav-container{

    height: 72px;
}
.wrapper{
    max-width: 1200px;
    margin: 0 auto
}
nav{
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 72px;
}
nav ul{
    display: flex;
}
nav .logo{
    font-family: "Poppins", sans-serif;
    font-size: 20px;
    text-decoration: none;;
    font-style: normal;
    font-weight: 800;
    font-size: 64px;
    color: #021718
}
nav li{
    list-style: none;
    margin-right: 50px;

}
nav li:nth-last-of-type(1){
    margin: 0;
}

nav li a{
    color: #021718;
    /*font-family: "Poppins", sans-serif;*/
    font-size: 20px;
    text-decoration: none;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}
button{
    border: none;
    background: none;
    color: #021718;
    /*font-family: "Poppins", sans-serif;*/
    font-size: 20px;
    text-decoration: none;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}


</style>