<template>
    <Navbar :logOut="logOut"/>
    <div class="container mt-5">
        <h4>Bienvenido al inicio componente Home</h4>
        
    </div>
   
</template>

<script>
import Navbar from './partials/Navbar.vue'

export default {
    name: 'Home',
    components: {
        Navbar
    },
    methods :{
        logOut(){
            this.$emit('logout');
        }
    }
}

</script>