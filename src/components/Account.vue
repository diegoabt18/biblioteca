<template>
  <div id="Historial">

    <div class="container">
      <h2>
        Titular Cuenta:
        <span>{{ username }}</span>
      </h2>
      <h2>
        Correo:
        <span>{{ userEmail }}</span>
      </h2>
      <h2>
        Dirección:
        <span>{{ userAddress }}</span>
      </h2>
      <h2>
        Telefono:
        <span>{{ userPhone }}</span>
      </h2>
    </div>

    <h2>Mis compras</h2>     
    <div class="container-table">
        <table>
            <tr>
                <th>ID</th>
                <th>Fecha</th>
                <th>Item</th>
                <th>Valor</th>
            </tr>

            <tr v-for="compra in compras" :key="compra.id">
                <td></td>
                <td></td>
                <td></td>
                <td></td>
            </tr>
        </table>
    </div>
  </div>

</template>


<script>
import gql from "graphql-tag";

export default {
  name: "Account",

  data: function () {
    return {
      username: localStorage.getItem("username") || "none",
      transactionByUsername: [],
      accountByUsername: {
        username: "",
        userEmail: "",
        userAddress: "",
        userPhone: "",
      }
    };
  },

  apollo: {
    transactionByUsername: {
      query: gql`
        query ($username: String!) {
          transactionByUsername(username: $username) {
            id
            username
            userEmail
            userAddress
            userPhone
          }
        }
      `,
      variables() {
        return {
          username: this.username,
        };
      },
    },

    accountByUsername: {
      query: gql`
        query ($username: String!) {
          accountByUsername(username: $username) {
            username
            userEmail
            userAddress
            userPhone
          }
        }
      `,
      variables() {
        return {
          username: this.username,
        };
      },
    },
  },

  created: function () {
    this.$apollo.queries.transactionByUsername.refetch();
    this.$apollo.queries.accountByUsername.refetch();
  }
};
</script>


<style>
#Historial {
  width: 100%;

  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-direction: column;
}

#Historial .container-table{
    width:50%;
    
    max-height: 250px;
    overflow-y: scroll;
    overflow-x: hidden;
}

#Historial table {
  width: 100%;
  border-collapse: collapse;
  border: 1px solid rgba(0, 0, 0, 0.3);
  
}

#Historial table td,
#Historial table th {
  border: 1px solid #ddd;
  padding: 8px;
}

#Historial table tr:nth-child(even) {
  background-color: #f2f2f2;
}

#Historial table tr:hover {
  background-color: #ddd;
}

#Historial table th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: crimson;
  color: white;
}

#Historial > h2 {
  color: #283747;
  font-size: 25px;
}

#Historial .container {
  padding: 30px;
  border: 3px solid rgba(0, 0, 0, 0.3);
  border-radius: 20px;
  margin: 5% 0 1% 0;
}

#Historial  .container h2 {
  font-size: 25px;
  color: #283747;
}
#Historial .container span {
  color: crimson;
  font-weight: bold;
}
</style>