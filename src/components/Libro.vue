<template>
<br>
<br>
  <div id="Author" class="author">
    <div class="container col-4 text-center">
      <h2>Módulo Libros</h2>

      <form v-on:submit.prevent="processBook">
          <input class="form-control"
            type="text"
            v-model="Libro.isbn"
            placeholder="ISBN"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.book_title"
            placeholder="Titulo"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.publication_year"
            placeholder="Año de publicación"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.language"
            placeholder="Idioma"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.sale_price"
            placeholder="Precio"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.quantity_for_sale"
            placeholder="Cantidad"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.author_id"
            placeholder="Author"
          />
          <br>
          <input class="form-control"
            type="text"
            v-model="Libro.category_id"
            placeholder="Categoria"
          />
          <br>
          <button class="btn btn-primary form-control" type="input">Nuevo libro</button>
      </form>
    </div>
  </div>
<br>
<br>
  <h4 class="text-center"> Lista de Libros</h4>
    <div class="container table-responsive" style="width:50%; max-height: 200px; overflow-y: scroll; overflow-x: hidden;">
        <table  class="table table-bordered text-center overflow-auto" style="margin-bottom: 0">
            <tr>
                <th>ISBN</th>
                <th>Titulo</th>
                <th></th>
                <th>Año</th>
                <th>Idioma</th>
                <th>Precio</th>
                <th>Cantidad</th>
                <th>Autor</th>
                <th>Categoria</th>
            </tr>

            <tr v-for="libro in libros" :key="libro.book_id">
                <td>{{libro.ISBN}}</td>
                <td>{{libro.book_title}}</td>
                <td>{{libro.image }}</td>
                <td>{{libro.publication_year }}</td>
                <td>{{libro.language }}</td>
                <td>{{libro.sale_price }}</td>
                <td>{{libro.quantity_for_sale }}</td>
                <td>{{libro.author_id }}</td>
                <td>{{libro.category_id }}</td>
            </tr>
        </table>
    </div>

</template>


<script>
import gql from "graphql-tag";

export default {
  name: "libro",

  data: function() {
    return {
      Libro:{
          book_id:"",
          ISBN:"",
          book_title:"",
          image:"",
          publication_year:"",
          language:"",
          sale_price:"",
          quantity_for_sale:"",
          author_id:"",
          category_id:"",
      },
      libros:[],
    };
  },
  mounted(){

  },
    apollo: {
        libros: {
            query: gql`
                query {
                  libros {
                    book_id
                    ISBN
                    book_title
                    image
                    publication_year
                    language
                    sale_price
                    quantity_for_sale
                    author_id
                    category_id
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
    processBook: async function() {
            console.log(this.Libro.book_title);

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
            mutation ($libro: LibroInput!) {
            createBook(libro: $libro) {
              book_id
              book_title
              ISBN
              image
              publication_year
              language
              sale_price
              quantity_for_sale
              author_id
              category_id
            }
          }
          `,
          variables: {
            libro: this.Libro,
          },
        })
        .then((result) => {
          alert("Nuevo libro Agregado de Manera Correcta, Revise Historial");
        })
        .catch((error) => {
          alert("Ocurrió un error al crear el libro");
        });
  },
  },
  created: function () {
    this.$apollo.queries.libros.refetch();
  },
};
</script>

