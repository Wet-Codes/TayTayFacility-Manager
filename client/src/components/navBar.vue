<template>
  <v-app-bar scroll-behavior="collapse" scroll-threshold="119" color="#406890" :elevation="2" height="100"   :class="{ 'app-bar-with-results': !state.noSearch && state.searchResults.length > 0 }"
>
<v-btn variant="plain" :to="{name: `${userRole}Home`}" :ripple="false" class="image-button">
  <v-img
    src="/imgs/logo-no-background.png"
    alt="taytay Icon"
    height="90px"
    width="100px"
  ></v-img>
</v-btn>
    <div v-if="this.userRole !== 'admin'" class="searchBar" :class="{'searchBar-results' : !state.noSearch && state.searchResults.length > 0}">
      <v-text-field
      @keyup="search"
      v-model="searchBar"
      height="60"
        variant="solo"
        class="no-underline"
        rounded
        placeholder="What facilities are you looking for?"
        prepend-inner-icon="mdi-magnify"
        hide-details
        color="primary"
        bg-color="white"
      ></v-text-field>
      <template v-if="!state.noSearch && state.searchResults.length > 0">
      <div class="search-item" v-for="(facility, index) in state.searchResults" :key="index" >
      <hr v-if="index > 0" >
      <v-btn @click="searched" :to="{name: `${userRole}Facility`, params: {id: facility._id}}" :ripple="false" variant="plain" >{{ facility.title }}</v-btn>
      </div>
      </template>
      <template v-else-if="!state.noSearch" >
        <div class="search-item">
          <p>There are no results. Sorry!</p>
        </div>
      </template>
    </div>
    <v-btn :to="{name: 'announcement'}" :ripple="false" variant="plain" color="white" >Announcement</v-btn>
    <v-btn v-if="this.userRole !== 'admin'" :to="{name: 'report'}" :ripple="false" variant="plain" color="white" >Report</v-btn>
    <v-btn :to="{name: 'inbox'}" :ripple="false" variant="plain" color="white">
      <v-badge :content="content" >

        <v-icon  size="35">mdi-email</v-icon>
      </v-badge>
</v-btn>
    <v-menu > 
      
      <template v-slot:activator="{ props }" >
      <v-btn v-bind="props" :ripple="false" class="userDiv">
        <v-img cover :src="userImage" class="profile-image" ></v-img>
        <p class="userName ml-3" >{{ userName }}</p>

      </v-btn>
      </template>
      
      <v-card min-width="300" >
      <v-list>
      <v-list-item
      :prepend-avatar="userImage"
      :title="userName + ' ' + userLName"
      :subtitle="userRole.charAt(0).toUpperCase() + userRole.slice(1)">
      
      </v-list-item></v-list>
      <v-divider></v-divider>
      <v-list>
        <v-list-item>
          <v-btn :to="{name: 'manageAccount'}" prepend-icon="mdi-account-convert" height="50" width="200" color="#406890">Manage Account</v-btn>
        </v-list-item>
        <v-list-item>
          
          <v-dialog
      v-model="dialog"
      max-width="600"
      persistent
    >

    <v-spacer></v-spacer>
      <template v-slot:activator="{ props: activatorProps }">
        <v-btn v-bind="activatorProps"  height="50" width="200" color="#406890">
          Logout
        </v-btn>
      </template>
      
      <v-container class="d-flex justify-center align-center">
      <v-card 
        width="420"
        
        prepend-icon="mdi-account"
        title="Are You Sure You Want To Logout?"
      >
        <template v-slot:actions>
       
          <v-btn @click="logout" prepend-icon="mdi-logout" height="50" width="200" color="#4A9A64">Yes</v-btn>
          <v-btn @click="dialog = false" height="50" width="200" color="#970220">No</v-btn>
        </template>
      </v-card>
      </v-container>
    
    </v-dialog>

          
        </v-list-item>
      </v-list>
      </v-card>
    </v-menu>
  </v-app-bar>
</template>

<script>
import OwnerAPI from '../API/ownerAPI';
import TenantAPI from '../API/tenantAPI';
import AdminAPI from '../API/adminAPI';
import {reactive} from 'vue'
export default {
  
  name: "navBar",
  data(){
    return{
      searchBar: '',
      dialog: false,
      content: 0
    }
  },
  async created(){
    if(this.userRole === 'admin'){
      const res = await AdminAPI.getReports();
      this.content = res.length;
    }
    else if(this.userRole === 'owner'){
      const res = await OwnerAPI.fetchRequests();
      this.content = res.length;
    }
    else{
      const res = await TenantAPI.fetchRequests(this.userId);
      this.content = res.length;
    }
  },
  methods: {
    searched(){
      this.state.noSearch = true;
      this.state.searchResults = [];
      this.searchBar = '';

    },
    logout(){
      
      sessionStorage.removeItem('user');
      this.$router.push({name: 'login'})
    }
  },
  props: ["userName", "userImage", "userRole", "userLName", "userId"],
  setup(props){

    const state = reactive({
      searchResults: [],
      noSearch: true
    })

    async function search(e){
      let search = e.target.value
      if(props.userRole === 'owner'){
        const res = await OwnerAPI.search(search)
        if(search === ''){
          state.noSearch = true;
          return;
        }
        state.noSearch = false
        state.searchResults = res;
      }
      else{
        const res = await TenantAPI.search(search)
        if(search === ''){
          state.noSearch = true;
          return;
        }
        state.noSearch = false
        state.searchResults = res;
      }
    }

    return {search, state}
  }
};
</script>


<style scoped>

.searchBar {
  width: 50vw;
}

.profile-image{
    width: 70px;
    height: 70px;
    border-radius: 50%;

}

.image-button {
  height: auto;
  width: 7rem;
  padding: 0;
  border: none;
  outline: none;
}

.userDiv{
    display: flex;
    flex-direction: row;
    align-items: center;
    margin-left: 20px;
    margin-right: 60px;
    justify-content: center;
    gap: 10px;
    height: 100px
}

.userName{
    font-size: 17px;
    font-weight: bold;
    color: white;
}

.app-bar-with-results {
  height: 220px !important; /* adjust as needed */
}

.searchBar-results{
  margin-top: 120px !important; /* adjust as needed */
}
</style>