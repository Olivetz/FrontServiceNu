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
    <Dialog header="Eliminar Servicio" v-model:visible="displayde" :style="{width: '20vw'}">
        <p>Ingrese su contraseña de proveedor para eliminar el servicio completamente</p>
        <p>Contraseña: <input v-model="password" type="password"></p>
        <Button label="Confirmar" @click="confirmDelete()"/>
        <p v-if="message">La contraseña no coincide</p>
    </Dialog>
    <Dialog header="Actualizar Servicio" v-model:visible="displayup" :style="{width: '20vw'}">
        <p>Ingrese su contraseña de proveedor para actualizar el servicio</p>
        <p>Contraseña: <input v-model="password" type="password"></p>
        <Button label="Confirmar" @click="showForm()"/>
        <p style="color:red;" v-if="message">La contraseña no coincide</p>
    </Dialog>
    <Dialog header="Modificar Servicio" v-model:visible="displayform" :style="{width: '30vw', height: '30vw'}">
        <p>Realice las modificaciones del servicio: {{provider.name}}</p>
        <p>Nombre: <input v-model="updateprovider.name" type="text"></p>
        <p>Ciudad: <input v-model="updateprovider.city" type="text"></p>
        <p>Dirección: <input v-model="updateprovider.address" type="text"></p>
        <p>Tipo de servicio: <input v-model="updateprovider.tip_user" type="text"></p>
        <p>Celular: <input v-model="updateprovider.mobile_num" type="number"></p>
        <p>Seguridad Social: <input v-model="updateprovider.social_security" type="number"></p>
        <p>Telefono: <input v-model="updateprovider.phone_num" type="number"></p>
        <p>Valor: <input v-model="updateprovider.value" type="number"></p>
        <p>Numero de documento: <input v-model="updateprovider.num_document" type="number"></p>
        <p>Tipo de documento: <input v-model="updateprovider.tip_document" type="text"></p>
        <Button label="Modificar" @click="confirmUpdate()"/>
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
            displayup: false,
            displayform: false,
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
            },
            updateprovider: {
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
            this.message = false;
        },
        updateProvider(){
            this.displayup = true;
            this.message = false;
        },
        showForm(){
            if(this.password == this.provider.password){
                this.displayup = false;
                this.displayform = true;
                this.message = false;

                this.updateprovider = Object.assign({}, this.provider);
            }
            else{
                this.message = true;
            }
            
        },
        confirmUpdate(){
            console.log(this.updateprovider);

            this.$apollo.mutate({
                    mutation: gql`
                    mutation Mutation($updateAccountId: String!, $name: String!, $tipDocument: String!, $numDocument: Int!, $city: String!, $address: String!, $phoneNum: Int!, $mobileNum: Int!, $socialSecurity: Int!, $value: Int!, $password: String!, $tipUser: String!) {
                        updateAccount(id: $updateAccountId, name: $name, tip_document: $tipDocument, num_document: $numDocument, city: $city, address: $address, phone_num: $phoneNum, mobile_num: $mobileNum, social_security: $socialSecurity, value: $value, password: $password, tip_user: $tipUser){
                            name
                        }                      
                    }
                `,
                variables: {
                    updateAccountId: this.id,
                    name: this.updateprovider.name,
                    tipDocument: this.updateprovider.tip_document,
                    numDocument: this.updateprovider.num_document,
                    city: this.updateprovider.city,
                    address: this.updateprovider.address,
                    phoneNum: this.updateprovider.phone_num,
                    mobileNum: this.updateprovider.mobile_num,
                    socialSecurity: this.updateprovider.social_security,
                    value: this.updateprovider.value,
                    password: this.updateprovider.password,
                    tipUser: this.updateprovider.tip_user

                }
                }).then(response => {
                    console.log("Se actualizo exitosamente: " + this.updateprovider.name);
                    this.displayform = false;
                    console.log(response)
                }).catch(e => {
                    console.log(JSON.stringify(e, null, 2));
                });

        },
        confirmDelete(){
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
                    this.displayde = false;
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
