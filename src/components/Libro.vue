<template>

  <div id="Transaction" class="transaction">
    <div class="container_transaction">
      <h2>Realizar Transacción</h2>

      <form v-on:submit.prevent="processTransaction">
        <input
          type="text"
          v-model="createTransaction.usernameDestiny"
          placeholder="Usuario Destino"
        />
        <br />
        <input
          type="number"
          v-model="createTransaction.value"
          placeholder="Valor"
        />
        <br />
        <button type="submit">Realizar Transacción</button>
      </form>
    </div>
  </div>
<!--
  <h2>Libros</h2>     
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
  name: "libro",

  data: function() {
    return {
      createTransaction: {
        usernameOrigin: localStorage.getItem("username"),
        usernameDestiny: "",
        value: "",
      },
      Libro:{
          book_id: "",
          ISBN:"",
          book_title:"",
          image: "",
          publication_year:"",
          language:"",
          sale_price: "",
          quantity_for_sale:"",
          author_id:"",
          category_id:""

      },
    };
  },
  mounted(){
    this.processRefresh();
  },
  methods: {
    processRefresh: async function() {
      
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
      
    },
    ProcessConsultarLibros: async function(){
        await this.$apollo
        .mutate({
          mutation: gql`
            mutation($transaction: TransactionInput!) {
              createTransaction(transaction: $transaction) {
                date
                id
                usernameDestiny
                usernameOrigin
                value
              }
            }
          `,
          variables: {
            transaction: this.createTransaction,
          },
        })
        .then((result) => {
          alert("Transacción Realizada de Manera Correcta, Revise Historial");
        })
        .catch((error) => {
          alert("Saldo Insuficiente o Destino Incorrecto");
        });
    }
  },
};
</script>