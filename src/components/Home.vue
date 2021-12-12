<template>
    <Navbar :logOut="logOut"/>
    <div class="container mt-5">
        <h4>Bienvenido usuario: {{username}}</h4>
        <div class="mb-3">
            <Button label="Registrar Servicio" @click="showRegistration()" />
        </div>
        <h3>En el siguiente espacio podras ver los servicios que tenemos para ti</h3>
        <br>
        <br>
        <div class="row">
            <div class="col-4" style="width: 18rem;" v-for="lista in list" :key="lista.id">
                <div class="card-img-top" alt="..." height="300" >
                    <img v-bind:src='"https://assets.quick.com.co/images/pictures/aseo_oficina.jpg"' class="card-img-top" height="300" alt="...">
                    <div class="card-body">
                      <h5 class="card-title">Empresa {{lista.name}}</h5>
                      <p>Ciudad: <b> {{lista.city}}</b></p>
                      <p class="card-text">Se ofrece servicio de {{lista.tip_user}} por el 
                          precio de ${{lista.value}} la hora
                      </p>
                      <button class="btn btn-primary me-1"
                      v-on:click="showProvider(lista)"
                      >Solicitar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <Dialog header="Proveedor de servicios" v-model:visible="display" :style="{width: '30vw'}">
        <Provider/>
    </Dialog>
    <Dialog header="Registar Nuevo Servicio" v-model:visible="displayre" :style="{width: '30vw'}">
        <NewProvider/>
    </Dialog>
   
</template>

<script>
import gql from "graphql-tag";
import Navbar from './partials/Navbar.vue'
import Dialog from 'primevue/dialog';
import Provider from '../components/Provider.vue'
import NewProvider from '../components/NewProvider.vue'
import Button from 'primevue/button';


export default {
    name: 'Home',
    data(){
        return{
            list:[],
            username: '',
            display: false,
            displayre: false
        }
    },
    components: {
        Navbar,
        Dialog,
        Provider,
        Button,
        NewProvider
    },
    methods :{
        logOut(){
            this.$emit('logout');
        },
        showProvider(lista){
            this.display = true;
            localStorage.setItem('provider',lista.id)
            console.log(localStorage.getItem('provider'))
        },
        showRegistration(){
            this.displayre = true;
        }
    },
    created(){
        this.username = localStorage.getItem('username') || '';
        this.$apollo.query({
            query: gql`
                query {
                    accountsProviders {
                        name
                        city
                        address
                        tip_user
                        mobile_num
                        social_security
                        phone_num
                        value
                        password
                        num_document
                        tip_document
                        id
                    }
                }
            `
        }).then(response => {
                this.list=response.data.accountsProviders;
                console.log(response.data.accountsProviders)
            }).catch(e => {
                console.log(JSON.stringify(e, null, 2));
            });
    }
}

</script>