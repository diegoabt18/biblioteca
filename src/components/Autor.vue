<template>
<br>
<br>
  <div id="Author" class="author">
    <div class="container col-4 text-center">
      <h2>Modulo Autores</h2>

      <form v-on:submit.prevent="processAuthor">
        <input class="form-control"
          type="text"
          v-model="Autor.author_name"
          placeholder="Nombre del Autor"
        />
        <br>
        <input class="form-control"
          type="text"
          v-model="Autor.author_surname"
          placeholder="Apellido del Autor"
        />
          <br>
          <button class="btn btn-primary form-control" type="input">Nuevo autor</button>
      </form>
    </div>
  </div>
<br>
<br>
  <h2 class="text-center"> Lista de Autores</h2>     
    <div class="container table-responsive" style="width:50%; max-height: 200px; overflow-y: scroll; overflow-x: hidden;">
        <table  class="table table-bordered text-center overflow-auto" style="margin-bottom: 0">
            <tr>
                <th>Id</th>
                <th>Autor</th>
                <th>Apellido</th>
            </tr>

            <tr v-for="autor in autores" :key="autor.author_id">
                <td>{{autor.author_id}}</td>
                <td>{{autor.author_name}}</td>
                <td>{{autor.author_surname }}</td>
            </tr>
        </table>
    </div>

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
      autores:[],
    };
  },
  mounted(){
    
  },
    apollo: {
        autores: {
            query: gql`
                query {
                    autores {
                    author_id
                    author_name
                    author_surname
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
    processAuthor: async function() {
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
            mutation ($author: AuthorInput!) {
            createAuthor(author: $author) {
              author_id
              author_name
              author_surname
            }
          }
          `,
          variables: {
            author: this.Autor,
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
  created: function () {
    this.$apollo.queries.autores.refetch();
  },
};
</script>

