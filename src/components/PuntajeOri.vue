<template>
  <div class="contpuntaje">
    <input v-modal= "puntosaSumar">
    <button v-on:click="sumar()">+</button>
  </div>
</template>
 
<script>
export default {
  name: "Puntaje",
  data: function() {
    return {
      puntos: 0,
      puntosaSumar: 0
    };
  },

  methods: {
    sumar() {
      this.puntos = Number(this.puntos) + Number(this.puntosaSumar);
      this.$emit("actualizarPuntos", this.puntos);
      this.puntosaSumar = 0;
    }
  }
};

<style scoped></style>;
