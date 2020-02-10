<template>
    <v-layout align-start>
        <v-flex>
            <v-data-table
      :headers="headers"
      :items="articulos"
      :search="search"
      class="elevation-1"
    >
        
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-btn @click="crearPDF"><v-icon>print</v-icon></v-btn>
          <v-toolbar-title>Artículos</v-toolbar-title>
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
                    <v-col cols="6" sm="6" md="6">
                      <v-text-field v-model="codigo" label="Código" ></v-text-field>
                    </v-col>
                    <v-col cols="6" sm="6" md="6">
                      <v-select v-model="idcategoria" label="Categoría" :items="categorias" :rules="[v => !!v || 'Debe elegir una categoría!']" ></v-select>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="nombre" label="Nombre" :rules="validaNombre" :counter="50"></v-text-field>
                    </v-col>
                    <v-col cols="6" sm="6" md="6">
                      <v-text-field type="number" v-model="stock" label="Stock" :rules="validaStock"></v-text-field>
                    </v-col>
                    <v-col cols="6" sm="6" md="6">
                      <v-text-field type="number" v-model="precio_venta" label="Precio Venta" :rules="validaPrecioVenta" ></v-text-field>
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
    import jsPDF from 'jspdf'
    import autoTable from 'jspdf-autotable'
    export default {
        data(){
                return {
                    articulos:[],
                            dialog: false,
                            
                    headers: [                      
                    { text: 'Opciones', value: 'opciones', sortable: false },
                    { text: 'Código', value: 'codigo', sortable: false },                    
                    { text: 'Nombre', value: 'nombre' },
                    { text: 'Categoría', value: 'categoria' },
                    { text: 'Stock', value: 'stock', sortable: false },
                    { text: 'Precio Venta', value: 'precio_venta', sortable: false },
                    { text: 'Descripción', value: 'descripcion', sortable: false },
                    { text: 'Estado', value: 'condicion', sortable: false }
                    ],
                    search: '',
                    editedIndex: -1,                    
                    id: '',
                    idcategoria: '',
                    categorias: [],
                    codigo: '',
                    nombre: '',
                    stock: 0,
                    precio_venta: 0,
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
                    validaStock: [
                      v => v > 0 || 'Debe ser mayor que 0'
                    ],
                    validaPrecioVenta: [
                      v => v > 0 || 'Debe ser mayor que 0'
                    ],
                    adModal: 0,
                    adAccion: 0,
                    adNombre: '',
                    adId: ''
                }
        },
        computed: {
            formTitle () {
            return this.editedIndex === -1 ? 'Nuevo artículo' : 'Actualizar artículo'
            },
        },

        watch: {
            dialog (val) {
            val || this.close()
            },
        },

        created () {
            this.listar();
            this.select();
        },

        methods:{
            crearPDF(){
              var columns = [
                    {title: "Nombre", dataKey: "nombre"},
                    {title: "Código", dataKey: "codigo"}, 
                    {title: "Categoría", dataKey: "categoria"}, 
                    {title: "Stock", dataKey: "stock"},
                    {title: "Precio Venta", dataKey: "precio_venta"}
                ];
                var rows = [];

                this.articulos.map(function(x){
                    rows.push({nombre:x.nombre,codigo:x.codigo,categoria:x.categoria,stock:x.stock,precio_venta:x.precio_venta});
                });

                // Only pt supported (not mm or in)
                var doc = new jsPDF('p', 'pt');
                doc.autoTable(columns, rows, {
                    margin: {top: 60},
                    addPageContent: function(data) {
                        doc.text("Listado de Artículos", 40, 30);
                    }
                });
                doc.save('Articulos.pdf');

            },
            listar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Articulos/Listar',configuracion).then(function(response){
                    //console.log(response);
                    me.articulos=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },

            select(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion={headers : header};
                var categoriasArray=[];
                axios.get('api/Categorias/Select',configuracion).then(function(response){
                    //console.log(response);
                    categoriasArray=response.data;
                    // .map para recorrer el arreglo, y push para agregar. Y lo deja en el formato para el select
                    categoriasArray.map(function(x){
                      me.categorias.push({text: x.nombre, value:x.idcategoria});
                    });
                }).catch(function(error){
                    console.log(error);
                });
            },

            editItem (item) {
              this.id = item.idarticulo;
              this.idcategoria = item.idcategoria;
              this.codigo = item.codigo;
              this.nombre = item.nombre;
              this.stock = item.stock;
              this.precio_venta = item.precio_venta;
              this.descripcion = item.descripcion;
              this.editedIndex=1;
              this.dialog = true
            },

            close () {

              this.dialog = false;
              this.limpiar();
              
            },
            limpiar (){
                this.id="";
                this.idcategoria="",
                this.codigo="",
                this.nombre="";
                this.stock="",
                this.precio_venta="",
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
                    axios.put('api/Articulos/Actualizar',{
                        'idarticulo':me.id,
                        'idcategoria':me.idcategoria,
                        'codigo':me.codigo,
                        'nombre': me.nombre,
                        'stock':me.stock,
                        'precio_venta':me.precio_venta,
                        'descripcion': me.descripcion
                    }, configuracion).then(function(response){
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
                    axios.post('api/Articulos/Crear',{
                        'idcategoria':me.idcategoria,
                        'codigo':me.codigo,
                        'nombre': me.nombre,
                        'stock':me.stock,
                        'precio_venta':me.precio_venta,
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
            activarDesactivarMostrar (accion,item) {
              this.adModal=1;
              this.adNombre=item.nombre;
              this.adId=item.idarticulo;
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
                    axios.post('api/Articulos/Activar/'+this.adId,{},configuracion).then(function(response){
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
                    axios.post('api/Articulos/Desactivar/'+this.adId,{},configuracion).then(function(response){
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