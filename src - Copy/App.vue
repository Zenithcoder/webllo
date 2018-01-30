<template>
  <v-app>
   <v-toolbar>
     <v-menu offset-y v-if='loggedIn'>
      <v-btn primary flat slot="activator"><v-icon left>developer_board</v-icon> Boards</v-btn>
      <v-list>
        <v-list-tile v-for="board in boards" :key="board.id">
          <v-list-tile-title>

          <router-link :to = "{name:'SingleBoard', params:{id:board.id}}" tag="span"></router-link>

          </v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-menu>
    <v-spacer></v-spacer>
    <v-toolbar-title><router-link to="/" tag="span">{{title}}</router-link></v-toolbar-title>
    <v-spacer></v-spacer>
    <div v-if='!loggedIn'>
      <router-link to="/register" tag="span"><v-btn flat>Register</v-btn></router-link>
      <router-link to="/login" tag="span"><v-btn flat>Login</v-btn></router-link>
    </div>

<v-menu offset-y >
      <v-btn primary flat slot="activator"><v-icon left>developer_board</v-icon> {{user.username}}</v-btn>
      <v-list>
        <v-list-tile >
        <router-link to="/logout" tag="span">Logout</router-link>
        </v-list-tile>
      </v-list>
    </v-menu>


  </v-toolbar>
    <main>
      <v-container fluid>
        <router-view></router-view>
      </v-container>
    </main>
   
  </v-app>
</template>

<script>
  export default {
    data () {
      return {
        boards: '',
        title: 'Webllo',
        loggedIn:false,
        user:""
      }
    },
    created () {

      this.redirectGuestToLogin();

       Event.$on('boardsLoaded',($boards)=>{
        this.boards = $boards;
      });

      Event.$on('login',()=>{
        this.loggedIn=true;
      });

       Event.$on('logout',()=>{
            this.loggedIn=false;
          });
        

        let token = localStorage.getItem('token');
        
          this.loggedIn=true;

       this.fetchUserData();
       this.fetchBoardsData();
    },
    methods:{
      fetchUserData(){
         axios.get(baseApiURL+"/users/"+token+"?api_token="+token)
        .then(response => {
          console.log(response);
          this.user = response.data.user;

        });
      },

      fetchBoardsData(){
        axios.get("http://localhost:8000/boards?api_token="+token)
      .then(response => {
      //  console.log(response);
        this.boards=response.data.boards;
       Event.$emit('boardsLoaded', this.boards);
      });
      },
      redirectGuestToLogin(){
        if (!token) {
        this.$router.push('/login');
      }
      }
    }
  }
</script>

<style lang="stylus">
  @import './stylus/main'

</style>
