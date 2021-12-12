<template>
    <div class="mb-3">
        <h2>Nuevo servicio</h2>
        <p>Ingrese sus datos para el nuevo servicio:</p>
        <p>Nombre: <input v-model="newprovider.name" type="text" required></p>
        <p>Ciudad: <input v-model="newprovider.city" type="text"></p>
        <p>Dirección: <input v-model="newprovider.address" type="text"></p>
        <p>Tipo de servicio: <input v-model="newprovider.tip_user" type="text"></p>
        <p>Celular: <input v-model="newprovider.mobile_num" type="number"></p>
        <p>Seguridad Social: <input v-model="newprovider.social_security" type="number"></p>
        <p>Telefono: <input v-model="newprovider.phone_num" type="number"></p>
        <p>Valor: <input v-model="newprovider.value" type="number"></p>
        <p>Numero de documento: <input v-model="newprovider.num_document" type="number"></p>
        <p>Tipo de documento: <input v-model="newprovider.tip_document" type="text"></p>
        <p>Contraseña: <input v-model="newprovider.password" type="password"></p>
        <Button label="Crear" @click="confirmRegister()"/>
    </div>
    
</template>

<script>
import gql from "graphql-tag";
import Button from 'primevue/button';


export default {
    name: 'NewProvider',
    components:{
        Button
    },
    data(){
        return{
            newprovider: {
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
                tip_document:''
            }
        }
    },
    methods:{
        confirmRegister(){
            console.log(this.newprovider);

            this.$apollo.mutate({
                    mutation: gql`
                    mutation Mutation($name: String!, $tipDocument: String!, $numDocument: Int!, $city: String!, $address: String!, $phoneNum: Int!, $mobileNum: Int!, $socialSecurity: Int!, $value: Int!, $password: String!, $tipUser: String!) {
                        newAccountService(name: $name, tip_document: $tipDocument, num_document: $numDocument, city: $city, address: $address, phone_num: $phoneNum, mobile_num: $mobileNum, social_security: $socialSecurity, value: $value, password: $password, tip_user: $tipUser){
                            name
                        }                      
                    }
                `,
                variables: {
                    name: this.newprovider.name,
                    tipDocument: this.newprovider.tip_document,
                    numDocument: this.newprovider.num_document,
                    city: this.newprovider.city,
                    address: this.newprovider.address,
                    phoneNum: this.newprovider.phone_num,
                    mobileNum: this.newprovider.mobile_num,
                    socialSecurity: this.newprovider.social_security,
                    value: this.newprovider.value,
                    password: this.newprovider.password,
                    tipUser: this.newprovider.tip_user

                }
                }).then(response => {
                    console.log("Se creo exitosamente: " + this.newprovider.name);
                    console.log(response)
                }).catch(e => {
                    console.log(JSON.stringify(e, null, 2));
                });

        }
        
        
    }
}
</script>