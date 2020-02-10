  <template>
    <v-app id="inspire">
      <v-navigation-drawer
        v-model="drawer"
        :clipped="$vuetify.breakpoint.lgAndUp"
        app
        v-if="logueado"
      >
        <v-list dense>
          <template v-if="esAdministrador || esAlmacenero || esVendedor">
            <v-list-item :to="{name: 'home'}">
              <v-list-item-action>
                <v-icon>home</v-icon>
              </v-list-item-action>
              <v-list-item-title>
                Inicio
              </v-list-item-title>
            </v-list-item>
          </template>
          <template v-if="esAdministrador || esAlmacenero">
            <v-list-group>
              <v-list-item slot="activator">
                <v-list-item-content>
                  <v-list-item-title>
                    Almacén
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'categorias'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Categorías
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'articulos'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Artículos
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>

            </v-list-group>
          </template>
          
          <template v-if="esAdministrador || esAlmacenero">
            <v-list-group>
              <v-list-item slot="activator">
                <v-list-item-content>
                  <v-list-item-title>
                    Compras
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'ingresos'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Ingresos
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'proveedores'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Proveedores
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>

            </v-list-group>
          </template>

          <template v-if="esAdministrador || esVendedor">
            <v-list-group>
              <v-list-item slot="activator">
                <v-list-item-content>
                  <v-list-item-title>
                    Ventas
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'ventas'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Ventas
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'clientes'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Clientes
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-group>
          </template>
          
          <template v-if="esAdministrador">
            <v-list-group>
              <v-list-item slot="activator">
                <v-list-item-content>
                  <v-list-item-title>
                    Accesos
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'roles'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Roles
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'usuarios'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Usuarios
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-group>
          </template>

          <template v-if="esAdministrador">
            <v-list-group>
              <v-list-item slot="activator">
                <v-list-item-content>
                  <v-list-item-title>
                    Consultas
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: ''}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Consultas compras
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item :to="{ name: 'consultaventas'}">
                <v-list-item-action>
                  <v-icon>table_chart</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>
                    Consultas ventas
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>

            </v-list-group>
          </template>
        </v-list>
      </v-navigation-drawer>

      <v-app-bar
        :clipped-left="$vuetify.breakpoint.lgAndUp"
        app
        color="blue darken-3"
        dark
      >
        <v-toolbar-title
          style="width: 300px"
          class="ml-0 pl-4"
        >
          <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
          <span class="hidden-sm-and-down">Sistema</span>
        </v-toolbar-title>
        
        <div class="flex-grow-1"></div>
        <v-btn @click="salir " v-if="logueado" icon>
          <v-icon>logout</v-icon> Salir
        </v-btn>
        <v-btn :to="{name: 'login'}" v-else>
          <v-icon>apps</v-icon> Login
        </v-btn>
        
      </v-app-bar>
      <v-content>
        <v-container class="fill-height" fluid>
            <v-slide-y-transition mode="out-in">
              <router-view/>
            </v-slide-y-transition>
        </v-container>
      </v-content>

      <v-footer dark height="auto">
        <v-layout justify-center>
          <v-flex text-xs-center>
            <v-card flat item color="primary" class="white--text">
              <v-card-text class="white--text pt-0">
                IncanatoIT &copy;2018
              </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
      </v-footer>

    </v-app>
  </template>


  <script>

  export default {
    name: 'App',
    data: () => ({
      //
      drawer: null,
    }),
  computed: {
    logueado(){
      return this.$store.state.usuario;
    },
    esAdministrador(){
      return this.$store.state.usuario && this.$store.state.usuario.rol =='Administrador';
    },
    esAlmacenero(){
      return this.$store.state.usuario && this.$store.state.usuario.rol =='Almacenero';
    },
    esVendedor(){
      return this.$store.state.usuario && this.$store.state.usuario.rol =='Vendedor';
    }
  },
  created(){
    this.$store.dispatch("autoLogin");
  },
  methods:{
    salir(){
      this.$store.dispatch("salir");
    }
  }
}
</script>
