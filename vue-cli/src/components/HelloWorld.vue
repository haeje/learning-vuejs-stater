<template>
  <form action="" v-on:submit.prevent="submitEvent">
    <div>
      <label for="username">id : </label>
      <input type="text" name="" id="username" v-model="username">
    </div>
    
    <div>
      <label for="password">password : </label>
      <input type="text" name="" id="password" v-model="password">
    </div>
    <button type="submit">login</button>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  data: function(){
    return {
      username : '',
      password : '',
    }
  },
  methods:{
    submitEvent:function(){
      // event.preventDefault();
      const url = "https://jsonplaceholder.typicode.com/users";
      const data = {
        username : this.username,
        password : this.password
      }
      console.log(data);
      
      axios.post(url, data)
        .then(function(response){
          console.log(response);
        })
        .catch(function(err){
          console.log(err);
        })
    }
  }
}
</script>

<style>

</style>