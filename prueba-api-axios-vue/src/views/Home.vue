<template>
  <div>
    <form @submit.prevent="addUser" class="mb-3">
      <div v-if="error" class="alert alert-dismissible alert-warning">
        <button type="button" class="close" data-dismiss="alert">&times;</button>
        <h4 class="alert-heading">Error!</h4>
        <p class="mb-0">{{error}}</p>
      </div>
      <div class="form-group">
        <label for="name">Name</label>
        <input
          v-model="user.name"
          type="text"
          class="form-control"
          id="name" 
          placeholder="Enter a Name" required>
      </div>
      <div class="form-group">
        <label for="email">Email</label>
        <input
          v-model="user.email"
          type="text"
          class="form-control"
          id="email"
          placeholder="Enter a Email" required>
      </div>
      <div v-if="user.id == ''">
        <button type="submit" class="btn btn-primary">Add User</button>
      </div>
      <div v-if="user.id != ''">
         <button type="button" class="btn btn-primary" v-on:click = "updateUser()">Update User</button>
      </div>
    </form>
    <hr>
    <div>
      <table style="width:100%">
        <tr>
          <th>Id</th>
          <th>Name</th>
          <th>Email</th>    
          <th>Actions</th>    
        </tr>
        <tr v-for="(user, index) in users" :key="user.id">
                <td>{{user.id}}</td>
                <td>{{user.name}}</td>
                <td>{{user.email}}</td>
                <td>
                  <button type="button" class="btn btn-primary" v-on:click = "preupdateUser(index)">Edit User</button>
                  <button type="button" class="btn btn-primary" v-on:click = "deleteUser(index, user.id)">Delete User</button>
                </td>
        </tr>
      </table>
    </div>
    
  </div>
</template>
 
<script>
const API_URL = "http://localhost:3000/users";
import axios from 'axios'; 
export default {
  name: "home",
  data: () => ({
    error: "",
    users: [],
    count: 1,
    user: {
      id: "",
      name: "",
      email: ""
    }
  }),
  computed: {
    reversedUsers() {
      return this.users.slice().reverse();
    }
  },
  created() {
    var self = this;   
    axios.get(API_URL)
      .then( function(res) {
        self.users = res.data;
      })
      .catch( function(error){
        console.log(error);
      })
  },

 
  methods: {
    addUser() {
      axios.post(API_URL, this.user)
        .then(response => {
            this.users.push(this.user);
            console.log(this.user);
            })
        .catch(err => {
            console.log(err);
            });  
        // this.user.name="";
        // this.user.email="";
    },

    updateUser(){
      let uri = `${API_URL}/${this.user.id}`;
      axios.put(uri, this.user)
      .then((resp) => {
        // console.log(resp.config.data)
        // this.user = {id: "", name: "", email: ""};
      }).catch(e => {
        console.log(e);
      })
      this.user = {id: "", name: "", email: ""};
    },

    preupdateUser(id){
      this.user = this.users[id];
    },

    deleteUser(index, id){
      let uri = `${API_URL}/${id}`;
      axios.delete(uri, {params : {id: `${id}`}})
        .then(response => {
          this.users.splice(index,1)
      })
    }
  }
};
</script>
 
<style>
img {
  max-width: 300px;
  height: auto;
}
</style>