<template>
    <goHome></goHome>
    <div class = "container-fluid">
        <div class = "row">
            <div class = "col-xl-9 text-start" style = "margin-top: 60px; margin-left: 100px">
                <h2>
                    <b>Редактирование записи по атаке</b>
                </h2>
            </div>
        </div>
        <div class = "row">
            <div class = "col-xl-9" style = "margin-top: 20px; margin-left: 60px">
                <textarea class="form-control" v-model="nameOfIncident" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите название инцидента">{{ this.nameOfIncident }}</textarea>
                <textarea class="form-control" v-model="Description" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите описание инцидента">{{ this.Description }}</textarea>
                <textarea class="form-control" v-model="Source" id="textAreaExample1" rows="4" style = "width: 500px;" placeholder="Введите источник инцидента">{{ this.Source }}</textarea>
            </div>
            <div class="col-xl-6 dropdown" style = "margin-left: 105px; margin-top: 20px">
                <button class="btn btn-secondary dropdown-toggle" type="button" id="dropdownMenuButton1" data-bs-toggle="dropdown" aria-expanded="false">
                    {{ this.Status }}
                </button>
                <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton1">
                    <div v-if="Status == 'Исчерпан'">
                        <li><a class="dropdown-item" @click="Status='В процессе решения'" href="#">В процессе решения</a></li>
                    </div>
                    <div v-if="Status == 'В процессе решения'">
                        <li><a class="dropdown-item" @click="Status='Исчерпан'" href="#">Исчерпан</a></li>
                    </div>
                </ul>
            </div>
        </div>
        <div class = "row">
            <div class = "col-xl-6 text-start" style = "margin-top: 20px; margin-left: 105px">
                <button type = "submit" @click="editAttack()" class = "btn btn-primary fw-bold text-uppercase" style = "width: 100px; margin-top: 25px;">edit</button>
            </div>
        </div>
    </div>
    <div v-if="ER">
        <h5 class = "addError">{{ ER }}</h5>
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
        nameOfIncident: null,
        Description: null,
        Source: null,
        Status: null,
        Attack: null,
        ER: null,
      }
    },

    methods: {
      
        async editAttack() {

            await axios.put('http://localhost:8000/api/updateNote/' + this.$route.params.id, {
                nameOfIncident: this.nameOfIncident,
                Description: this.Description,
                Source: this.Source,
                Status: this.Status,
                Attack: this.Attack,
            }, {
            headers: {
                Authorization: "Token " + localStorage.getItem('token'),
            }
            }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

            
        }
    },

    async mounted() {

        await axios.put('http://localhost:8000/api/updateNote/' + this.$route.params.id, {}, {
        headers: {
            Authorization: "Token " + localStorage.getItem('token'),
        }
        }).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER.detail != null) {
            router.push('/404');
            return;
        }

        this.ER = null;

        await axios.get('http://localhost:8000/api/updateNote/' + this.$route.params.id,
        ).then(response => (this.info = response.data)).catch(error => (this.ER = error.response.data))

        if ( this.ER != null ) {
            router.push('/404');
        }

        this.nameOfIncident = this.info.nameOfIncident;
        this.Description = this.info.Description;
        this.Source = this.info.Source;
        this.Attack = this.info.Attack;
        this.Status = this.info.Status;
        
        return;
    },

  };
</script>

<style>
</style>