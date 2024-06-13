<template>
  
  <div class="background" id="bp">
    <div class="websiteInfoBox">
      <h id="title">Taytay Facilities</h>
      <p id="flavorText">Browse affordable and luxurious facilities to your liking!</p>
    </div>
    <div class="loginFlex">
      <div>
        <h id="welcomeText">Welcome!</h>
        <p id="loginh2">login to your account</p>
        <v-form @submit.prevent="submitForm">
          <v-text-field
            class="textFields"
            v-model="user.email"
            variant="outlined"
            label="Email Address"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="rules"
          ></v-text-field>
          <v-text-field
            class="textFields"
            v-model="user.password"
            variant="outlined"
            type="password"
            label="Password"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="rules"
          ></v-text-field>
          <v-btn
            type="submit"
            append-icon="mdi-login"
            class="loginBtn"
            size="x-large"
            rounded="l"
            color="#0B3667"
            width="200"
            >Login</v-btn
          >
          <h class="loginP">
            Don't have an account? <v-btn :to="{name:'signUp'}">Sign up!</v-btn>
          </h>
        </v-form>
      </div>
    </div>
    <v-alert
      class="alert"
      border="left"
      closable
      close-text="Close Alert"
      color="red"
      dark
      dismissible
      v-if="this.toggleAlert"
    >
      {{ this.message }}
    </v-alert>
    <v-alert
      class="alert"
      border="left"
      closable
      close-text="Close Alert"
      color="green accent-4"
      dark
      dismissible
      v-if="this.$route.query.message"
    >
      {{ this.$route.query.message }}
    </v-alert>
  </div>
</template>

<script>
import UserAPI from "../API/userAPI.js";
export default {
  name: "login",
  data() {
    return {
      user: {
        email: "",
        password: "",
      },
      toggleAlert: false,
      message: "",
      rules: [
        (v) => !!v || "This is required",
      ]
    };
  },
  methods: {
    async submitForm() {
      let user = {
        email: this.user.email,
        password: this.user.password,
      };

      try {
        let response = await UserAPI.login(user);
        sessionStorage.setItem('user', JSON.stringify(response.user));
        this.message = "";
        this.toggleAlert = false;
        this.$router.push(`/${response.user.role}`, { params: { user: response.user } });
      } catch (err) {
        this.toggleAlert = true;
        this.message = err.response.data.message;
        this.$nextTick(() => {
            setTimeout(() => {
                this.toggleAlert = false;
            }, 5000)
        })
        this.user.email = "";
        this.user.password = "";
      }
    },
  },
};
</script>


<style>
.background {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #FBFCFC;
  height: 50vh;
  width: 100vw;
}

a {
  margin-left: 5px;
  text-decoration: none;
  color: #31ABFF;
}

.loginFlex {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  background-color: #34495E;
  height: 70vh;
  width: 25vw;
  border-radius: 0px;
  box-shadow: 10px 10px 15px #4E769E;
}
.websiteInfoBox{
  display: flex;
  justify-content: center;
  align-items: flex-start;
  height: 70vh;
  width: 30vw;
  background-color: #406890;
  box-shadow: 15px 10px 15px #4A79A8;
  border-radius: 0px;
}
#title{
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 170px;
  font-size: 45px;
  color: white;
  position:absolute;
}
#flavorText{
  display: flex;
  justify-content: center;
  margin: auto;
  font-size: 12px;
  color: #ECF0F1;
  font-family: 'Courier New', Courier, monospace;
  text-align: center;
  padding:10%;
}

#loginh2 {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 0px;
  margin-bottom: 50px;
  font-size: 15px;
  color: white;
}
#welcomeText {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 40px;
  margin-bottom: 0px;
  font-size: 50px;
  color: white;
}

.loginP {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 15px;
  font-size: 15px;
  color: white;
}

.loginBtn {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 30px;
}
.textFields {
  width: 20vw;
  margin-top: 20px;
}


</style>

<style>
a:link{
  color: #31ABFF;
  background-color: transparent;
  text-decoration: none;
}
a:visited{
  color: #208AD2;
  background-color: transparent;
  text-decoration: none;
}
a:hover{
  color: #0098FF;
  background-color: transparent;
  text-decoration: none;
}
a:active{
  color: #0098FF;
  background-color: transparent;
  text-decoration: none;
}
</style>

<style scoped>
.alert {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
}

#bp {
  margin: 0;
  padding: 0;
  padding-top: 20%;
  padding-bottom: 20%;
  height: 100vh;
}
</style>

