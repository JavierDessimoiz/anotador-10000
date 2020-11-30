<template>
  <div class="juego">
    <h1>{{ titulo }}</h1>
    <div v-if="this.finDelJuego">
      <b-alert show>Fin del juego</b-alert>
    </div>
    <!--
    <div v-if="this.cantidadDejugadores === 0">
      <input
        type="number"
        v-model="cantDejugadores"
        placeholder="Cantidad de Jugadores"
        required
      />
      <button v-on:click="cantidadDejugadores = cantDejugadores">
        Aceptar
      </button>
    </div>
    -->
    <div v-if="jugadores.length < this.cantidadDejugadores">
      <CrearJugador @agregaJugador="agregaJugador" />
    </div>
    <table>
      <tr>
        <th v-for="(jugador, index) in jugadores" :key="index">
          <jugador
            v-bind:jugador="jugador"
            @controlarPuntaje="controlarPuntaje(jugador, index)"
          />
          <!-- Alerta de ingreso -->
          <div v-if="alertaDeIngreso === index">
            <b-alert show>Se ingresa con 750 puntos</b-alert>
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
    </table>
  </div>
</template>
<script>
import CrearJugador from "./CrearJugador";
import Jugador from "./Jugador";

export default {
  name: "Juego",
  methods: {
    agregaJugador: function(jugadorInicializado) {
      const newJug = {
        nombre: jugadorInicializado.nombre,
        puntos: jugadorInicializado.puntos,
        puntosParcilales: []
      };
      this.jugadores.push(newJug);
    },
    controlarPuntaje: function(jugador, index) {
      //se verifica el ingreso minimo al juego con 750 puntos

      if (
        this.jugadores[index].puntosParcilales.length === 0 &&
        jugador.puntos < Number(750)
      ) {
        jugador.puntos = 0;
        this.alertaDeIngreso = index;
      } else {
        this.alertaDeIngreso = -1;
        if (jugador.puntos > Number(10000)) {
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
    }
  },
  props: ["titulo"],
  data: function() {
    return {
      //array de jugadores
      jugadores: [],
      cantidadDejugadores: 0,
      cantDejugadores: 2,
      comienzo: false,
      finDelJuego: false,
      alertaDeIngreso: -1
    };
  },
  created() {
    //this.jugadores = [];
  },
  components: {
    CrearJugador,
    Jugador
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