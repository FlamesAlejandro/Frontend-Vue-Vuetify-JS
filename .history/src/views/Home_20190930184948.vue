<template>
  <HelloWorld />
</template>

<script>
import axios from 'axios'
import Chart from 'chart.js'
import HelloWorld from '../components/HelloWorld';

export default {
  data(){

    return {
      // etiquetas y valores
      mesesValores:null,
      // etiquetas
      nombreMeses:[],
      // total de cada mes
      totalMeses:[]
    }
  },
  methods:{
    loadProductosMasVendidos(){
      let me = this;
      let mesn='';
      me.mesesValores.map(function(x){
        switch(parseInt(x.etiqueta)){
          case 1:
            mesn='Enero'
        }
        me.nombreMeses.push(x.etiqueta);
        me.totalMeses.push(x.valor);
      });
      var ctx = document.getElementById('myChart');
      var myChart = new Chart(ctx, {
          type: 'bar',
          data: {
              labels: me.nombreMeses,
              datasets: [{
                  label: 'Ventas en los últimos 12 Meses',
                  data: me.totalMeses,
                  backgroundColor: [
                      'rgba(255, 99, 132, 0.2)',
                      'rgba(54, 162, 235, 0.2)',
                      'rgba(255, 206, 86, 0.2)',
                      'rgba(75, 192, 192, 0.2)',
                      'rgba(153, 102, 255, 0.2)',
                      'rgba(255, 159, 64, 0.2)',
                      'rgba(255, 99, 132, 0.2)',
                      'rgba(54, 162, 235, 0.2)',
                      'rgba(255, 206, 86, 0.2)',
                      'rgba(75, 192, 192, 0.2)',
                      'rgba(153, 102, 255, 0.2)',
                      'rgba(255, 159, 64, 0.2)'
                  ],
                  borderColor: [
                      'rgba(255, 99, 132, 1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',
                      'rgba(75, 192, 192, 1)',
                      'rgba(153, 102, 255, 1)',
                      'rgba(255, 159, 64, 1)',
                      'rgba(255, 99, 132, 1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',
                      'rgba(75, 192, 192, 1)',
                      'rgba(153, 102, 255, 1)',
                      'rgba(255, 159, 64, 1)'
                  ],
                  borderWidth: 1
              }]
          },
          options: {
              scales: {
                  yAxes: [{
                      ticks: {
                          beginAtZero: true
                      }
                  }]
              }
          }
      });
    },
    getProductosMasVendidos(){
      let me=this;
      let header={"Authorization" : "Bearer " + this.$store.state.token};
      let configuracion= {headers : header};
      axios.get('api/Ventas/VentasMes12',configuracion).then(function(response){
          //console.log(response);
          me.mesesValores=response.data;
          me.loadProductosMasVendidos();
      }).catch(function(error){
          console.log(error);
      });
    }
  },
  mounted(){
    this.getProductosMasVendidos();
  },
  components: {
    HelloWorld,
  },
};
</script>
