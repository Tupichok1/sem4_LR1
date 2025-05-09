<template>
  <goHome></goHome>
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
                <textarea class="form-control" v-model="title" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите название инцидента"></textarea>
                <textarea class="form-control" v-model="source" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите источник инцидента"></textarea>
                <textarea class="form-control" v-model="attack" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите название атаки"></textarea>
                <textarea class="form-control" v-model="description" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите описание инцидента"></textarea>
            </div>
            <div class="col-xl-9 dropdown" style = "margin-left: 105px; margin-top: 20px">
              <button class="btn btn-secondary dropdown-toggle" type="button" id="dropdownMenuButton1" data-bs-toggle="dropdown" aria-expanded="false">
                {{ this.status }}
              </button>
              <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton1">
                <li><a class="dropdown-item" @click="status='Исчерпан'" href="#">Исчерпан</a></li>
                <li><a class="dropdown-item" @click="status='В процессе решения'" href="#">В процессе решения</a></li>
              </ul>
            </div>
            <div class = "col-xl-4 text-start" style = "margin-top: 20px; margin-left: 105px">
                <button type = "submit" @click="addAttack()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-top: 25px;">Add</button>
            </div>
        </div>
        <div class = "row">
        </div>
    </div>
    <div v-if="ER">
        <h5 class = "addError">Не все поля заполнены!</h5>
    </div>
</template>

<script>
import goHome from "./goHome.vue"
import router from "../Router.js"
import axios from "axios";
  export default {
    name: "auth",
    components: {
      goHome
    },

    data() {
      return {
        title: '',
        description: '',
        source: '',
        status: 'Статус инцидента',
        attack: '',
        token: null,
        ER: null,
      }
    },

    methods: {
      async addAttack() {

        if ( this.status == 'Статус инцидента' ) {
          this.ER = true;
          return;
        }

        await axios.post('http://localhost:8000/api/createNote', {
            nameOfIncident: this.title,
            Description: this.description,
            Source: this.source,
            Attack: this.attack,
            Status: this.status 
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