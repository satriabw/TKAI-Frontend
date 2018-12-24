<template>
  <div id="app">
    <div class="container">
        <div class="wrapper">
          <table class="table">
            <thead>
              <div>
                <th class="">#</th>
                <th class="">Name</th>
                <th class="">Address</th>
                <th class=""></th>
                <th class=""></th>
            </div>
           </thead>
            <div v-if="loading">
              <img src="../src/assets/Eclipse.gif"/>
            </div>
           <tbody>
            <div v-for="(address, index) in addresses" :key="address.user_id">
              <template v-if="address.edit == 'true'">
                <form @submit="updateAddress">
                  <input class="hidden" type="text" :value="updateForm.user_id = address.user_id" />
                  <th scope="row">{{ index+1 }}</th>
                  <td>{{ address.name }}</td>
                  <td><input class="form-control" type="text" v-model="updateForm.address" placeholder="Masukkan Alamat"/><td>
                  <td><input type="submit" class="btn btn-warning"></td>
                </form>
              </template>
              <template v-else>
                <form @submit="deleteAddress">
                  <th scope="row">{{ index+1 }}</th>
                  <td><div>{{ address.name }}</div></td>
                  <td><div>{{ address.address }}</div></td>
                  <input class="hidden" type="text" :value="deleteForm.user_id = address.user_id" />
                  <td><div><button type="button" class="btn btn-warning" @click="toggleUpdate(address.user_id)">Update</button></div></td>
                  <td><div><input type="submit" class="btn btn-danger" value="Delete"></div></td>
                </form>
              </template>
            </div>
           </tbody>
          </table>
        </div>
        <div class="tambah">
          <label>Tambah User</label>
          <form class="row" @submit="addUser">
          <div class="col-md-6">
            <input type="text" class="form-control" id="userNama" placeholder="Masukkan Nama" v-model="userForm.nama">
          </div>
          <div class="col-md-6"><input type="submit" class="btn btn-primary" value="Add Users"></div>
          </form>
        </div>
        <div class="tambah">
          <label>Tambah Alamat</label>
          <form class="row" @submit="addAddress">
          <div class="col-md-4">
            <select class="form-control" id="selectUser" v-model="addressForm.user_id">
              <option v-for="user in users" :key="user.user_id" v-bind:value="user.user_id" selected="selected">{{user.name}}</option>
            </select>
          </div>
          <div class="col-md-4">
            <input type="text" class="form-control" id="userNama" placeholder="Masukkan Alamat" v-model="addressForm.address">
          </div>
          <div class="col-md-4"><input type="submit" class="btn btn-primary" value="Add Address"></div>
          </form>
        </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import _ from 'lodash';  
axios.defaults.headers.common = {
  "Content-Type": "application/json"
}
export default {
  name: 'app',
  data () {
    return {
      addresses: [],
      users: [],
      loading: false,
      userForm: {nama: ''},
      addressForm: {user_id: null, address: ''},
      updateForm: {user_id: null, address: ''},
      deleteForm: {user_id: null}
    }
  },
  methods: {
    getData: function () {
      this.loading = true;
      let url_user = process.env.VUE_APP_HOST_USER + "user";
      let url_addr = process.env.VUE_APP_HOST_ADDR + "address";
      axios.all([
        axios.get(url_user),
        axios.get(url_addr)
      ]).then(axios.spread((res1, res2) => {
        this.users = res1.data;
        this.addresses = _.intersectionWith(_.cloneDeep(res1.data), res2.data, function(x, y) {
              return x.user_id === y.user_id && _.assign(x, y);
            })
        this.loading = false;
        }));
    },
    addUser: function () {
      let url_user = process.env.VUE_APP_HOST_USER + "user";
      axios.post(url_user, {
        "name": this.userForm.nama
      }).then(
        (response) => {
          console.log(response.data)
        }, (error) => {

        })
    },
    addAddress: function () {
      let url_addr = process.env.VUE_APP_HOST_ADDR + "address";
      axios.post(url_addr, {
        "user_id": this.addressForm.user_id,
        "address": this.addressForm.address
      }).then(
        (response) => {
          console.log(response.data)
        }, (error) => {

        })  
    },

    deleteAddress: function () {
      let url_addr = process.env.VUE_APP_HOST_ADDR + "delete-address";
      axios.post(url_addr, {
        "user_id": this.deleteForm.user_id
      }).then(
        (response) => {
          console.log(response)
        }, (error) => {

        })  
    },

    toggleUpdate: function (id) {
      var idx = this.addresses.findIndex(x => x.user_id === id);
      this.addresses[idx].edit = "true";
    },

    updateAddress: function () {
    let url_addr = process.env.VUE_APP_HOST_ADDR + "edit-address";
      axios.post(url_addr, {
        "user_id": this.updateForm.user_id,
        "address": this.updateForm.address
      }).then(
        (response) => {
          console.log(response)
        }, (error) => {

        })   
      },
  
    },
    mounted () {
    console.log(process.env)
    this.getData();
  },
    created() {

    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.tambah {
  margin: 2em;
}

.hidden {
  display: none;
}
</style>
