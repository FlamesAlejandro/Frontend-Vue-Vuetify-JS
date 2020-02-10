<template>
    <v-layout align-start>
        <v-flex>
            <v-data-table :headers="headers" :items="categorias" :search="search" class="elevation-1">
        
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>Categorías</v-toolbar-title>
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
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on }">
              <v-btn color="primary" dark class="mb-2" v-on="on">Nuevo</v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="headline">{{ formTitle }}</span>
              </v-card-title>
              <v-form class="" ref="form" v-model="valid" lazy-validation>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="nombre" label="Nombre" :rules="validaNombre" :counter="50"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="descripcion" label="Descripción" :rules="validaDescripcion" :counter="250"></v-text-field>
                    </v-col>
                                        
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <div class="flex-grow-1"></div>
                <v-btn color="blue darken-1" text @click="close">Cancelar</v-btn>
                <v-btn color="blue darken-1" text @click="guardar">Guardar</v-btn>
              </v-card-actions>
              </v-form>
            </v-card>
          </v-dialog>
          <v-dialog v-model="adModal" max-width="300">
            <v-card-text>
              Estás a punto de
              <span v-if="adAccion==1">Activar</span>
              <span v-if="adAccion==2">Desactivar</span>
              el ítem {{adNombre}}
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="green darken-1" @click="activarDesactivarCerrar">
                Cancelar
              </v-btn>
              <v-btn v-if="adAccion==1" color="orange darken-4" @click="Activar">
                Aceptar
              </v-btn>
              <v-btn v-if="adAccion==2" color="orange darken-4" @click="Desactivar">
                Aceptar
              </v-btn>
            </v-card-actions>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.opciones="{ item }">
                    <v-icon
                        small
                        class="mr-2"
                        @click="editItem(item)"
                      >
                        editar
                      </v-icon>
                      <template v-if="item.condicion">
                      <v-icon
                        small
                        @click="activarDesactivarMostrar(2,item)"
                      >
                        block
                    </v-icon>
                      </template>
                      <template v-else>
                      <v-icon
                        small
                        @click="activarDesactivarMostrar(1,item)"
                      >
                        check
                    </v-icon>
                      </template>
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
                    categorias:[],
                            dialog: false,
                            
                    headers: [                      
                    { text: 'Opciones', value: 'opciones', sortable: false },                    
                    { text: 'Nombre', value: 'nombre' },
                    { text: 'Descripcion', value: 'descripcion', sortable: false },
                    { text: 'Estado', value: 'condicion', sortable: false }
                    ],
                    search: '',
                    editedIndex: -1,                    
                    id: '',
                    nombre: '',
                    descripcion: '',
                    valid: true,
                    validaNombre:[
                      v => !!v || 'Nombre es requerido',
                      v => v.length <= 50 || 'El nombre debe tener más de 3 caracteres y menos de 50',
                      v => v.length > 3 || 'El nombre debe tener más de 3 caracteres y menos de 50',
                    ],
                    validaDescripcion: [
                      v => !!v || 'Descripcion es requerida',
                      v => v.length <= 250 || 'La descripción debe tener más de 3 caracteres y menos de 250',
                      v => v.length > 3 || 'La descripción debe tener más de 3 caracteres y menos de 250',
                    ],
                    adModal: 0,
                    adAccion: 0,
                    adNombre: '',
                    adId: ''
                }
        },
        computed: {
            formTitle () {
            return this.editedIndex === -1 ? 'Nueva categoria' : 'Actualizando categoria'
            },
        },

        watch: {
            dialog (val) {
            val || this.close()
            },
        },

        created () {
            this.listar();
        },

        methods:{
            
            listar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Categorias/Listar',configuracion).then(function(response){
                    //console.log(response);
                    me.categorias=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },

           

            editItem (item) {
              this.id = item.idcategoria;
              this.nombre = item.nombre;
              this.descripcion = item.descripcion;
              this.editedIndex=1;
              this.dialog = true
            },

            deleteItem (item) {
              const index = this.desserts.indexOf(item)
              confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
            },

            close () {

              this.dialog = false;
              this.limpiar();
              
            },
            limpiar (){
                this.id="";
                this.nombre="";
                this.descripcion="";
                this.editedIndex=-1;
            },

            guardar () {
                //if (this.validar()){
                 // return;
                  // Acá ira a validar y ver que todo esta bien, en caso de no lo termina acá
               // }
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion={headers : header};
               if (this.$refs.form.validate()){
                 if (this.editedIndex > -1) {
                    //Código para editar UPDATE
                    // para editar se usa el put
                    let me=this;
                    axios.put('api/Categorias/Actualizar',{
                        'idcategoria':me.id,
                        'nombre': me.nombre,
                        'descripcion': me.descripcion
                    },configuracion).then(function(response){
                        me.close();
                        me.listar();
                        me.limpiar();                        
                    }).catch(function(error){
                        console.log(error);
                    });

                } else { 
                    //Código para guardar INSERT
                    let me=this;
                    axios.post('api/Categorias/Crear',{
                        'nombre': me.nombre,
                        'descripcion': me.descripcion
                    },configuracion).then(function(response){
                        me.close();
                        me.listar();
                        me.limpiar();                        
                    }).catch(function(error){
                        console.log(error);
                    });
                }
               }

                
            },
            reset () {
              this.$refs.form.reset()
            },
            resetValidation () {
              this.$refs.form.resetValidation()
            },
            activarDesactivarMostrar (accion,item) {
              this.adModal=1;
              this.adNombre=item.nombre;
              this.adId=item.idcategoria;
              if (accion==1) {
                this.adAccion=1;
              }
              else if (accion==2) {
                this.adAccion=2;
              }
              else{
                this.adModal=0;
              }
            },
            Activar () {
              let me=this;
              let header={"Authorization" : "Bearer " + this.$store.state.token};
              let configuracion={headers : header};
                    axios.post('api/Categorias/Activar/'+this.adId,{},configuracion).then(function(response){
                        me.adModal=0;
                        me.adAccion=0;
                        me.adNombre='';
                        me.adId=''; 
                        me.listar();                       
                    }).catch(function(error){
                        console.log(error);
                    });
            },
            Desactivar () {
              let me=this;
              let header={"Authorization" : "Bearer " + this.$store.state.token};
              let configuracion={headers : header};
                    axios.post('api/Categorias/Desactivar/'+this.adId,{},configuracion).then(function(response){
                        me.adModal=0;
                        me.adAccion=0;
                        me.adNombre='';
                        me.adId='';        
                        me.listar();                
                    }).catch(function(error){
                        console.log(error);
                    });
            },
            activarDesactivarCerrar() {
              this.adModal=0;
            }

          }
}
</script>