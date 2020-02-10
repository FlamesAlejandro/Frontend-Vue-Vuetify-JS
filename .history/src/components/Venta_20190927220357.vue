<template>
    <v-layout align-start>
        <v-flex>
              <v-toolbar color="white">
                <v-toolbar-title>Ventas</v-toolbar-title>
                  <v-divider class="mx-4" inset vertical></v-divider>
                  <div class="flex-grow-1"></div>
                  <v-text-field v-if="verNuevo==0" class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
                  <v-btn v-if="verNuevo==0" @click="listar()" color="primary" dark class="mb-2">Buscar</v-btn>
                  <v-spacer></v-spacer>
                  <v-btn v-if="verNuevo==0" @click="mostrarNuevo" color="primary" dark class="mb-2">Nuevo</v-btn>     
                  <v-dialog v-model="verArticulos" max-width="1000px">
                        <v-card>
                            <v-card-title>
                                <span class="headline">Seleccione un artículo</span>
                            </v-card-title>
                            <v-card-text>
                                <v-container grid-list-md>
                                    <v-layout wrap>
                                        <v-flex xs12 sm12 md12 lg12 xl12>
                                            <v-text-field append-icon="search" 
                                            class="text-xs-center" v-model="texto"
                                            label="Ingrese artículo a buscar" @keyup.enter="listarArticulo()">

                                            </v-text-field>
                                            <template>
                                              <v-data-table
                                                    :headers="cabeceraArticulos"
                                                    :items="articulos"
                                                    class="elevation-1"
                                                >
                                                    <template v-slot:item.seleccionar="{ item }">
                                                      <v-icon small class="mr-2" @click="agregarDetalle(item)">
                                                        add
                                                      </v-icon>
                                                    </template>
                                                    <template v-slot:item.articulo="{ item }">
                                                        <v-text-field v-model="item.nombre" readonly flat></v-text-field>
                                                    </template>
                                                  <template slot="no-data">
                                                      <h3>No hay artículos para mostrar.</h3>
                                                  </template>
                                              </v-data-table> 
                                          </template>
                                      </v-flex>
                                  </v-layout>
                              </v-container>
                          </v-card-text>
                          <v-card-actions>
                              <v-spacer></v-spacer>
                              <v-btn @click="ocultarArticulos()" color="blue darken" text>
                                  Cancelar
                              </v-btn>
                          </v-card-actions>
                      </v-card>
                  </v-dialog>           
                  <v-dialog v-model="adModal" max-width="300">
                    <v-card-title class="headline" v-if="adAccion==1">¿Activar Item?</v-card-title>
                    <v-card-title class="headline" v-if="adAccion==2">¿Anular Venta?</v-card-title>
                    <v-card-text>
                      Estás a punto de
                      <span v-if="adAccion==1">Activar</span>
                      <span v-if="adAccion==2">Anular</span>
                      el ítem {{adNombre}}
                    </v-card-text>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="green darken-1" @click="activarDesactivarCerrar">
                        Cancelar
                      </v-btn>
                      <v-btn v-if="adAccion==1" color="orange darken-4" @click="Activar">
                        Activar
                      </v-btn>
                      <v-btn v-if="adAccion==2" color="orange darken-4" @click="Desactivar">
                        Desactivar
                      </v-btn>
                    </v-card-actions>
                  </v-dialog>
                  <v-dialog>
                    <v-card>
                      <v-card-text>
                        <v-btn><v-icon>print</v-icon></v-btn>
                        <div id="factura"></div>
                      </v-card-text>
                    </v-card>
                  </v-dialog>
                </v-toolbar>
              
          <v-data-table :headers="headers" :items="ventas" :search="search" class="elevation-1" v-if="verNuevo==0">         
            <template v-slot:top>
            </template>
              <template v-slot:item.estado="{ item }">
                <!-- Para crear condicionales dentro de un datatable, hay que tratar a cada item con esta pestaña de template, y buscar el metodo, en este caso un v-if y v-else. Y mostrar de acuerdo al resultado -->
                  <div v-if="item.estado=='Aceptado'">
                    <span class="blue--text">Activo</span>
                  </div>
                  <div v-else>
                    <span class="red--text">{{item.estado}}</span>
                  </div>
              </template>     
              <template v-slot:no-data>
                <v-btn color="primary" @click="listar">Reset</v-btn>
            </template>
            <template v-slot:item.opciones="{ item }">
                            <v-icon
                                small
                                class="mr-2"
                                @click="verDetalles(item)"
                              >
                                tab
                              </v-icon>
                              <template v-if="item.estado=='Aceptado'">
                              <v-icon
                                small
                                @click="activarDesactivarMostrar(2,item)"
                              >
                                block
                            </v-icon>
                              </template>
              </template>
          </v-data-table>
    <v-container grid-list-sm class="pa-4 white" v-if="verNuevo">
      <v-layout row wrap>
        <v-flex xs12 sm4 md4 lg4 xl4>        
          <v-select v-model="tipo_comprobante" :items="comprobantes" label="Tipo Comprobante">
            </v-select>  
        </v-flex>
        <v-flex xs12 sm4 md4 lg4 xl4>    
          <v-text-field v-model="serie_comprobante" label="Serie Comprobante"></v-text-field>      
        </v-flex>
        <v-flex xs12 sm4 md4 lg4 xl4>          
          <v-text-field v-model="num_comprobante" label="Número Comprobante"></v-text-field>  
        </v-flex>
        <v-flex xs12 sm8 md8 lg8 xl8>
          <v-select v-model="idcliente" :items="clientes" label="Cliente">
            </v-select>          
        </v-flex>
        <v-flex xs12 sm4 md4 lg4 xl4> 
          <v-text-field v-model="impuesto" type="number" label="Impuesto"></v-text-field>  
        </v-flex>
        <v-flex xs12 sm8 md8 lg8 xl8> 
          <v-text-field @keyup.enter="buscarCodigo()" v-model="codigo" label="Código"></v-text-field>  
        </v-flex>
        <v-flex xs12 sm2 md2 lg2 xl2> 
          <v-btn @click="mostrarArticulos()" small fab dark color="teal">
            <v-icon dark>list</v-icon>
          </v-btn>          
        </v-flex>
        <v-flex xs12 sm2 md2 lg2 xl2> 
          <div class="red--text" v-text="errorArticulo">>
            
          </div>
        </v-flex>
        <v-flex xs12 sm12 md12 lg12 xl12> 
          <v-data-table :headers="cabeceraDetalles" :items="detalles" hide-default-footer class="elevation-1">
            <template slot="items">
              <td class="justify-center layout px-0">
                <v-icon small class="mr-2" @click="eliminarDetalle(detalles,item)">
                  delete
                </v-icon>
              </td>
            </template>
            <template v-slot:item.subtotal="{ item }">
                <td>$ {{item.cantidad * item.precio}}</td>
              </template>
              <template v-slot:item.cantidad="{ item }">
                  <v-text-field type="number" v-model="item.cantidad"></v-text-field>
              </template>      
              <template v-slot:item.precio="{ item }">
                  <v-text-field type="number" v-model="item.precio"></v-text-field>
              </template>
              <template v-slot:item.descuento="{ item }">
                  <v-text-field type="number" v-model="item.descuento"></v-text-field>
              </template>
            <template slot="no-data">
              <h3>No hay artículos agregados al detalle.</h3>
            </template>
          </v-data-table>
          <v-flex class="text-xs-right">
            <strong>Total Parcial: </strong>$ {{totalParcial=(total-totalImpuesto).toFixed(2)}}
          </v-flex>
          <v-flex class="text-xs-right">
            <strong>Total Impuesto: </strong>$ {{totalImpuesto=((total*impuesto)/(100+impuesto)).toFixed(2)}}
          </v-flex>
          <v-flex class="text-xs-right">
            <strong>Total Neto: </strong>$ {{total=(calcularTotal).toFixed(2)}}
          </v-flex>
        </v-flex>
        <v-flex xs12 sm12 md12 lg12 xl12>
          <div class="red--text" v-for="v in validaMensaje" :key="v" v-text="v"></div>
        </v-flex>
        <v-flex xs12 sm12 md12 lg12 xl12>
          <v-btn @click="ocultarNuevo()" color="blue darken-1">Cancelar</v-btn>
          <v-btn v-if="verDet==0" @click="guardar()" color="success">Guardar</v-btn>
        </v-flex>
      </v-layout>
    </v-container>
        </v-flex>
    </v-layout>
</template>

<script>
    import axios from 'axios'
    export default {
        data(){
                return {
                    ventas:[],
                    dialog: false,                            
                    headers: [
                    { text: 'Opciones', value: 'opciones', sortable: false },
                    { text: 'Usuario', value: 'usuario' },
                    { text: 'Cliente', value: 'cliente' },
                    { text: 'Tipo Comprobante', value: 'tipo_comprobante' },
                    { text: 'Serie Comprobante', value: 'serie_comprobante', sortable: false  },
                    { text: 'Número Comprobante', value: 'num_comprobante', sortable: false  },
                    { text: 'Fecha', value: 'fecha_hora', sortable: false  },
                    { text: 'Impuesto', value: 'impuesto', sortable: false  },
                    { text: 'Total', value: 'total', sortable: false  },
                    { text: 'Estado', value: 'estado', sortable: false  }                
                ],
                    cabeceraDetalles: [
                    { text: 'Borrar', value: 'borrar', sortable: false },
                    { text: 'Artículo', value: 'articulo', sortable: false },
                    { text: 'Cantidad', value: 'cantidad', sortable: false  },
                    { text: 'Precio', value: 'precio', sortable: false  },
                    { text: 'Descuento', value: 'descuento', sortable: false  },
                    { text: 'Subtotal', value: 'subtotal', sortable: false  }                
                ],
                    cabeceraArticulos: [
                    {text: 'Seleccionar', value: 'seleccionar', sortable: false },
                    { text: 'Artículo', value: 'articulo'},
                    { text: 'Categoría', value: 'categoria' },
                    { text: 'Descripción', value: 'descripcion', sortable: false },
                    { text: 'Stock', value: 'stock', sortable: false  },
                    { text: 'Precio Venta', value: 'precio_venta', sortable: false  }            
                    ],
                    detalles:[                    
                    ],
                    search: '',
                    verArticulos:0,                 
                    id: '',
                    idcliente: '',
                    clientes: [],
                    tipo_comprobante:'',
                    comprobantes: ['FACTURA','BOLETA','TICKET','GUIA'],
                    serie_comprobante:'',
                    num_comprobante:'',
                    impuesto:18,
                    codigo:'',
                    verNuevo:0,
                    errorArticulo:null,
                    totalParcial:0,
                    totalImpuesto:0,
                    total:0,
                    articulos:[],
                    texto:'',
                    verDet:0,
                    valida:0,
                    validaMensaje:[],
                    adModal: 0,
                    adAccion: 0,
                    adNombre: '',
                    adId: ''
                }
        },
        computed: {
            calcularTotal:function(){
                var resultado=0.0;
                for(var i=0;i<this.detalles.length;i++){
                    resultado=resultado+(this.detalles[i].precio*this.detalles[i].cantidad-this.detalles[i].descuento);
                }
                return resultado;
            }
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
        //// TRABAJANDO EN ESTE
        methods:{
            mostrarNuevo(){
              this.verNuevo=1;
              this.limpiar();
            },
            ocultarNuevo(){
              this.verNuevo=0;
            },
            buscarCodigo(){
                let me=this;
                me.errorArticulo=null;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Articulos/BuscarCodigoVenta/'+this.codigo,configuracion)
                .then(function(response){
                    //console.log(response);
                    me.agregarDetalle(response.data);
                }).catch(function(error){
                    console.log(error);
                    me.errorArticulo='No existe el artículo';
                });
            },
            listar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion={headers : header};
                let url='';
                if (!me.search){
                  url='api/Ventas/Listar';
                }
                else{
                  url='api/Ventas/ListarFiltro/'+me.search;
                }
                axios.get(url,configuracion).then(function(response){
                    //console.log(response);
                    me.ventas=response.data;
                }).catch(function(error){
                    console.log(error);
                    
                });
            },
            mostrarArticulos(){
                this.verArticulos=1;
            },
            ocultarArticulos(){
                this.verArticulos=0;
            },
            listarArticulo(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Articulos/ListarVenta/'+me.texto,configuracion).then(function(response){
                    //console.log(response);
                    me.articulos=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },
            agregarDetalle(data = []){
                this.errorArticulo=null;
                if (this.encuentra(data['idarticulo'])){
                    this.errorArticulo="El artículo ya ha sido agregado."
                }
                else{
                    this.detalles.push(
                        {idarticulo:data['idarticulo'],
                        articulo: data['nombre'],
                        cantidad:1,
                        precio:data['precio_venta'],
                        descuento:0}
                    );
                }                
            },
            verDetalles(item){
                this.limpiar();
                this.tipo_comprobante=item.tipo_comprobante;
                this.serie_comprobante=item.serie_comprobante;
                this.num_comprobante=item.num_comprobante;
                this.idcliente=item.idcliente;
                this.impuesto=item.impuesto;
                this.listarDetalles(item.idventa);
                this.verNuevo=1;
                this.verDet=1;
            },
            listarDetalles(id){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Ventas/ListarDetalles/'+id,configuracion).then(function(response){
                    //console.log(response);
                    me.detalles=response.data;
                }).catch(function(error){
                    console.log(error);
                });
            },
            eliminarDetalle(arr,item){
                var i= arr.indexOf(item);
                if (i!==-1){
                    arr.splice(i,1);
                }
            },

            select(){
                let me=this;
                var clientesArray=[];
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.get('api/Personas/SelectClientes',configuracion).then(function(response){
                    clientesArray=response.data;
                    clientesArray.map(function(x){
                        me.clientes.push({text: x.nombre,value:x.idpersona});
                    });
                }).catch(function(error){
                    console.log(error);
                });
            },
            encuentra(id){
                var sw=0;
                for(var i=0;i<this.detalles.length;i++){
                    if (this.detalles[i].idarticulo==id){
                        sw=1;
                    }
                }
                return sw;
            },
            limpiar(){
                this.id="";
                this.idproveedor="";
                this.tipo_comprobante="";
                this.serie_comprobante="";
                this.num_comprobante="";
                this.impuesto="18";
                this.codigo="";
                this.detalles=[];
                this.total=0;
                this.totalImpuesto=0;
                this.totalParcial=0;
                this.verDet=0;
            },
            guardar () {
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};                
                let me=this;
                axios.post('api/Ventas/Crear',{
                    'idcliente':me.idcliente,
                    'idusuario':me.$store.state.usuario.idusuario,
                    'tipo_comprobante': me.tipo_comprobante,
                    'serie_comprobante': me.serie_comprobante,
                    'num_comprobante':me.num_comprobante,
                    'impuesto': me.impuesto,
                    'total':me.total,
                    'detalles':me.detalles
                },configuracion).then(function(response){
                    me.ocultarNuevo();
                    me.listar();
                    me.limpiar();                        
                }).catch(function(error){
                    console.log(error);
                });
            },
            validar(){
                this.valida=0;
                this.validaMensaje=[];

                if (!this.idproveedor){
                    this.validaMensaje.push("Seleccione un proveedor.");
                }
                if (!this.tipo_comprobante){
                    this.validaMensaje.push("Seleccione un tipo de comprobante.");
                }
                if (!this.num_comprobante){
                    this.validaMensaje.push("Ingrese el número del comprobante.");
                }
                if (!this.impuesto || this.impuesto<0){
                    this.validaMensaje.push("Ingrese un impuesto válido.");
                }
                if (this.detalles.length<=0){
                    this.validaMensaje.push("Ingrese al menos un artículo al detalle.");
                }
                if (this.validaMensaje.length){
                    this.valida=1;
                }
                return this.valida;
            },
            activarDesactivarMostrar(accion,item){
                this.adModal=1;
                this.adNombre=item.num_comprobante;
                this.adId=item.idventa;                
                if (accion==1){
                    this.adAccion=1;
                }
                else if (accion==2){
                    this.adAccion=2;
                }
                else{
                    this.adModal=0;
                }
            },
            /*
            Activar () {
              let me=this;
              let header={"Authorization" : "Bearer " + this.$store.state.token};
              let configuracion={headers : header};
                    axios.post('api/Usuarios/Activar/'+this.adId,{},configuracion).then(function(response){
                        me.adModal=0;
                        me.adAccion=0;
                        me.adNombre='';
                        me.adId=''; 
                        me.listar();                       
                    }).catch(function(error){
                        console.log(error);
                    });
            },
            */
            desactivar(){
                let me=this;
                let header={"Authorization" : "Bearer " + this.$store.state.token};
                let configuracion= {headers : header};
                axios.put('api/Ventas/Anular/'+this.adId,{},configuracion).then(function(response){
                    me.adModal=0;
                    me.adAccion=0;
                    me.adNombre="";
                    me.adId="";
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