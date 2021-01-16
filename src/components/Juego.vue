<template>
  <div class="container-fluid">
    <Header
      titulo="Diez mil"
      @nuevoJuego="nuevoJuego"
      @genChart="genChart"
      @deshacerUltimoPuntaje="deshacerUltimoPuntaje"
    />

    <!-- Comienzo -->
    <b-collapse id="collapse-agregar" v-model="ingresaJugadores">
      <div class="container-fluid">
        <!-- <div v-if="comienzaElJuego === false"> -->
        <b-button
          type="button"
          class="mb-2 mt-2"
          variant="success"
          v-on:click="verificaComienzo"
        >
          Comenzar
          <b-icon
            icon="play-fill"
            aria-hidden="true"
            scale="1"
            color="white"
            title="Agregar"
          ></b-icon>
        </b-button>

        <CrearJugador @agregaJugador="agregaJugador" />
      </div>
    </b-collapse>

    <b-collapse v-model="finDelJuego" id="collapse-ganador">
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

    <b-sidebar id="sidebar-right" left shadow>
      <b-row class="shadow p-3 mb-1 bg-light rounded justify-content-center"
        ><strong>POSICIONES</strong></b-row
      >
      <div class="px-3 py-2" v-if="HistPosiciones.length > 1">
        <div
          v-for="(posiciones, index) in HistPosiciones[
            HistPosiciones.length - 1
          ]"
          :key="index"
        >
          <b-row>
            <b-col
              class="shadow p-1 mb-1 bg-white rounded justify-content-center"
              ><strong>{{ posiciones.posicion }}</strong>
            </b-col>
            <b-col
              class="shadow p-1 mb-1 bg-white rounded"
              cols="6"
              :style="{ color: getBorderColorPorNombre(posiciones.nombre) }"
              ><strong> {{ posiciones.nombre }} </strong></b-col
            >
            <b-col class="shadow p-1 mb-1 bg-white rounded"
              ><h5>
                <strong> {{ getpuntajeJugador(posiciones.nombre) }}</strong>
              </h5>
            </b-col>
            <b-col class="shadow p-1 mb-1 bg-white rounded">
              <p style="color: red;">
                ({{ 10000 - getpuntajeJugador(posiciones.nombre) }})
              </p>
            </b-col>
          </b-row>
        </div>
      </div>
    </b-sidebar>

    <div class="container-fluid" style="background: #fff">
      <div class="row">
        <div class="col" v-for="(jugador, index) in jugadores" :key="index">
          <div class="container-fluid">
            <div v-if="ingresaJugadores">
              <div class="col" style="background: #fff;">
                <b-button
                  variant="outline-primary"
                  block
                  @click="eliminarJugador(jugador.nombre)"
                >
                  <P>{{ jugador.nombre }}</P>
                  <b-icon icon="trash" aria-hidden="false" scale="1.3"></b-icon>
                </b-button>
              </div>
            </div>
            <div v-else>
              <div
                class="col"
                style="border-bottom: 1px solid blue; border-left: 1px solid blue; border-top: 1px solid blue;  background: #fff;"
              >
                <strong :style="{ color: getBorderColor(index) }">{{
                  jugador.nombre
                }}</strong>
              </div>
              <div class="progress" style="height: 4px;">
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
            <div v-if="ingresaJugadores === false">
              <div
                class="d-flex justify-content-center"
                style="background: #F1F1F1; border-left: 2px solid white; text-align: center;"
              >
                <h4>
                  <strong
                    class="d-flex justify-content-center"
                    style="color:blue;"
                    >{{ jugador.puntos }}</strong
                  >
                </h4>
              </div>
            </div>

            <jugador
              v-bind:jugador="jugador"
              v-bind:sumar="comienzaElJuego"
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
                  class="shadow-lg p-1 bg-white rounded justify-content-center"
                >
                  <h6>{{ LineaPunto }}</h6>
                </div>
              </div>
            </b-collapse>
          </div>
        </div>
      </div>
    </div>
    <b-collapse id="collapse-chart" class="mt-1">
      <div>
        <canvas id="posChart"></canvas>
      </div>
    </b-collapse>
    <b-modal id="instrucciones-scrollable" scrollable title="Instrucciones">
      <p class="my-4">
        <strong>Como se juega?</strong>
      </p>

      <p>
        Para entrar al juego es necesario sacar como mínimo 750 puntos. El
        jugador en su turno tira 5 dados, tratando de que salgan UNOS (cada uno
        vale 100 puntos), CINCOS (cada uno vale 50 puntos), y/o tres dados
        iguales (valen 100 veces el número que sale, por ejemplo: 2-2-2= 200,
        etc. excepto si salen 1-1-1 que en ese caso valen 1000) Un tiro de
        1-2-3-4-5 o 2-3-4-5-6 es un tiro especial que vale 500 puntos.
      </p>

      <p>
        Un jugador después de contar los puntos, puede terminar y agregar todos
        los puntos de ese turno a su puntaje total, o tirar otra vez usando los
        dados que no sumen, tratando de hacer puntos adicionales, el turno del
        jugador finaliza cuando hace un tiro sin conbinaciones que den puntos.
        En caso de no realizar un tiro que no sume puntaje en su turno no sumará
        puntos. Una vez que suma los puntos iniciales para ingresar al juego
        (750) en los turnos siguientes sumará los puntos que consiga, no es
        necesario sumar un mínimo de 750.
      </p>

      <strong>Final del juego.</strong>
      <p>El primer jugador que llegue a los 10.000 exactos será el ganador.</p>
    </b-modal>
  </div>
</template>
<script>
import CrearJugador from "./CrearJugador";
import Jugador from "./Jugador";
import Header from "./Header";
import Chart from "chart.js";

export default {
  name: "Juego",
  props: ["titulo"],
  data: function() {
    return {
      //array de jugadores
      jugadores: [],
      comienzaElJuego: false,
      ingresaJugadores: true,
      finDelJuego: false,
      HistPosiciones: [{ jugadorPos: [] }],
      confirmaNuevo: "",
      confirmaEliminar: "",
      ultimoJugadorEnSumar: "",
      ultimosPuntosSumados: 0
    };
  },

  methods: {
    agregaJugador: function(jugadorInicializado) {
      if (this.jugadores.length === 8) {
        this.makeToast(
          "danger",
          "Error",
          "El máximo número de jugadores es 8."
        );
      }
      else {
      const existe =
        this.jugadores.filter(
          jugador =>
            jugador.nombre.toUpperCase() ==
            jugadorInicializado.nombre.toUpperCase()
        ).length > 0;
      if (!existe) {
        const newJug = {
          nombre: jugadorInicializado.nombre,
          puntos: jugadorInicializado.puntos,
          puntosParcilales: []
        };
        this.jugadores.push(newJug);
        this.saveLocalStorageJugadores();
        this.saveLocalStoragehistorial();
        this.genChart();
      } else {
        this.makeToast(
          "danger",
          "Error",
          jugadorInicializado.nombre + " ya ha sido agregado."
        );
      }
    }},
    agregarHistorialPosiciones: function() {
      var jugOrd = this.jugadoresOrdenadoPosicion;
      var posiciones = [];
      var pos = 1;
      var puntosPos = jugOrd[0].puntos;

      var i = 0;
      while (i < jugOrd.length) {
        while (i < jugOrd.length && puntosPos == jugOrd[i].puntos) {
          const hist = {
            nombre: jugOrd[i].nombre,
            posicion: pos
          };
          posiciones.push(hist);
          i++;
        }
        if (i < jugOrd.length) {
          puntosPos = jugOrd[i].puntos;
          pos++;
        }
      }
      this.HistPosiciones.push(posiciones);
      this.saveLocalStorageHistorial();
    },
    controlarPuntaje: function(jugador, index) {
      if (jugador.puntos === this.jugadores[index].puntosParcilales[this.jugadores[index].puntosParcilales.length - 1]){
        this.makeToast(
          "danger",
          "Error",
          " No se ingresaron puntos para " + this.jugadores[index].nombre + ". Por favor ingresar un puntaje.",  
        );
      }
      else {
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
              this.jugadores[index].nombre + " entró al juego."
            );
          }

          //agrega al historial de posiciones
          this.agregarHistorialPosiciones();

          //Notificaión restan pocos puntos
          if (
            Number(this.jugadores[index].puntos) < Number(10000) &&
            Number(10000) - Number(this.jugadores[index].puntos) <= Number(1000)
          ) {
            this.makeToast(
              "Info",
              "Info",
              "A " +
                this.jugadores[index].nombre +
                " solo le restan " +
                (Number(10000) - Number(this.jugadores[index].puntos)) +
                " puntos para ganar."
            );
          }

          //Notificación de cambio de posición
          if (this.HistPosiciones.length > 2) {
            var posAct = this.HistPosiciones[
              this.HistPosiciones.length - 1
            ].find(jug => jug.nombre === this.jugadores[index].nombre).posicion;
            var posAnt = this.HistPosiciones[
              this.HistPosiciones.length - 2
            ].find(jug => jug.nombre === this.jugadores[index].nombre).posicion;

            if (
              posAnt > posAct &&
              this.jugadores[index].puntosParcilales.length > 0
            )
              this.makeToast(
                "info",
                "Info",
                this.jugadores[index].nombre +
                  " subió del puesto " +
                  posAnt +
                  " al puesto " +
                  posAct +
                  "."
              );
          }

          //agregar en parciales
          this.jugadores[index].puntosParcilales.push(jugador.puntos);
          this.saveLocalStorageJugadores();
          this.genChart();
          //guarda el ultimo puntaje sumado junto con su nombre en caso de querer deshacer
          if (this.jugadores[index].puntosParcilales.length > 1) {
            var dif =
              this.jugadores[index].puntosParcilales[
                this.jugadores[index].puntosParcilales.length - 1
              ] -
              this.jugadores[index].puntosParcilales[
                this.jugadores[index].puntosParcilales.length - 2
              ];
          } else {
            if (this.jugadores[index].puntosParcilales.length == 1) {
              dif = this.jugadores[index].puntosParcilales[
                this.jugadores[index].puntosParcilales.length - 1
              ];
            } else {
              dif = 0;
            }
          }
          this.ultimoJugadorEnSumar = jugador.nombre;
          this.ultimosPuntosSumados = dif;
          this.saveLocalStorageHistorial();
          //controla el ganador
          if (jugador.puntos === Number(10000)) {
            this.finDelJuego = true;
            this.comienzaElJuego = false;
            this.ingresaJugadores = false;

            this.gardarVariablesControlLocalStorage();
          }
        }
      }
    }},
    makeToast(variant = null, titulo, mensaje) {
      this.$bvToast.toast(mensaje, {
        title: titulo,
        variant: variant,
        solid: true
      });
    },
    eliminarJugador: function(nombre) {
      this.confirmaEliminar = "";
      this.$bvModal
        .msgBoxConfirm("¿Está seguro de eliminar a " + nombre + "?")
        .then(value => {
          this.confirmaEliminar = value;
          if (this.confirmaEliminar) {
            this.jugadores = this.jugadores.filter(
              jugador => jugador.nombre != nombre
            );
            this.saveLocalStorageJugadores();
            this.saveLocalStorageHistorial();
            this.genChart();
          }
        })

        .catch(err => {
          console.log(err + "error en Eliminar jugador");
          // An error occurred
        });
    },
    primerPuesto: function() {
      var max = 0;
      if (this.jugadores.length > 0) {
        for (var i = 1; i < this.jugadores.length; i++) {
          if (this.jugadores[i].puntos > this.jugadores[max].puntos) {
            max = i;
          }
        }
        return this.jugadores[max].nombre;
      }
    },
    nuevoJuego: function() {
      this.confirmaNuevo = "";
      this.$bvModal
        .msgBoxConfirm("¿Está seguro de iniciar un nuevo juego?")
        .then(value => {
          this.confirmaNuevo = value;
          if (this.confirmaNuevo) {
            this.jugadores.splice(0, this.jugadores.length);
            this.saveLocalStorageJugadores();
            this.comienzaElJuego = false;
            this.ingresaJugadores = true;
            this.finDelJuego = false;
            this.ultimoJugadorEnSumar = "";
            this.ultimosPuntosSumados = 0;
            this.gardarVariablesControlLocalStorage();
            this.HistPosiciones.splice(0, this.HistPosiciones.length);
            this.saveLocalStorageHistorial();
            this.genChart();
          }
        })

        .catch(err => {
          console.log(err + "error en Nuevo juego");
          // An error occurred
        });
    },
    verificaComienzo: function() {
      if (this.jugadores.length > 1) {
        this.comienzaElJuego = true;
        this.ingresaJugadores = false;
        this.finDelJuego = false;
        this.gardarVariablesControlLocalStorage();
      } else {
        this.makeToast(
          "danger",
          "Error",
          "Deben participar al menos 2 jugadores."
        );
      }
    },
    getpuntajeJugador: function(nombreJug) {
      const jug = this.jugadores.filter(
        jugador => jugador.nombre === nombreJug
      );
      var puntosJug;
      jug.forEach(element => {
        puntosJug = element.puntos;
      });
      return puntosJug;
    },
    posiconesPorJugador: function(nombre) {
      var posicJuga = [];
      var posicion = 1;

      for (var i = 0; i < this.HistPosiciones.length; i++) {
        for (var j = 0; j < this.HistPosiciones[i].length; j++) {
          if (this.HistPosiciones[i][j].nombre == nombre) {
            posicion = this.HistPosiciones[i][j].posicion;
          }
        }
        posicJuga.push(Number(posicion));
      }
      return posicJuga;
    },

    chartEjeY: function() {
      var ejey = [];
      for (var i = 1; i < this.jugadores.length; i++) {
        ejey.push(Number(i));
      }
      return ejey;
    },

    chartEjeX: function() {
      var ejex = [];
      for (var i = 1; i < this.HistPosiciones.length; i++) {
        ejex.push(Number(i));
      }
      return ejex;
    },

    getBorderColor: function(i) {
      var borderColors = [
        "rgba(255,99,132,1)",
        "rgba(54, 162, 235, 1)",
        "rgba(255, 206, 86, 1)",
        "rgba(75, 192, 192, 1)",
        "rgba(153, 50, 190, 1)",
        "rgba(255, 159, 64, 1)",
        "rgba(200, 99, 64, 1)",
        "rgba(75, 100, 255, 1)"
      ];
      return borderColors[i];
    },

    getBorderColorPorNombre: function(nombre) {
      var ind = 0;
      for (var i = 1; i < this.jugadores.length; i++) {
        if (this.jugadores[i].nombre === nombre) {
          ind = i;
        }
      }
      return this.getBorderColor(ind);
    },

    getBackgroundColor: function(i) {
      var backgroundColors = [
        "rgba(255,99,132,0.2)",
        "rgba(54, 162, 235, 0.2)",
        "rgba(255, 206, 86, 0.2)",
        "rgba(75, 192, 192, 0.2)",
        "rgba(153, 50, 190, 0.2)",
        "rgba(255, 159, 64, 0.2)",
        "rgba(200, 99, 64, 0.2)",
        "rgba(75, 100, 255, 0.2)"
      ];
      return backgroundColors[i];
    },

    chartData: function() {
      var Dataset = [];
      for (var i = 0; i < this.jugadores.length; i++) {
        var Data = {
          label: this.jugadores[i].nombre,
          fill: false,
          data: this.posiconesPorJugador(this.jugadores[i].nombre),
          lineTension: 0,
          reverse: true,
          backgroundColor: this.getBackgroundColor(i),
          borderColor: this.getBorderColor(i)
        };
        Dataset.push(Data);
      }
      return Dataset;
    },

    genChart: function() {
      new Chart(document.getElementById("posChart"), {
        type: "line",
        data: {
          labels: this.chartEjeX(),
          datasets: this.chartData()
        },
        options: {
          responsive: true,
          lineTension: 1,
          title: {
            display: true,
            text: "Historial posiciones",
            fontSize: 17
          },
          scales: {
            yAxes: [
              {
                scaleLabel: {
                  display: true,
                  labelString: "Posiciones",
                  fontSize: 14,
                  easing: "linear"
                },
                ticks: {
                  beginAtZero: false,
                  reverse: true,
                  steps: this.jugadores.length,
                  suggestedMin: 1,
                  //scaleSteps: 500,
                  suggestedMax: this.jugadores.length,
                  scaleStartValue: 1,
                  stepSize: 1
                }
              }
            ],
            xAxes: [
              {
                scaleLabel: {
                  display: true,
                  labelString: "Turnos",
                  fontSize: 14
                }
              }
            ]
          }
        }
      });
    },

    saveLocalStorageHistorial: function() {
      const parsed = JSON.stringify(this.HistPosiciones);
      localStorage.setItem("HistPosiciones", parsed);
      localStorage.ultimoJugadorEnSumar = this.ultimoJugadorEnSumar;
      localStorage.ultimosPuntosSumados = this.ultimosPuntosSumados;
    },

    saveLocalStorageJugadores: function() {
      const parsed = JSON.stringify(this.jugadores);
      localStorage.setItem("jugadores", parsed);
    },

    cargarNuevoJuegoJugadores: function() {
      if (localStorage.getItem("jugadores")) {
        try {
          this.jugadores = JSON.parse(localStorage.getItem("jugadores"));
        } catch (e) {
          localStorage.removeItem("jugadores");
        }
      } else {
        this.jugadores = [
          { nombre: "ROMINA", puntos: 0, puntosParcilales: [] },
          { nombre: "JAVIER", puntos: 0, puntosParcilales: [] },
          { nombre: "HAYDEE", puntos: 0, puntosParcilales: [] },
          { nombre: "MARTIN", puntos: 0, puntosParcilales: [] },
          { nombre: "MELISA", puntos: 0, puntosParcilales: [] },
          { nombre: "DIEGO", puntos: 0, puntosParcilales: [] }
        ];
      }
    },

    CargarNuevoJuegoHistorial: function() {
      if (localStorage.getItem("HistPosiciones")) {
        try {
          this.HistPosiciones = JSON.parse(
            localStorage.getItem("HistPosiciones")
          );
        } catch (e) {
          localStorage.removeItem("HistPosiciones");
        }
      } else {
        this.comienzaElJuego = false;
        this.ingresaJugadores = true;
        this.finDelJuego = false;
        this.ultimoJugadorEnSumar = "";
        this.ultimosPuntosSumados = 0;
        this.gardarVariablesControlLocalStorage();
        this.HistPosiciones.splice(0, this.HistPosiciones.length);
        this.genChart();
      }
    },

    gardarVariablesControlLocalStorage: function() {
      localStorage.finDelJuego = this.finDelJuego;
      localStorage.comienzaElJuego = this.comienzaElJuego;
      localStorage.ingresaJugadores = this.ingresaJugadores;
    },

    deshacerUltimoPuntaje: function() {
      this.confirmaEliminar = "";
      if (this.ultimosPuntosSumados > 0 && this.finDelJuego === false) {
        this.$bvModal
          .msgBoxConfirm(
            "¿Está seguro deshacer la ultima anotación? Se restarán " +
              this.ultimosPuntosSumados +
              " puntos a " +
              this.ultimoJugadorEnSumar +
              "."
          )
          .then(value => {
            this.confirmaEliminar = value;
            if (this.confirmaEliminar) {
              //Borro del historial de posiciones el último movimiento
              this.HistPosiciones.splice(this.HistPosiciones.length - 1);
              //Busco el jugador al que hay que restarles los puntos
              var ind = 0;
              for (var i = 1; i < this.jugadores.length; i++) {
                if (this.jugadores[i].nombre === this.ultimoJugadorEnSumar) {
                  ind = i;
                }
              }

              //Saco de parciales el último valor
              if (this.ultimosPuntosSumados > 0) {
                this.jugadores[ind].puntos =
                  this.jugadores[ind].puntos - this.ultimosPuntosSumados;
                this.jugadores[ind].puntosParcilales.splice(
                  this.jugadores[ind].puntosParcilales.length - 1
                );
                this.makeToast(
                  "Info",
                  "Info",
                  "Se borraron " +
                    this.ultimosPuntosSumados +
                    " puntos a " +
                    this.jugadores[ind].nombre
                );
                this.ultimosPuntosSumados = 0;
              }

              this.saveLocalStorageHistorial();
              this.genChart();
            }
          })
          .catch(err => {
            console.log(err + "error en deshacer");
            // An error occurred
          });
      } else {
        if (this.finDelJuego) {
        this.makeToast(
          "danger",
          "Info",
          "El juego ha finalizado"
        )}
        else {
          this.makeToast(
          "danger",
          "Info",
          "Solo se puede deshacer el último puntaje ingresado."
        )
        }
      }
    }
  },

  computed: {
    jugadoresOrdenadoPosicion: function() {
      return this.jugadores.slice(0).sort(function(a, b) {
        return b.puntos - a.puntos;
      });
    }
  },

  beforeMount() {
    this.cargarNuevoJuegoJugadores();
    this.CargarNuevoJuegoHistorial();

    if (localStorage.comienzaElJuego) {
      var cond = localStorage.getItem("comienzaElJuego");
      cond = JSON.parse(cond);
      this.comienzaElJuego = cond;
    }

    if (localStorage.ingresaJugadores) {
      cond = localStorage.getItem("ingresaJugadores");
      cond = JSON.parse(cond);
      this.ingresaJugadores = cond;
    }

    if (localStorage.comienzaElJuego) {
      cond = localStorage.getItem("finDelJuego");
      cond = JSON.parse(cond);
      this.finDelJuego = cond;
    }
    
    if (localStorage.ultimoJugadorEnSumar) {
      this.ultimoJugadorEnSumar = localStorage.ultimoJugadorEnSumar;
    }

    if (localStorage.ultimosPuntosSumados) {
      this.ultimosPuntosSumados = localStorage.ultimosPuntosSumados;
    }

    this.gardarVariablesControlLocalStorage();
    //this.genChart();
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
