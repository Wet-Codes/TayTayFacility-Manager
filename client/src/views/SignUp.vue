<template>
  <div class="background">
    <div class="signUpFlex">
      <div>
        <h id="header1">Join us!</h>
        <p id="signUph2">Create an account</p>
        <v-form @submit.prevent="submitForm">
          <v-text-field
            class="textFields"
            variant="outlined"
            label="First Name"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="rules"
            v-model="this.user.fName"
          ></v-text-field>
          <v-text-field
            class="textFields"
            variant="outlined"
            label="Last Name"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="rules"
            v-model="this.user.lName"
          ></v-text-field>
          <v-text-field
            class="textFields"
            variant="outlined"
            label="Email Address"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="emailRules"
            v-model:="this.user.email"
          ></v-text-field>
          <v-text-field
            class="textFields"
            variant="outlined"
            label="Password"
            type="password"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="passwordRules"
            v-model="this.user.password"
          ></v-text-field>
          <v-text-field
            class="textFields"
            variant="outlined"
            type="password"
            label="Confirm Password"
            color="#B4DAFF"
            bg-color="#729DC8"
            :rules="confirmPasswordRules"
            v-model="this.user.confirmPassword"
          ></v-text-field>
          <v-btn
            type="submit"
            append-icon="mdi-account-plus"
            class="signUpBtn"
            size="x-large"
            rounded="l"
            color="#0B3667"
            width="200"
            >Sign Up</v-btn
          >
          <p class="signUpP">Already have an account? <a href="/">Login</a></p>
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
  </div>
</template>

<script>
import UserAPI from "../API/userAPI.js";
export default {
  name: "signUp",
  data() {
    return {
      user: {
        fName: "",
        lName: "",
        email: "",
        password: "",
        confirmPassword: "",
      },
      rules: [(v) => !!v || "This is required"],
      passwordRules: [
        (v) => !!v || "This is required",
        (v) => v.length >= 8 || "Password must be at least 8 characters",
      ],
      confirmPasswordRules: [
        (v) => !!v || "This is required",
        (v) => v === this.user.password || "Passwords do not match",
      ],
      emailRules: [
        (v) => !!v || "This is required",
        (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
      ],
      toggleAlert: false,
      message: "",
    };
  },
  methods: {
    async submitForm() {
      let user = {
        fName: this.user.fName,
        lName: this.user.lName,
        email: this.user.email,
        password: this.user.password,
        confirmPassword: this.user.confirmPassword,
      };

      try {
        let response = await UserAPI.signUp(user);
        this.$router.push({
          name: "login",
          query: { message: response.message},
        });
      } catch (err) {
        this.toggleAlert = true;
        this.message = err.response.data.message;
        this.$nextTick(() => {
          setTimeout(() => {
            this.toggleAlert = false;
          }, 5000);
        });
        this.user.password = "";
        this.user.email = "";
        this.user.confirmPassword = "";
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
  height: 120vh;
  width: 100vw;
}

a {
  margin-left: 5px;
  text-decoration: none;
  color: white;
}

.signUpFlex {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  background-color: #34495E;
  height: 110vh;
  width: 25vw;
  border-radius: 12px;
}

#signUph2 {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 10px;
  margin-bottom: 50px;
  font-size: 15px;
  color: white;
}

.signUpP {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 10px;
  font-size: 12px;
  color: white;
}

.signUpBtn {
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 30px;
}
.textFields {
  width: 20vw;
  margin-top: 20px;
}
#header1{
  display: flex;
  justify-content: center;
  margin: auto;
  margin-top: 30px;
  margin-bottom: -25px;
  font-size: 50px;
  color: white;
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
</style>