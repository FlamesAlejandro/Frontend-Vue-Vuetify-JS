<template>
    <v-layout align-start>
        <v-flex>
            <v-toolbar color="white">
                <v-toolbar-title>Consulta Ventas</v-toolbar-title>
                <v-divider class="mx-4" inset vertical></v-divider>
                <div class="flex-grow-1"></div>
                <v-spacer><v-spacer/>
                Desde:&nbsp;
                <v-text-field type="date" v-if="verNuevo==0" class="text-xs-center" v-model="fechaInicio" single-line hide-details></v-text-field>
                <v-btn v-if="verNuevo==0" @click="listar()" color="primary" dark class="mb-2">Buscar</v-btn>                  
                <v-dialog v-model="comprobanteModal" max-width="1000px">
                    <v-card>
                      <v-card-text>
                        <v-btn @click="crearPDF()"><v-icon>print</v-icon></v-btn>
                        <div id="factura">
                                    <header>
                                        <div id="logo">
                                            <img src="data:image/png;base64,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" id="imagen">
                                        </div>
                                        <div id="datos">
                                            <p id="encabezado">
                                                <b>Soluciones IT</b><br>Villagrán 1368, Los Ángeles - Octava Región, Chile<br>Teléfono:(+51)931742904<br>Email:jcarlos.ad7@gmail.com
                                            </p>
                                        </div>
                                        <div id="fact">
                                            <p>{{tipo_comprobante}}<br>
                                            {{serie_comprobante}}-{{num_comprobante}}<br>
                                            {{fecha_hora}}</p>
                                        </div>
                                    </header>
                                    <br>
                                    <section>
                                        <div>
                                            <table id="facliente">
                                                <tbody>
                                                    <tr>
                                                        <td id="cliente">
                                                            <strong>Sr(a). {{cliente}}</strong><br>
                                                            <strong>Documento:</strong> {{num_documento}}<br>
                                                            <strong>Dirección:</strong> {{direccion}}<br>
                                                            <strong>Teléfono:</strong> {{telefono}}<br>
                                                            <strong>Email:</strong> {{email}}
                                                        </td>
                                                    </tr>
                                                </tbody>
                                            </table>
                                        </div>
                                    </section>
                                    <br>
                                    <section>
                                        <div>
                                            <table id="facarticulo">
                                                <thead>
                                                    <tr id="fa">
                                                        <th>CANT</th>
                                                        <th>DESCRIPCION</th>
                                                        <th>PRECIO UNIT</th>
                                                        <th>DESC.</th>
                                                        <th>PRECIO TOTAL</th>
                                                    </tr>
                                                </thead>
                                                <tbody>
                                                    <tr v-for="det in detalles" :key="det.iddetalle_venta">
                                                        <td style="text-align:center;">{{det.cantidad}}</td>
                                                        <td>{{det.articulo}}</td>
                                                        <td style="text-align:right;">{{det.precio.toFixed(2)}}</td>
                                                        <td style="text-align:right;">{{det.descuento.toFixed(2)}}</td>
                                                        <td style="text-align:right;">{{(det.cantidad*det.precio-det.descuento).toFixed(2)}}</td>
                                                    </tr>
                                                </tbody>
                                                <tfoot>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">SUBTOTAL</th>
                                                        <th style="text-align:right;">$ {{totalParcial=(total-totalImpuesto).toFixed(2)}}</th>
                                                    </tr>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">IGV ({{impuesto}}%)</th>
                                                        <th style="text-align:right;">$ {{totalImpuesto=((total*impuesto)/(100+impuesto)).toFixed(2)}}</th>
                                                    </tr>
                                                    <tr>
                                                        <th></th>
                                                        <th></th>
                                                        <th></th>
                                                        <th style="text-align:right;">TOTAL</th>
                                                        <th style="text-align:right;">$ {{total=(calcularTotal).toFixed(2)}}</th>
                                                    </tr>
                                                </tfoot>
                                            </table>
                                        </div>
                                    </section>
                                    <br>
                                    <br>
                                    <footer>
                                        <div id="gracias">
                                            <p><b>Gracias por su compra!</b></p>
                                        </div>
                                    </footer>
                                </div>
                        <v-btn @click="ocultarComprobante()" color="blue darken-1" flat>Cancelar</v-btn>
                    </v-card-text>
                    </v-card>
                </v-dialog>
                </v-toolbar>              
        <v-data-table :headers="headers" :items="ventas"  class="elevation-1" v-if="verNuevo==0">         
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
                            <v-icon
                                small
                                class="mr-2"
                                @click="mostrarComprobante(item)"
                            >
                                print
                            </v-icon>                            
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
    import jsPDF from 'jspdf'
    import html2canvas from 'html2canvas';
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
                    comprobanteModal:0,
                    adAccion: 0,
                    adNombre: '',
                    cliente:'',
                    fecha_hora:'',
                    num_documento:'',
                    direccion:'',
                    telefono:'',
                    email:'',
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
            crearPDF(){
                var quotes = document.getElementById('factura');
                html2canvas(quotes).then(function (canvas) {
                    var imgData = canvas.toDataURL('image/png');
                    var imgWidth = 210;
                    var pageHeight = 295;
                    var imgHeight = canvas.height * imgWidth / canvas.width;
                    var heightLeft = imgHeight;
                    var doc = new jsPDF();
                    var position = 0;

                    doc.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);                    
                    doc.save('ComprobanteVenta.pdf');
                });
            },
            mostrarComprobante(item){
                this.limpiar();
                this.tipo_comprobante=item.tipo_comprobante;
                this.serie_comprobante=item.serie_comprobante;
                this.num_comprobante=item.num_comprobante;
                this.cliente=item.cliente;
                this.num_documento=item.num_documento;
                this.direccion=item.direccion;
                this.telefono=item.telefono;
                this.email=item.email;
                this.fecha_hora=item.fecha_hora;
                this.impuesto=item.impuesto;
                this.listarDetalles(item.idventa);
                this.comprobanteModal=1;
            },
            ocultarComprobante(){
                this.comprobanteModal=0;
                this.limpiar();
            },
            mostrarNuevo(){
              this.verNuevo=1;
              this.limpiar();
            },
            ocultarNuevo(){
              this.verNuevo=0;
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
            reset () {
                this.$refs.form.reset()
            },
            resetValidation () {
                this.$refs.form.resetValidation()
            }

          }
}
</script>
<style>
	    #factura {
            padding: 20px;
            font-family: Arial, sans-serif;
            font-size: 16px ;
        }

        #logo {
            float: left;
            margin-left: 2%;
            margin-right: 2%;
        }
        #imagen {
            width: 100px;
        }

        #fact {
            font-size: 18px;
            font-weight: bold;
            text-align: center;
        }   

        #datos {
            float: left;
            margin-top: 0%;
            margin-left: 2%;
            margin-right: 2%;
            /*text-align: justify;*/
        }

        #encabezado {
            text-align: center;
            margin-left: 10px;
            margin-right: 10px;
            font-size: 16px;
        }

        section {
            clear: left;
        }

        #cliente {
            text-align: left;
        }

        #facliente {
            width: 40%;
            border-collapse: collapse;
            border-spacing: 0;
            margin-bottom: 15px;
        }

        #fa {
            color: #FFFFFF;
            font-size: 14px;
        }

        #facarticulo {
            width: 100%;
            border-collapse: collapse;
            border-spacing: 0;
            padding: 20px;
            margin-bottom: 15px;
        }

        #facarticulo thead {
            padding: 20px;
            background: #2183E3;
            text-align: center;
            border-bottom: 1px solid #FFFFFF;
        }

        #gracias {
            text-align: center;
        }
</style>