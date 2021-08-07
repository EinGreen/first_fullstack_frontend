<template>
  <div id="app">
    <h1>Welcome to CandyLand!</h1>

    <section>
      <h2>Post Candy:</h2>
      <form id="candy_form" action="javascript:void(0)">
        <input id="name_input" type="text" placeholder="name">
        <input id="description_input" type="text" placeholder="description">
        <input id="price_input" type="text" placeholder="price">
        <input id="url_input" type="text" placeholder="image URL">
        <input @click="postCandy" type="submit" value="Submit">
      </form>
      <h3>{{ post_status }}</h3>
    </section>
    
    <section>
      <h2>Patch Candy:</h2>
      <form id="patch_candy_form" action="javascript:void(0)">
        <input type="text" placeholder="candy Id" id="id_input_update" />
        <input type="text" placeholder="new name" id="name_upate" />
        <input type="text" placeholder="new description" id="description_update" />
        <input type="text" placeholder="new price" id="price_update" />
        <input type="text" placeholder="new img url" id="url_update" />
        <input @click="patchCandy" type="submit" placeholder="Update" />
      </form>
      <h3>{{ patch_status }}</h3>
    </section>

    <section>
      <h2>Delete Candy:</h2>
      <form id="deletecandy_form" action="javascript:void(0)">
        <input type="text" placeholder="id of Candy" id="deletion_id" />
        <input @click="deleteCandy" type="submit" placeholder="Delete" />
      </form>
      <h3>{{ delete_status }}</h3>
    </section>

    <h1>{{ loading_status }}</h1>
    <div v-for="candy in candies" v-bind:key="candy.id">
      <h3>{{ candy[0] }}</h3>
      <p>ID: {{ candy[4] }}</p>
      <img :src="candy[3]" alt="Candy image" />
      <p>{{ candy[1] }}</p>
      <p>{{ candy[2] }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      candies: [],
      post_status: "",
      loading_status: "",
      patch_status: "",
      delete_status: ""
    }
  },
  methods: {
    postCandy(){
      axios.request({
        url: `${process.env.VUE_APP_API_URL}/candy`,
        method: "POST",
        data:{
          name: document.getElementById("name_input").value,
          desc: document.getElementById("description_input").value,
          price: document.getElementById("price_input").value,
          img: document.getElementById("url_input").value,
        } 
      }).then((res) => {
        this.candies.push(res.data);
        this.getCandies();
        document.getElementById("candy_form").reset();
        this.post_status = "Candy Posted!"
      }).catch((err) => {
        console.log(err)
        this.post_status = "Error, Can't Post Candy"
      });
    },
    patchCandy(){
      axios.request({
        url: `${process.env.VUE_APP_API_URL}/candy`,
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        data: {
          candyId: document.getElementById("id_input_update").value,
          name: document.getElementById("name_upate").value,
          desc: document.getElementById("description_update").value,
          price: document.getElementById("price_update").value,
          img: document.getElementById("url_update").value,
        },
      }).then((res) => {
        this.candies.push(res.data);
        this.getCandies();
        document.getElementById("patch_candy_form").reset();
        console.log(res.data);
        this.patch_status = "Candy Updated!"
      }).catch((err) => {
        console.log(err);
        this.patch_status = "Error, Can't Update Candy"
      });
    },
    deleteCandy() {
    axios.request({
        url: `${process.env.VUE_APP_API_URL}/candy`,
        method: "DELETE",
        headers: { "Content-Type": "application/json" },
        data: {
          candyId: document.getElementById("deleteId").value,
        },
      }).then((res) => {
        res;
        for (let i=0; i<this.candies.length; i++) {
          if (this.candies[i][4] === Number(document.getElementById("deletion_id").value)){
            this.candies.splice(i, 1);
          }
        }
        document.getElementById("deleteForm").reset();
        this.getCandies();
        this.delete_status = "Candy Removed"
      }).catch((err) => {
        console.log(err);
        this.delete_status = "Error, Can't Remove Candy"
      });
    },
  },
  mounted() {
    this.loading_status = "Loading Candy";
    axios.request({
        url: `${process.env.VUE_APP_API_URL}/candy`,
        method: "GET",
      }).then((res) => {
        console.log(res);
        this.candies = res.data
        this.loading_status = ""
      }).catch((err) => {
        console.log(err);
        this.loading_status = "Failed to Load Candy"
      });
  },
}
</script>

<style>
img {
  width: 200px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
