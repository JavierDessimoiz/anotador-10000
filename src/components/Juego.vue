<template>
  <div class="container-fluid">
    <Header titulo="Anotador 10.000" />
  
    <!-- Comienzo -->
    <div v-if="comienzaElJuego === false">
     <CrearJugador @agregaJugador="agregaJugador" />
      <b-button
        type="button"
        class="btn btn-default"
        variant="success"
        v-on:click="comienzaElJuego = true"
      >
        Comenzar
      </b-button>
    </div>

    <div v-if="this.finDelJuego">
      <b-alert show>Fin del juego</b-alert>
    </div>
    <div class="container-fluid">
    <div class="row" >
      <div class="col" style="padding: 0" v-for="(jugador, index) in jugadores" :key="index">
        <div>
          <div v-if="comienzaElJuego === false">
            <div class="col" style="border-bottom: 1px solid blue; border-left: 1px solid blue; border-top: 1px solid blue;"> 
             <b-button >
              <b-icon icon="trash" aria-hidden="true"></b-icon>
            </b-button>
             {{ jugador.nombre }}
             </div>    
            
          </div>
          <div v-else>
            <div class="col" style="border-bottom: 1px solid blue; border-left: 1px solid blue; border-top: 1px solid blue;"> 
             {{ jugador.nombre }}
             </div>    

          </div>
        </div>
        <div>
        <div v-if="comienzaElJuego">
        <h6  style="text-align: center background-color: lightgrey;">
          {{
            jugador.puntos
          }}
        </h6>
        
        </div>
        <div v-if="finDelJuego === false">
          <jugador
            v-bind:jugador="jugador"
            @controlarPuntaje="controlarPuntaje(jugador, index)"
          />
        </div>

        <ul id="Puntos">
          <div class="row" 
            v-for="(LineaPunto,
            index) in jugador.puntosParcilales.slice().reverse()"
            :key="index"
          >
          <li style="text-align: center;">
            {{
              LineaPunto
            }}
          </li> 
          </div>
        </ul>
      </div>
    </div>
    </div>
  </div>
  </div>
</template>
<script>
import CrearJugador from "./CrearJugador";
import Jugador from "./Jugador";
import Header from "./Header";

export default {
  name: "Juego",
  methods: {
    agregaJugador: function(jugadorInicializado) {
      const existe =
        this.jugadores.filter(
          jugador => jugador.nombre == jugadorInicializado.nombre
        ).length > 0;
      if (!existe) {
        const newJug = {
          nombre: jugadorInicializado.nombre,
          puntos: jugadorInicializado.puntos,
          puntosParcilales: []
        };
        this.jugadores.push(newJug);
      } else {
        this.makeToast(
          "danger",
          "Error",
          jugadorInicializado.nombre + " ya ha sido agregado"
        );
      }
    },
    controlarPuntaje: function(jugador, index) {
      //se verifica el ingreso minimo al juego con 750 puntos

      if (
        this.jugadores[index].puntosParcilales.length === 0 &&
        jugador.puntos < Number(750)
      ) {
        jugador.puntos = 0;
        this.makeToast(
          "danger",
          "Error",
          this.jugadores[index].nombre +
            " no ingresó, debe ingresar al menos con 750 puntos."
        );
      } else {
        //this.alertaDeIngreso = -1;
        if (jugador.puntos > Number(10000)) {
          this.makeToast(
            "danger",
            "Error",
            "La suma de puntos a " +
              this.jugadores[index].nombre +
              " supera los 10000 puntos, no se sumará. "
          );
          //corrige el puntaje del jugador
          if (this.jugadores[index].puntosParcilales.length === 0) {
            jugador.puntos = 0;
          } else
            jugador.puntos = this.jugadores[index].puntosParcilales[
              this.jugadores[index].puntosParcilales.length - 1
            ];
        } else {
          //agregar en parciales
          this.jugadores[index].puntosParcilales.push(jugador.puntos);
          //controla el ganador
          if (jugador.puntos === Number(10000)) {
            this.finDelJuego = true;
          }
        }
      }
    },
    makeToast(variant = null, titulo, mensaje) {
      this.$bvToast.toast(mensaje, {
        title: titulo,
        variant: variant,
        solid: true
      });
    }
  },
  props: ["titulo"],
  data: function() {
    return {
      //array de jugadores
      jugadores: [],
      cantidadDejugadores: 0,
      comienzaElJuego: false,
      finDelJuego: false
    };
  },
  created() {
    this.jugadores = [
      { nombre: "Javier", puntos: 0, puntosParcilales: [] },
      { nombre: "Romina", puntos: 0, puntosParcilales: [] },
      { nombre: "Haydee", puntos: 0, puntosParcilales: [] },
      { nombre: "Martín", puntos: 0, puntosParcilales: [] },
      { nombre: "Melisa", puntos: 0, puntosParcilales: [] },
      { nombre: "Diego", puntos: 0, puntosParcilales: [] }
    ];
  },

  components: {
    CrearJugador,
    Jugador,
    Header
  }
};
</script>

<style scoped>
.container-fluid{
  padding: 0;
  background:#f2f2f2;
}
</style>