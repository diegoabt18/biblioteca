<template>

  <div id="Author" class="author">
    <div class="container_author">
      <h2>Modulo Autores</h2>

      <form v-on:submit.prevent="processAuthor">
        <input class="form-control"
          type="text"
          v-model="Author.author_name"
          placeholder="Nombre del Autor"
        />
        <br>
          <br>
          <button class="btn btn-primary form-control" type="input">Nueva categoria</button>
      </form>
    </div>
  </div>
<!--
  <h2>Autores</h2>     
    <div class="container-table">
        <table>
            <tr>
                <th>Id</th>
                <th>Autor</th>
                <th>Apellido</th>
            </tr>

            <tr v-for="autores in transactionByUsername" :key="transaction.id">
                <td>{{ autores.author_id}}</td>
                <td>{{ autores.author_name}}</td>
                <td>{{ autores.author_surname }}</td>
            </tr>
        </table>
    </div>
-->
</template>


<script>
import gql from "graphql-tag";

export default {
  name: "autor",

  data: function() {
    return {
      Autor:{
          author_name:"",
          author_surname:""
      },
      authorDetails:[],
    };
  },
  mounted(){
    
  },
    apollo: {
        authorDetails: {
            query: gql`
                query {
                    authorDetails {
                        category_id
                        category_name
                        category_surname
                    }
                }
                ` ,
                variables() {
                    return {
                      
                    };
            },
        },
    },


    methods: {
    processAutor: async function() {
            console.log(this.Autor.author_name);
      
      if (localStorage.getItem("token_access")  === null ||
          localStorage.getItem("token_refresh") === null ) {
        this.$emit("logOut");
        return;
      }

      localStorage.setItem("token_access", "");

      await this.$apollo
        .mutate({
          mutation: gql`
            mutation ($refresh: String!) {
              refreshToken(refresh: $refresh) {
                access
              }
            }
          `,
          variables: {
            refresh: localStorage.getItem("token_refresh"),
          },
        })
        .then((result) => {
          localStorage.setItem("token_access", result.data.refreshToken.access);
        })
        .catch((error) => {
          this.$emit("logOut");
          return;
        });
      
      await this.$apollo
        .mutate({
          mutation: gql`
            mutation($authorName: AuthorInput!) {
              createAuthor(author_name: $authorName) {
                author_id
                author_name
                author_surname
              }
            }
          `,
          variables: {
            authorName: this.Autor,
          },
        })
        .then((result) => {
          alert("Nuevo Autor Agregado de Manera Correcta, Revise Historial");
        })
        .catch((error) => {
          alert("Ocurrió un error al crear el autor");
        });
    },
  },
};
</script>

