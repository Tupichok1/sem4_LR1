<template>
  <router-link :to="{ path: '/'}">
        <svg xmlns="http://www.w3.org/2000/svg" style= "position: absolute; top: 20px; left: 20px" width="35" height="35" fill="currentColor" class="bi bi-house-door" viewBox="0 0 16 16">
            <path d="M8.354 1.146a.5.5 0 0 0-.708 0l-6 6A.5.5 0 0 0 1.5 7.5v7a.5.5 0 0 0 .5.5h4.5a.5.5 0 0 0 .5-.5v-4h2v4a.5.5 0 0 0 .5.5H14a.5.5 0 0 0 .5-.5v-7a.5.5 0 0 0-.146-.354L13 5.793V2.5a.5.5 0 0 0-.5-.5h-1a.5.5 0 0 0-.5.5v1.293zM2.5 14V7.707l5.5-5.5 5.5 5.5V14H10v-4a.5.5 0 0 0-.5-.5h-3a.5.5 0 0 0-.5.5v4z"/>
        </svg>
    </router-link>
    <div class = "container-fluid">
        <div class = "row">
            <div class = "col-xl-9 text-start" style = "margin-top: 60px; margin-left: 100px">
                <h2>
                    <b>Создание записи по атаке</b>
                </h2>
            </div>
        </div>
        <div class = "row">
            <div class = "col-xl-9" style = "margin-top: 20px; margin-left: 60px">
                <textarea class="form-control" v-model="title" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите название атаки"></textarea>
                <textarea class="form-control" v-model="description" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите описание атаки"></textarea>
                <textarea class="form-control" v-model="shortDescription" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите короткое описание атаки"></textarea>
            </div>
            <div class = "col-xl-4 text-end" style = "margin-top: 20px; margin-left: 10px">
                <button type = "submit" @click="addAttack()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-top: 25px;">Add</button>
            </div>
        </div>
    </div>
    <div v-if="ER">
        <h5 class = "addError">Не все поля заполнены!</h5>
    </div>
</template>

<script>
import router from "../Router.js"
import axios from "axios";
  export default {
    name: "auth",

    data() {
      return {
        title: '',
        description: '',
        shortDescription: '',
        token: null,
        ER: null,
      }
    },

    methods: {
      async addAttack() {

        await axios.post('http://localhost:8000/api/createNote', {
            nameOfAttack: this.title,
            Description: this.description,
            ShortDescription: this.shortDescription
        }, 
        {
        headers: {
          Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))
      }
    },

    async mounted() {

        await axios.get('http://localhost:8000/api/takeToken', {
        headers: {
            Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER != null) {
            router.push('/404');
            return;
        }
    },

  }
</script>

<style>
</style>