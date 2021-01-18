<template>
  <div class="container-fluid">
    <div class="row justify-content-center">
      <input style="text-align:center;"
        id="nombre"
        name="nombre"
        type="text"
        v-model="jugadorInicializado.nombre"
        placeholder="Nombre del jugador"
      />
    </div>
    <b-button
      class="mr-2 mb-2 mt-2"
      variant="primary"
      type="submit"
      @click="agregarJugador()"
    >
      Agregar jugador
    </b-button>
    <b-button
      class="mr-2 mb-2 mt-2"
      variant="secondary"
      type="submit"
      @click="resetForm()"
      >Limpiar
    </b-button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jugadorInicializado: {
        nombre: null,
        puntos: 0
      }
    };
  },

  methods: {
    agregarJugador() {
      this.$emit("agregaJugador", this.jugadorInicializado);
      this.jugadorInicializado.nombre = null;
    },

    resetForm() {
      this.jugadorInicializado = {
        nombre: null,
        puntos: 0
      };
    }
  }
};
</script>

<style scoped>
.container-fluid {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
  background-color: #1855c7;
}

.border-primary {
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