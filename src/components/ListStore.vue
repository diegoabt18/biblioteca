<template>
   <div id="ListaCompra"  >
        <div class="container col-4 text-center">
            <br>
        <h2>Lista Compras</h2>

        <form v-on:submit.prevent="proccesBuscarCompra"  >
            <br>
            <input class="form-control" 
            type="text"
            v-model="idcompra"
            placeholder="Id de la compra"
            />
            <br>
            <br>
            <button class="btn btn-primary form-control" type="input">Buscar compra</button>
        </form>
        </div>
    
    </div>
    <br>
    <h4 class="container text-center">Lista de Compra</h4>     
    <div class="container table-responsive" style="width:50%; max-height: 200px; overflow-y: scroll; overflow-x: hidden;">
        <table  class="table table-bordered text-center overflow-auto" style="margin-bottom: 0">
            <tr>
                <th>Id</th>
                <th>Fecha</th>
                <th>Total</th>
                <th>cuenta</th>
                <th>libros</th>
            </tr>

            <tr v-for="compra in lista" :key="compra.id">
                <td>{{ compra.id}}</td>
                <td>{{ compra.fecha}}</td>
                <td>{{ compra.total}}</td>
                <td>{{ compra.accountId}}</td>
                <td>{{ compra.items}}</td>
            </tr>
        </table>
    </div>
   

</template>


<script>
import gql from "graphql-tag";

export default {
  name: "liststore",

  data: function() {
    return {
      idcompra:"",
      lista:[],
    };
  },
  mounted(){
    
  },
    methods: {
    proccesBuscarCompra: async function(){
      
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
        .query({
          query: gql`
            query($compraByidId: String!) {
                compraByid(id: $compraByidId) {
                    id
                    fecha
                    total
                    accountId
                    items
                }
            }
          `,
          variables: {
            compraByidId: this.idcompra,
          },
        })
        .then((result) => {
            this.lista=result.data;
          alert("Compra consultada exitosamente");
        })
        .catch((error) => {
          alert("Ocurrio un error al consultar la compra");
        });
    },
  },
};
</script>