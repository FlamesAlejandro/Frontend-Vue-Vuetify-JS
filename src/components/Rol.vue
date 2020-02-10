<template>
    <v-layout align-start>
        <v-flex>
            <v-data-table
      :headers="headers"
      :items="roles"
      :search="search"
      class="elevation-1"
    >
        
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>Roles</v-toolbar-title>
          <v-divider class="mx-4" inset vertical
          ></v-divider>
          <div class="flex-grow-1"></div>
          <v-text-field
          v-model="search"
          append-icon="search"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
          <v-spacer></v-spacer>
          
        </v-toolbar>
      </template>
      
      
      <template v-slot:item.condicion="{ item }">
        <!-- Para crear condicionales dentro de un datatable, hay que tratar a cada item con esta pestaña de template, y buscar el metodo, en este caso un v-if y v-else. Y mostrar de acuerdo al resultado -->
          <div v-if="item.condicion">
            <span class="blue--text">Activo</span>
          </div>
          <div v-else>
            <span class="red--text">Inactivo</span>
          </div>
      </template>
     
                           
      
      <template v-slot:no-data>
        <v-btn color="primary" @click="listar">Reset</v-btn>
      </template>
    </v-data-table>
        </v-flex>
    </v-layout>
</template>

<script>
    import axios from 'axios'
    export default {
        data(){
                return {
                    roles:[],
                            dialog: false,
                            
                    headers: [                 
                    { text: 'Nombre', value: 'nombre' },
                    { text: 'Descripcion', value: 'descripcion', sortable: false },
                    { text: 'Estado', value: 'condicion', sortable: false }
                    ],
                    search: ''
                    
                }
        },
        computed: {
            
        },

        watch: {
            
        },

        created () {
            this.listar();
        },

        methods:{
            listar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion={headers : header};
                axios.get('api/Roles/Listar',configuracion).then(function(response){
                    //console.log(response);
                    me.roles=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            }

          }
}
</script>