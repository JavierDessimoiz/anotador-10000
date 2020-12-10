<template>
  <div class="container-fluid">
    <Header titulo="Diez mil" />
    <!-- Comienzo -->
    <div v-if="comienzaElJuego === false">
      <b-button
        type="button"
        class="btn btn-default"
        variant="success"
        v-on:click="verificaComienzo"
      >
        Comenzar
      </b-button>
      <CrearJugador @agregaJugador="agregaJugador" />
    </div>

    <div v-if="this.finDelJuego">
      <b-collapse visible id="collapse-ganador">
        <b-card>
          <h3>
            <strong> Ganó {{ this.primerPuesto() }} </strong>
          </h3>
          <b-icon
            icon="trophy-fill"
            aria-hidden="true"
            scale="4"
            color="yellow"
            animation="throb"
            title="1ro"
          ></b-icon
        ></b-card>
      </b-collapse>
    </div>
    <div class="container-fluid" style="background: #fff">
      <div class="row">
        <div
          class="col"
          style="padding: 0"
          v-for="(jugador, index) in jugadores"
          :key="index"
        >
          <div class="container-fluid">
            <div v-if="comienzaElJuego === false">
              <div class="col" style="background: #fff;">
                <b-button
                  variant="outline-primary"
                  block
                  @click="eliminarJugador(jugador.nombre)"
                >
                  <b-icon icon="trash" aria-hidden="false"></b-icon
                  >{{ jugador.nombre }}
                </b-button>
              </div>
            </div>
            <div v-else>
              <div
                class="col"
                style="border-bottom: 1px solid blue; border-left: 1px solid blue; border-top: 1px solid blue;  background: #fff;"
              >
                <strong>{{ jugador.nombre }}</strong>
              </div>
              <div class="progress" style="height: 3px;">
                <div
                  class="progress-bar"
                  role="progressbar"
                  v-bind:style="{ width: (jugador.puntos * 100) / 10000 + '%' }"
                  v-bind:aria-valuenow="jugador.puntos"
                  aria-valuemin="0"
                  aria-valuemax="100"
                ></div>
              </div>
            </div>
          </div>
          <div>
            <div v-if="comienzaElJuego">
              <div
                class="row justify-content-md-center"
                style="background: #F1F1F1; border-left: 2px solid white; text-align: center;"
              >
                <h4 style="color: blue;">
                  <strong>{{ jugador.puntos }}</strong>
                </h4>
              </div>
            </div>
            <!-- <div v-if="finDelJuego === false"> -->
            <jugador
              v-bind:jugador="jugador"
              @controlarPuntaje="controlarPuntaje(jugador, index)"
            />

            <b-collapse id="collapse-parciales" visible class="mt-1">
              <div
                class="row-fluid"
                v-for="(LineaPunto,
                index) in jugador.puntosParcilales.slice().reverse()"
                :key="index"
              >
                <div
                  class="shadow p-1 mb-1 bg-white rounded justify-content-center"
                >
                  <h6>{{ LineaPunto }}</h6>
                </div>
              </div>
            </b-collapse>
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
          jugadorInicializado.nombre + " ya ha sido agregado."
        );
      }
    },
    agregarHistorialPosiciones: function() {
      var jugOrd = this.jugadoresOrdenadoPosicion;
      var posiciones = [];
      for (var i = 0; i < jugOrd.length; i++) {
        const hist = {
          nombre: jugOrd[i].nombre,
          posicion: i + 1
        };
        posiciones.push(hist);
      }
      this.HistPosiciones.push(posiciones);
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
          if (this.jugadores[index].puntosParcilales.length === 0) {
            this.makeToast(
              "info",
              "Info",
              "Entró al juego " + this.jugadores[index].nombre + "."
            );
          }

          //agrega al historial de posiciones

          this.agregarHistorialPosiciones();

          //Notificación de cambio de posición
          //console.log(this.HistPosiciones[this.HistPosiciones.length-1].jugadorPos,0);
          console.log(this.HistPosiciones[this.HistPosiciones.length-1].jugadorPos);

          //if (this.HistPosiciones.length > 1) {
          //  const posAnt = this.HistPosiciones[
          //    this.HistPosiciones.length - 1
          //  ].jugadorPos.find(this.jugadores[index].nombre).posicion;
          //  const posAct = this.HistPosiciones[
          //    this.HistPosiciones.length
          //  ].jugadorPos.find(this.jugadores[index].nombre).posicion;
          //}

          //agregar en parciales
          this.jugadores[index].puntosParcilales.push(jugador.puntos);

          //controla el ganador
          if (jugador.puntos === Number(10000)) {
            this.finDelJuego = true;
            this.$root.$emit("bv::show::collapse", "collapse-ganador");
            //this.collapse-ganador.show;

            //this.$root.$emit('bv::toggle::collapse', 'collapse-ganador');
            //$('#collapse-ganador').collapse('show');
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
    },
    eliminarJugador: function(nombre) {
      this.jugadores = this.jugadores.filter(
        jugador => jugador.nombre != nombre
      );
    },
    primerPuesto: function() {
      var max = 0;
      for (var i = 1; i < this.jugadores.length; i++) {
        if (this.jugadores[i].puntos > this.jugadores[max].puntos) {
          max = i;
        }
      }
      return this.jugadores[max].nombre;
    },
    nuevoJuego: function() {
      console.log("juego nuevo juego");
      this.jugadores = [
        { nombre: "Romina", puntos: 0, puntosParcilales: [] },
        { nombre: "Javier", puntos: 0, puntosParcilales: [] },
        { nombre: "Haydee", puntos: 0, puntosParcilales: [] },
        { nombre: "Martin", puntos: 0, puntosParcilales: [] },
        { nombre: "Melisa", puntos: 0, puntosParcilales: [] },
        { nombre: "Diego", puntos: 0, puntosParcilales: [] }
      ];
      this.comienzaElJuego = true;
      this.finDelJuego = false;
    },
    verificaComienzo: function() {
      if (this.jugadores.length > 1) {
        this.comienzaElJuego = true;
      } else {
        this.makeToast(
          "danger",
          "Error",
          "Deben participar al menos 2 jugadores."
        );
      }
    }
  },
  props: ["titulo"],
  data: function() {
    return {
      //array de jugadores
      jugadores: [],
      comienzaElJuego: false,
      finDelJuego: false,
      HistPosiciones: [{ jugadorPos: [] }]
    };
  },
  computed: {
    jugadoresOrdenadoPosicion: function() {
      return this.jugadores.slice(0).sort(function(a, b) {
        return b.puntos - a.puntos;
      });
    }
  },
  created() {
    this.jugadores = [
      { nombre: "Romina", puntos: 0, puntosParcilales: [] },
      { nombre: "Javier", puntos: 0, puntosParcilales: [] },
      { nombre: "Haydee", puntos: 0, puntosParcilales: [] },
      { nombre: "Martin", puntos: 0, puntosParcilales: [] },
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
.container-fluid {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
  background: #f2f2f2;
}

.col {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}

.row {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}
</style>
