<template>
    <router-link :to="{ path: '/'}">
        <svg xmlns="http://www.w3.org/2000/svg" style= "position: absolute; top: 20px; left: 20px" width="35" height="35" fill="currentColor" class="bi bi-house-door" viewBox="0 0 16 16">
            <path d="M8.354 1.146a.5.5 0 0 0-.708 0l-6 6A.5.5 0 0 0 1.5 7.5v7a.5.5 0 0 0 .5.5h4.5a.5.5 0 0 0 .5-.5v-4h2v4a.5.5 0 0 0 .5.5H14a.5.5 0 0 0 .5-.5v-7a.5.5 0 0 0-.146-.354L13 5.793V2.5a.5.5 0 0 0-.5-.5h-1a.5.5 0 0 0-.5.5v1.293zM2.5 14V7.707l5.5-5.5 5.5 5.5V14H10v-4a.5.5 0 0 0-.5-.5h-3a.5.5 0 0 0-.5.5v4z"/>
        </svg>
    </router-link>
    <div class = "container">
        <div class = "row text-center">
            <h2 class = "col-xl-9 text-start" style = "margin-top: 80px;">Подробная информация об атаке</h2>
            <div class = col-xl-10>
                <div class = "d-flex justify-content-start" style = "margin-top: 20px;">
                    <h3>
                        <b>{{ this.nameOfAttack }}</b>
                    </h3>
                    <h5 style = "margin-top: 7px; margin-left: 20px;">
                        {{ this.Author }}, {{ this.date }}
                    </h5>
                </div>
            </div>
            <h3 class = "col-xl-9 text-start" style = "margin-top: 30px;">
                {{ this.Description }}
            </h3>
        </div>
    </div>
    <div v-if="IsAuthor" class = "container">
        <div class = "row">
            <div class = "d-flex justify-content-start" style = "margin-top: 20px;">
                <div class = "col-xl-4">
                    <button type = "submit" @click="editAttack()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-right: 20px">Edit</button>
                    <button type = "submit" @click="deleteAttack()" class = "btn btn-danger fw-bold text-uppercase" style = "width: 100px">Delete</button>
                </div>
            </div>
        </div>
    </div>
    <router-view></router-view>
</template>

<script>
import router from "../Router.js"
import axios from "axios";
  export default {
    name: "auth",

    data() {
      return {
        nameOfAttack: '',
        Description: '',
        ShortDescription: '',
        Author: '',
        IsAuthor: null,
        date: null,
        ER: null,
      }
    },

    methods: {
      async deleteAttack() {

        await axios.delete('http://localhost:8000/api/updateNote/' + this.$route.params.id, {
        headers: {
            Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data));

        router.push({name: 'home'});
      },

      editAttack() {
        router.push({path: '/edit/' + this.$route.params.id });
        return;
      },

    },

    async mounted() {

        await axios.get('http://localhost:8000/api/updateNote/' + this.$route.params.id,
        ).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER != null ) {
            router.push('/404');
        }

        if ( this.info != null) {
          this.nameOfAttack = this.info.nameOfAttack;
          this.Description = this.info.Description;
          this.ShortDescription = this.info.ShortDescription;
          this.date = this.info.Date;
          this.Author = this.info.Author;
        }
        
        await axios.put('http://localhost:8000/api/updateNote/' + this.$route.params.id, {}, {
        headers: {
            Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER.detail == null) {
            this.IsAuthor = true;
            return;
        }
    },

  }
</script>

<style>
</style>