<template>
  <div class="juego">
    
    <table class="table table-responsive table-bordered">
      <thead class="thead-light">

          <th >
            <b-iconstack font-scale="2">
              <b-icon stacked icon="square"></b-icon>
              <b-icon stacked icon="dot" shift-h="-3" shift-v="4"></b-icon>
              <b-icon stacked icon="dot" shift-h="-3"></b-icon>
              <b-icon stacked icon="dot" shift-h="-3" shift-v="-4"></b-icon>
              <b-icon stacked icon="dot" shift-h="3" shift-v="4"></b-icon>
              <b-icon stacked icon="dot" shift-h="3"></b-icon>
              <b-icon stacked icon="dot" shift-h="3" shift-v="-4"></b-icon>
            </b-iconstack>
          </th>
          <th>
            <b-iconstack font-scale="2" rotate="55">
              <b-icon stacked icon="square"></b-icon>
              <b-icon stacked icon="dot" shift-h="-3" shift-v="4"></b-icon>
              <b-icon stacked icon="dot" shift-h="-3" shift-v="-4"></b-icon>
              <b-icon stacked icon="dot" shift-h="3" shift-v="4"></b-icon>
              <b-icon stacked icon="dot" shift-h="3" shift-v="-4"></b-icon>
            </b-iconstack>
          </th>
          <th>
            <h2>{{ titulo }}</h2>
          </th>
          <tr>
          
            <div v-if="this.finDelJuego">
              <b-alert show>Fin del juego</b-alert>
            </div>
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
          </tr>

      </thead>

      <tbody>
        <!-- <table class="table table-responsive table-bordered "> -->
        <tr>
          <th v-for="(jugador, index) in jugadores" :key="index">
            <table class="table table-responsive">
              <thead>
                <div v-if="comienzaElJuego === false">
                  <b-button variant="outline-danger">
                    <b-icon icon="trash" aria-hidden="true"></b-icon>
                    {{ jugador.nombre }}
                  </b-button>
                </div>
                <div v-else>
                  <b-button variant="outline-primary">
                    <strong> {{ jugador.nombre }} </strong>
                  </b-button>
                </div>
              </thead>
              <tr>
                {{
                  jugador.puntos
                }}
              </tr>
            </table>

            <div v-if="finDelJuego === false">
              <jugador
                v-bind:jugador="jugador"
                @controlarPuntaje="controlarPuntaje(jugador, index)"
              />
            </div>

            <ul id="Puntos">
              <tr
                v-for="(LineaPunto,
                index) in jugador.puntosParcilales.slice().reverse()"
                :key="index"
              >
                {{
                  LineaPunto
                }}
              </tr>
            </ul>
          </th>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
import CrearJugador from "./CrearJugador";
import Jugador from "./Jugador";
//import Alerta from "./Alerta";

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
      { nombre: "Diego", puntos: 0, puntosParcilales: [] },
      { nombre: "Santi", puntos: 0, puntosParcilales: [] }
    ];
  },

  components: {
    CrearJugador,
    Jugador
    //Alerta
  }
};
</script>

<style scoped>
.juego {
  background-image: url("/assets/fondo.png");
  background-color: #ffff;
  height: 100vh;
  width: 100%;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
</style>