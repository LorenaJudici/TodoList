<template>
  <div id="app">
    <div>
  <b-navbar toggleable="lg" type="dark" variant="dark" v-if="notIsloginPage">
    <b-navbar-brand href="#">Todo List</b-navbar-brand>

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <b-nav-item to="/">Tarefas</b-nav-item>
        <b-nav-item to="/form">Formulário</b-nav-item>
      </b-navbar-nav>
    </b-collapse>

    <b-navibar-nav right="">
      <b-nav-item
        @click="logout()"
        v-b-tooltip.hover
        title="Sair"
      >

      <i class="fas fa-sign-out-alt"></i>
        
    </b-nav-item>
      
    </b-navibar-nav>
  </b-navbar>
</div>

<transition name="fade" mode="out-in">
  <router-view/>
</transition>
    
  </div>
</template>

<script>
export default{
  computed: {
    notIsloginPage(){ // não mostra barra la em cima caso for tela de login ou de registro de usuario
      return this.$route.name !== 'login' && this.$route.name !== 'register';
    }
  },

  methods:{
    logout(){
      localStorage.removeItem('authUser');
      this.$router.push({name:'login'});
    }
  }
}
</script>

<style>
  .fade-enter-active, .fade-leave-active{
    transition-duration:0.1s;
    transition-property:opacity;
    transition-timing-function: ease;
  }

  .fade-enter, .fade-leave{
    opacity: 0;
  }
</style>