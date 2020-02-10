<template>
    <v-layout align-start>
        <v-flex>
            <v-data-table
      :headers="headers"
      :items="clientes"
      :search="search"
      class="elevation-1"
    >
        
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>Cliente</v-toolbar-title>
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
                      <v-text-field v-model="nombre" label="Nombre" :rules="validaNombre" :counter="50" required></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-select v-model="tipo_documento" label="Tipo Documento" :items="documentos" :rules="[v => !!v || 'Debe elegir un Documento!']" required></v-select>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field  v-model="num_documento" label="Número Documento" required></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="direccion" label="Dirección" required ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="telefono" label="Teléfono" required ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="email" label="Email"></v-text-field>
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
                    clientes:[],
                            dialog: false,
                            
                    headers: [                      
                    { text: 'Opciones', value: 'opciones', sortable: false },                    
                    { text: 'Nombre', value: 'nombre' },
                    { text: 'Tipo Persona', value: 'tipo_persona' },
                    { text: 'Tipo Documento', value: 'tipo_documento' },
                    { text: 'Número Documento', value: 'num_documento', sortable: false },
                    { text: 'Dirección', value: 'direccion', sortable: false },
                    { text: 'Teléfono', value: 'telefono', sortable: false },
                    { text: 'Email', value: 'email', sortable: false }
                    ],
                    search: '',
                    editedIndex: -1,                    
                    id: '',
                    nombre:'',
                    tipo_documento: '',
                    documentos: ['DNI','RUC','PASAPORTE','CEDULA'],
                    num_documento:'',
                    direccion: '',
                    telefono:'',
                    email:'',                    
                    valid: true,
                    validaNombre:[
                      v => !!v || 'Nombre es requerido',
                      v => v.length <= 50 || 'El nombre debe tener más de 3 caracteres y menos de 50',
                      v => v.length > 3 || 'El nombre debe tener más de 3 caracteres y menos de 50',
                    ],
                    adModal: 0,
                    adAccion: 0,
                    adNombre: '',
                    adId: ''
                }
        },
        computed: {
            formTitle () {
            return this.editedIndex === -1 ? 'Nuevo cliente' : 'Actualizar cliente'
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
                let configuracion={headers : header};
                axios.get('api/Personas/ListarClientes',configuracion).then(function(response){
                    //console.log(response);
                    me.clientes=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },

            editItem (item) {
              this.id = item.idpersona;      
              this.nombre = item.nombre;
              this.tipo_documento = item.tipo_documento;
              this.num_documento = item.num_documento;
              this.direccion = item.direccion;
              this.telefono = item.telefono;
              this.email = item.email;
              this.editedIndex=1;
              this.dialog = true
            },

            close () {

              this.dialog = false;
              this.limpiar();
              
            },
            limpiar (){
                this.id="";
                this.nombre="";
                this.tipo_documento="";
                this.num_documento="";
                this.direccion="";
                this.telefono="";
                this.email="";
                this.editedIndex=-1;
            },

            guardar () {
                  // Acá ira a validar y ver que todo esta bien, en caso de no lo termina acá
               // }
               let header={"Authorization" : "Bearer " + this.$store.state.token};
              let configuracion={headers : header};
              if (this.$refs.form.validate()){
                if (this.editedIndex > -1) {
                    //Código para editar UPDATE
                    // para editar se usa el put
                    let me=this;
                    
                    axios.put('api/Personas/Actualizar',{
                        'idpersona':me.id,
                        'tipo_persona':'Cliente',
                        'nombre':me.nombre,
                        'tipo_documento': me.tipo_documento,
                        'num_documento':me.num_documento,
                        'direccion':me.direccion,
                        'telefono': me.telefono,
                        'email' : me.email
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
                    let header={"Authorization" : "Bearer " + this.$store.state.token};
                    let configuracion={headers : header};
                    axios.post('api/Personas/Crear',{
                        'tipo_persona':'Cliente',
                        'nombre':me.nombre,
                        'tipo_documento': me.tipo_documento,
                        'num_documento':me.num_documento,
                        'direccion':me.direccion,
                        'telefono': me.telefono,
                        'email' : me.email
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
            }

          }
}
</script>