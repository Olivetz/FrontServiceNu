<template>
   
   <h2>{{provider.name}}</h2>

   <div class="mb-3">
      <p>{{provider.city}}</p>
      <p>{{provider.address}}</p>
      <p>{{provider.phone_num}}</p>
      <p>{{provider.mobile_num}}</p>
      <p>Este servicio es de: {{provider.tip_user}}</p>
   </div>
   <div class="mb-3" style="display:flex; justify-content:space-around;">
        <Button label="Eliminar" @click="deleteProvider()"/>
        <Button class="btn btn-danger" label="Contratar"/>
        <Button label="Actualizar" @click="updateProvider()"/>
    </div>
    <Dialog header="Eliminar Proveedor" v-model:visible="displayde" :style="{width: '20vw'}">
        <p>Ingrese su contraseña de proveedor para eliminar el servicio completamente</p>
        <p>Contraseña: <input v-model="password" type="password"></p>
        <Button label="Confirmar" @click="confirm()"/>
        <p v-if="message">La contraseña no coincide</p>
    </Dialog>
 
   
</template>

<script>
import gql from "graphql-tag";
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';

export default{
    name: 'Provider',
    components:{
        Button,
        Dialog
    },
     data() {
        return {
            message: false,
            password: '',
            displayde: false,
            id: localStorage.getItem('provider') || '',
            provider: {
                name:'',
                city:'',
                address:'',
                tip_user:'',
                mobile_num:0,
                social_security:0,
                phone_num:0,
                value:0,
                password:'',
                num_document:0,
                tip_document:'',
                id: this.id
            }
        }
    },
    methods:{
        deleteProvider(){
            this.displayde = true;
        },
        updateProvider(){

        },
        confirm(){
            console.log(this.password);

            if(this.password == this.provider.password){
                console.log("Son iguales");
                this.message = false;

                this.$apollo.mutate({
                    mutation: gql`
                    mutation Mutation($id: String!) {
                        deleteAccount(id: $id){
                            name
                        }                      
                    }
                `,
                variables: {
                    id: this.id,
                }
                }).then(response => {
                    console.log("Se borro exitosamente: " + this.provider.name);
                    console.log(response)
                }).catch(e => {
                    console.log(JSON.stringify(e, null, 2));
                });

            }
            else{
                console.log("No son iguales");
                this.message = true;
            }

            
        }
    },
    created() {
        this.$apollo.query({
            query: gql`
               query($id: String!) {
                    accountById(id: $id) {
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
            `,
            variables: {
                id: this.id
            }
        }).then(response => {
            console.log(response.data);
            this.provider = response.data.accountById;
        }).catch(e => {
            console.log(this.id)
            console.log(JSON.stringify(e, null, 2));
        });
    }
    
  
}
</script>
