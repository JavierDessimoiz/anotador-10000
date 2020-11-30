<template >
  <div class="container">
    <b-button pill variant="light" id="show-btn" @click="showModal">
      <b-icon icon="pencil" aria-hidden="true"></b-icon
    ></b-button>

    <b-modal ref="puntos-modal" hide-footer>
      <div class="row">
        <h3> <b-avatar variant="info"></b-avatar> Sumar puntos a {{ nombreJugador }} </h3>
      </div>
      <div class="d-block text-center">
        <form ref="form" @submit.stop.prevent="handleSubmit">
          <b-form-group
            :state="puntosState"
            label-for="puntos-input"
            invalid-feedback="Ingrese los puntos"
          >
            <b-form-input
              id="puntos-input"
              v-model="puntosum"
              type="number"
              :state="puntosState"
              required
            ></b-form-input>
          </b-form-group>
        </form>

        <div class="row">
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(50)">
              +50
            </button>
          </div>
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(100)">
              +100
            </button>
          </div>
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(200)">
              +200
            </button>
          </div>
        </div>
        <div class="row">
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(300)">
              +300
            </button>
          </div>
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(500)">
              +500
            </button>
          </div>
          <div class="col">
            <button class="btn btn-outline-primary" v-on:click="sumar(1000)">
              +1000
            </button>
          </div>
        </div>

        <div class="row" style="align: center">
          <div class="col">
            <!-- <button class="btn btn-outline-danger" v-on:click="borrar()">
            Borrar
          </button> -->
            <b-button
              class="mt-3"
              variant="outline-danger"
              block
              @click="borrar()"
              >Limpiar</b-button
            >
          </div>

          <div class="col">
            <b-button
              class="mt-3"
              variant="outline-success"
              block
              @click="hideModal()"
              >Sumar</b-button
            >
          </div>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "Puntaje",
  props: ["nombreJugador","habilitado"],
  data() {
    return {
      puntosum: null,
      puntosState: null
    };
  },
  created: function() {
    this.puntosum = null;
    this.puntosState = null;
  },
  methods: {
    sumar(cantPuntos) {
      this.puntosum = Number(this.puntosum) + Number(cantPuntos);
    },
    borrar() {
      this.puntosum = null;
      this.puntosState = null;
    },
    //modal ini
    showModal() {
      this.puntosum = null;
      this.puntosState = null;
      this.$refs["puntos-modal"].show();
    },
    hideModal() {
      if (this.validarPuntaje()) {
        this.$emit("controlarPuntaje", this.puntosum);
        this.puntosum = null;
        this.$refs["puntos-modal"].hide();
      }
    },
    //modal fin
    validarPuntaje() {
      const valid = this.$refs.form.checkValidity();
      this.puntosState = valid;
      return valid;
    }
  }
};
</script>
