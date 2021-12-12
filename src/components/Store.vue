<template>
    <br>
    <table class="table container">
            <tr>
            <!-- columna uno -->
            <th scope="col" class="align-baseline">
                <div id="Categoria"  >
                    <div class="container text-center">
                    <h2>Modulo Compra</h2>
                    <form v-on:submit.prevent="proccesCompra"  >
                        <br>
                        <input class="form-control" 
                        type="text"
                        v-model="Compra.id"
                        placeholder="Id de la compra"
                        />
                        <br>
                        <input type="date" class="form-control" id="start" name="trip-start"
                        min="2021-01-01" max="2022-12-31" v-model="Compra.fecha">
                        <br>
                        <input class="form-control" 
                        type="text"
                        v-model="Compra.total"
                        placeholder="Total de compra" disabled
                        />
                        <br>
                        <input class="form-control" 
                        type="text"
                        v-model="Compra.accountId"
                        placeholder="id de cuenta" disabled
                        />
                        <br>
                        <button class="btn btn-primary form-control" type="input">Finalizar compra</button>
                    </form>
                    </div>
                </div>
            </th>
            <!-- Columna dos-->
            <th scope="col" class="align-baseline">

                <h3 class="container text-center">Detalle de compra</h3>     
                 <br>
                <select v-model="LibroSeleccionado" class="form-select" aria-label="Default select example">
                    <option selected>Seleccione un producto</option>
                    <option v-for="libro in libros" :key="libro.book_id" :value="libro">{{libro.book_title}}</option>
                </select>
                <br>
                <button type="button" v-on:click="addLibro" class="btn btn-outline-success form-control">agregar al carrito</button>
                <br>
                <br>
                <div class="container table-responsive" style=" max-height: 200px; overflow-y: scroll; overflow-x: hidden;">
                    <table  class="table table-bordered text-center overflow-auto" style="margin-bottom: 0">
                        <tr>
                            <th>Id</th>
                            <th>Nombre del libro</th>
                            <th>Valor</th>
                        </tr>

                        <tr v-for="libro in listaCompra" :key="libro.book_id">
                            <td>{{ libro.book_id}}</td>
                            <td>{{ libro.book_title}}</td>
                            <td>{{ libro.sale_price}}</td>
                        </tr>
                    </table>
                </div>
            </th>
            </tr>

    </table>
    <!--
    <div class="container">
        <button type="button" v-on:click="VerListadoCompras" class="btn btn-outline-warning form-control">Ver lista de compras</button>
    </div>
    -->
            
   
 
   

</template>


<script>
import gql from "graphql-tag";

export default {
  name: "store",

  data: function() {
    return {
      Compra:{
        id: "",
        fecha: "",
        total: null,
        accountId: localStorage.getItem("username") || "none",
        items: []
      },
      Categoria:{
          category_name:""
      },
      libros:[],
      listaCompra:[],
      LibroSeleccionado:{},
      VerLista:[],
    };
  },
  mounted(){
    
  },
    apollo: {
        libros: {
            query: gql`
               query{
                    libros {
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
                ` ,
                variables() {
                    return {
                      
                    };
            },
        },

    },
    methods: {
    proccesCompra: async function(){
        console.log(this.Compra);
      
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
           mutation($compra: compraInput!) {
                createCompra(compra: $compra) {
                    id
                    fecha
                    total
                    accountId
                    items
                }
            }
          `,
          variables: {
            compra: this.Compra,
          },
        })
        .then((result) => {
          alert("Nueva Compra agregada de Manera Correcta, Revise Historial");
        })
        .catch((error) => {
          alert("Ocurrio un error al crear una Compra");
        });
    },
    addLibro: function(){
        if(this.listaCompra.length==0){
            this.listaCompra.push(this.LibroSeleccionado);
            this.Compra.items.push(this.LibroSeleccionado.book_title);
            console.log(this.LibroSeleccionado.sale_price);
            this.Compra.total=this.Compra.total+this.LibroSeleccionado.sale_price;
        }
        else{
            if(!this.isKeyExists(this.listaCompra, this.LibroSeleccionado.book_title)){
            this.listaCompra.push(this.LibroSeleccionado);
            this.Compra.items.push(this.LibroSeleccionado.book_title);
            this.Compra.total=this.Compra.total+this.LibroSeleccionado.sale_price;
            }else
            {
                alert("Ya existe ese articulo en la compra")
            }
        }
    },
      isKeyExists:function(obj,clave){
          console.log(obj);
          console.log(clave);
        let saber=false;
        for(let lista of obj){
            console.log(lista.book_title)
            if(lista.book_title===clave){
                saber=true;
                return saber;
                break;
            }
        }
        return saber;
    },
    VerListadoCompras: function(){
        
    }
  },
  created: function () {
    this.$apollo.queries.libros.refetch();
  },
};
</script>