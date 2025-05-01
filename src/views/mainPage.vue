<template>
  <section class = "homePage">
    <div class = "container-fluid" style = "margin-top: 50px; margin-bottom: 100px; border-bottom: 6px solid #eee; padding-bottom: 5px;">
      <div class="row text-center">
        <h1 class = "col-xl-6">SPA-кибербезопасность</h1>
        <div class = "col-xl-6" style = "margin-top: 10px;">
          <div v-if = "token" class = "d-flex justify-content-end">
            <h4 style = "margin-top: 10px">Hi, {{ login }}!</h4>
            <button type = "submit" @click="logout()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-left: 30px;">Logout</button>
          </div>
          <div v-else class = "d-flex justify-content-end">
            <button type = "submit" @click="signIn()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-left: 30px;">Sign in</button>
          </div>
        </div>
      </div>
    </div>
    <div class = "container">
      <div class = "row text-start">
        <h1 class = "col-xl-6">
          <b>Какие бывают кибератаки</b>
        </h1>
      </div>
    </div>
    <div v-for="Attack in Attacks" style = "margin-top: 30px">
      <div class = "container">
        <div class = "row">
          <div class = "col-xl-4">
            <router-link class = "text-start" style = "text-decoration: none; color: inherit; font-size: 2em; " :to="{ path: '/detail/' + Attack.id}">
              <b>{{ Attack.nameOfAttack }}</b>
            </router-link>
          </div>
        </div>
        <div class = "row">
          <h3 class = "col-xl-9 text-start" style = "margin-left: 2px;">{{ Attack.ShortDescription }}</h3>
        </div>
      </div>
    </div>
    <div class = "container-fluid">
      <div class = "row text-center">
        <div class = "col-xl-9 text-end" style = "margin-top: 20px;">
          <div v-if="token">
            <button type = "submit" @click="toAdd()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-left: 30px;">+ ADD</button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from 'axios';
import router from '../Router';
  export default {
    name: "mainPage",

    data() {
      return {
        login: '',
        token: null,
        info: null,
        ER: null,
        Attacks: null,
      }
    },

    methods: {
      async logout() {

        await axios.post('http://localhost:8000/api/logout', {}, {
        headers: {
          Authorization: "Token " + this.token,
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.info != null) {
          localStorage.clear();
          this.token = null;
          return;
        }
      },

      signIn() {

        router.push('authorization');
        return;
      },

      async toAdd() {

        await axios.get('http://localhost:8000/api/takeToken', {
        headers: {
            Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER != null) {
            router.push('/404');
            return;
        }

        router.push('addAttack');
        return;
      }

    },

    async mounted() {

      this.login = localStorage.getItem('login');

      await axios.get('http://localhost:8000/api/home',
        ).then(response => (this.Attacks = response.data)).catch(error => (this.ER = error.response.data))

      await axios.get('http://localhost:8000/api/takeToken', {
        headers: {
          Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

      if ( this.ER == null) {
        this.token = this.info.token;
        return;
      }

    },
  }
</script>

<style>

</style>
  