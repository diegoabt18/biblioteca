<template>
  <div id="app" class="app">
    <div class="header">
      <nav
        class="navbar navbar-expand-lg navbar-light"
        style="background-color: #e3f2fd"
      >
        <div class="container-fluid">
          <a class="navbar-brand" href="#">Biblioteca</a>
          <button
            class="navbar-toggler"
            type="button"
            data-bs-toggle="collapse"
            data-bs-target="#navbarNavAltMarkup"
            aria-controls="navbarNavAltMarkup"
            aria-expanded="false"
            aria-label="Toggle navigation"
          >
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
            <div class="navbar-nav">
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadHome"
                >Inicio</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadAccount"
                >Cuenta</b-button
              >
              
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadAutor"
                >Autores</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadLibro"
                >Libros</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadCategoria"
                >Categorias</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadStore"
                >Compra</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="is_auth"
                v-on:click="loadListStore"
                >Lista Compra</b-button
              >
              <b-button class="btn btn-light" v-if="is_auth" v-on:click="logOut"
                >Cerrar Sesión</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="!is_auth"
                v-on:click="loadLogIn"
                >Iniciar Sesión</b-button
              >
              <b-button
                class="btn btn-light"
                v-if="!is_auth"
                v-on:click="loadSignUp"
                >Registrarse</b-button
              >
            </div>
          </div>
        </div>
      </nav>
    </div>

    <div class="main-component">
      <router-view
        v-on:completedLogIn="completedLogIn"
        v-on:completedSignUp="completedSignUp"
        v-on:logOut="logOut"
      >
      </router-view>
    </div>

    <div class="footer">
      <h2>Misión TIC 2022</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",

  computed: {
    is_auth: {
      get: function () {
        return this.$route.meta.requiresAuth;
      },
      set: function () {},
    },
  },

  methods: {
    loadLogIn: function () {
      this.$router.push({ name: "logIn" });
    },

    loadSignUp: function () {
      this.$router.push({ name: "signUp" });
    },

    completedLogIn: function (data) {
      localStorage.setItem("username", data.username);
      localStorage.setItem("token_access", data.token_access);
      localStorage.setItem("token_refresh", data.token_refresh);
      alert("Autenticación Exitosa");
      this.loadHome();
    },

    completedSignUp: function (data) {
      alert("Registro Exitoso");
      this.completedLogIn(data);
    },

    loadHome: function () {
      this.$router.push({ name: "home" });
    },

    loadAccount: function () {
      this.$router.push({ name: "account" });
    },

    loadTransaction: function () {
      this.$router.push({ name: "transaction" });
    },

    logOut: function () {
      localStorage.clear();
      alert("Sesión Cerrada");
      this.loadLogIn();
    },
    loadAutor: function () {
      this.$router.push({ name: "autor" });
    },
    loadLibro: function () {
      this.$router.push({ name: "libro" });
    },
    loadCategoria: function () {
      this.$router.push({ name: "category" });
    },
    loadStore: function () {
      this.$router.push({ name: "store" });
    },
    loadListStore: function () {
      this.$router.push({ name: "liststore" });
    },
  },
};
</script>

<style scoped>
body {
  margin: 0 0 0 0;
}

.main-component {
  height: 95%;
  margin-bottom: 30px;
  padding: 0%;

  background: #fdfefe;
}

.footer {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 10vh;
  min-height: 70px;

  background-color: #6a727a;
  color: #e5e7e9;
}

.footer h2 {
  width: 100%;
  height: 100%;

  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
