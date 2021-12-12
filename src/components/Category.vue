<template>
   <div id="Categoria" >
        <div class="container text-center">
            <br>
            <br>
       
        <h2>Modulo Categorias</h2>

        <form v-on:submit.prevent="proccesCategoria">
            <br>
            <input
            type="text"
            v-model="Categoria.category_name"
            placeholder="Nombre de la categoria"
            />
            <br>
            <br>
            <button class="btn" type="submit">Nueva categoria</button>
        </form>
        </div>
    
    </div>
    <br>
    <h4 class="container text-center">Lista de Categorias</h4>     
    <div class="container table-responsive" style="width:50%; max-height: 200px; overflow-y: scroll; overflow-x: hidden;">
        <table  class="table table-bordered text-center overflow-auto" style="margin-bottom: 0">
            <tr>
                <th>Id</th>
                <th>Categoria</th>
            </tr>

            <tr v-for="categorias in categoryDetails" :key="categorias.category_id">
                <td>{{ categorias.category_id}}</td>
                <td>{{ categorias.category_name}}</td>
            </tr>
        </table>
    </div>
   

</template>


<script>
import gql from "graphql-tag";

export default {
  name: "categoria",

  data: function() {
    return {
      Categoria:{
          category_name:""
      },
      categoryDetails:[],
    };
  },
  mounted(){
    
  },
    apollo: {
        categoryDetails: {
            query: gql`
                query {
                    categoryDetails {
                        category_id
                        category_name
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
    proccesCategoria: async function(){
            console.log(this.Categoria.category_name);
      
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
            mutation($categoryName: CategoryInput!) {
                createCategory(category_name: $categoryName) {
                    category_id
                    category_name
                }
            }
          `,
          variables: {
            categoryName: this.Categoria,
          },
        })
        .then((result) => {
          alert("Nueva categoria agregada de Manera Correcta, Revise Historial");
        })
        .catch((error) => {
          alert("Ocurrio un error al crear una categoria");
        });
    },
  },
  created: function () {
    this.$apollo.queries.categoryDetails.refetch();
  },
};
</script>