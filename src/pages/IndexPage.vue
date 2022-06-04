<template>
  <q-page padding>
    <div class="row">
      <div class="col-6 col-md-5 col-lg-4">
        <h5>Sign Up</h5>
        <q-form action="" method="post" @submit.prevent="submitRegForm">
          <q-input name="username" v-model="username" label="Username" outlined required />
          <q-input type="password" name="password" v-model="password" label="Password" outlined required />
          <q-btn label="Register" type="submit" color="primary"/>
        </q-form>
        <!-- <div v-if="loading" class="loading">Loading...</div> -->
        <div v-if="users.length > 0">
          <!-- <h6>Registered users</h6>
          <q-list dense>
            <q-item v-for="user in users.slice().reverse()" :key="user.username" dense>
              {{ user.id }}. {{ user.username }}
            </q-item>
          </q-list> -->
          <div class="q-pa-md">
            <q-table
              title="Registered users"
              :rows="users"
              :columns="columns"
              row-key="name"
            />
          </div>
        </div>
      </div>
      <div class="col-6 col-md-1 col-lg-1"></div>
      <div class="col-6 col-md-5 col-lg-4">
        <h5><span v-if="loggedIn === false">Log In</span><span v-if="loggedIn">Hello, {{ loggedUser.username }}</span></h5>
        <q-form action="" method="post" @submit.prevent="submitLoginForm" v-if="!loggedIn">
          <q-input name="username2" v-model="username2" label="Username" outlined required />
          <q-input type="password" name="password2" v-model="password2" label="Password" outlined required />
          <q-btn label="Sign in" type="submit" color="secondary"/>
        </q-form>
        <q-btn label="Sign out" color="secondary" @click="signOut" v-if="loggedIn"/>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import { api } from 'boot/axios'

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      username: null,
      password: null,
      username2: null,
      password2: null,
      loading: false,
      users: [],
      loggedIn: false,
      loggedUser: {},
      tableColumns: [
        { name: 'id', label: 'ID', sortable: true },
        { name: 'username', label: 'Username', sortable: true },
        { name: 'password', label: 'Password', sortable: false }
      ]
    }
  },
  mounted () {
    this.fetchData()
  },
  methods: {
    fetchData () {
      this.loading = true
      api.get('http://localhost:3000/users/')
        .then(response => {
          this.loading = false
          this.users = response.data
        }).catch(error => {
          this.response = 'Error: ' + error.response.status
        })
    },
    submitRegForm () {
      api.post('http://localhost:3000/users/create', {
        username: this.username,
        password: this.password
      }).then(response => {
        this.fetchData()
        this.username = ''
        this.password = ''
      }).catch(error => {
        this.response = 'Error: ' + error.response.status
      })
    },
    submitLoginForm () {
      api.post('http://localhost:3000/users/login', {
        username: this.username2,
        password: this.password2
      }).then(response => {
        this.loggedUser = response.data
        if (Object.keys(this.loggedUser).length > 0) {
          this.loggedIn = true
          this.username2 = ''
          this.password2 = ''
        }
      }).catch(error => {
        this.response = 'Error: ' + error.response.status
      })
    },
    signOut () {
      this.loggedIn = false
      this.loggedUser = {}
    }
  }
})
</script>
