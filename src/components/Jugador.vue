<template>

  <div v-if="sumar" class="container">
      <Puntaje
        @controlarPuntaje="controlarPuntaje"
        v-bind:nombreJugador="this.jugador.nombre"
      />
  </div>
</template>

<script>
import Puntaje from "./Puntaje";
export default {
  name: "Jugador",
  methods: {
    controlarPuntaje(puntos) {
      //historial de puntos
      this.jugador.puntos = this.jugador.puntos + puntos;
      this.$emit("controlarPuntaje", this.jugador.puntos);
    }
  },
  props: ["jugador","sumar"],
  components: {
    Puntaje
  }
};
</script>


<style scoped>
</style>