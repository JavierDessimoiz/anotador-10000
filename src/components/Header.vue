<template>
  <div class="container-fluid" style="padding: 0; background:#3587dac2;">
    <div class="row">
      <div class="col">
        <b-icon
          icon="dice5-fill"
          aria-hidden="true"
          scale="2"
          variant="white"
          shift-h="-7"
          shift-v="-6"
        ></b-icon>
        <b-icon
          icon="dice1-fill"
          aria-hidden="true"
          scale="2"
          variant="white"
          shift-h="10"
          shift-v="-6"
        ></b-icon>
      </div>
      <div class="col">
        <h4 style="color: white;">
          <strong>{{ titulo }}</strong>
        </h4>
      </div>
      <div class="col">
        <b-button v-b-toggle.collapse-menu variant="primary"
          ><b-icon icon="list" aria-hidden="true"></b-icon
        ></b-button>
        <b-collapse id="collapse-menu" class="mt-2">
          <div class="row">
            <b-button
              v-on:click="nuevoJuego"
              v-b-toggle.collapse-menu
              block
              variant="primary"
              size="sm"
              class="mt-1"
              >Nuevo juego</b-button
            >
          </div>
          <div class="row">
            <b-button
              v-on:click="deshacerUltimoPuntaje"
              v-b-toggle.collapse-menu
              block
              variant="primary"
              size="sm"
              class="mt-1"
              >Deshacer puntaje</b-button
            >
          </div>
          <div class="row">
            <b-button
              v-b-toggle="['collapse-menu', 'sidebar-right']"
              block
              variant="primary"
              size="sm"
              class="mt-1"
              >Mostrar posiciones
            </b-button>
          </div>
          <div class="row">
            <b-button
              block
              v-b-toggle="['collapse-menu', 'collapse-parciales']"
              variant="primary"
              size="sm"
              class="mt-1"
              aria-expanded= 'true'
              >Mostrar parciales
            </b-button>
          </div>
          <div class="row">
            <b-button
              block
              v-b-toggle="['collapse-menu', 'collapse-chart']"
              v-on:click="genChart"
              variant="primary"
              size="sm"
              aria-expanded= 'true'
              class="mt-1"
              >Gráfico posiciones
            </b-button>
          </div>
          <div class="row">
            <b-button 
            v-b-toggle.collapse-menu 
            v-b-modal.instrucciones-scrollable
            block 
            variant="primary" 
            size="sm"
            class="mt-1"
              >Instrucciones
            </b-button>
          </div>
          <div class="row">
            <b-button 
            v-b-toggle.collapse-menu 
            block 
            variant="primary" 
            size="sm"
            class="mt-1"
             href="https://github.com/JavierDessimoiz/anotador-10000" 
             target="_blank"
              > GitHub
            </b-button>
          </div>
           <div class="row">
            <b-button 
            v-b-toggle.collapse-menu 
            block 
            variant="primary" 
            size="sm"
            class="mt-1"
             href="https://www.linkedin.com/in/javierdessimoiz/" 
             target="_blank"
              > Linkedin
            </b-button>
          </div>
        </b-collapse>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0
    };
  },
  props: ["titulo"],
  methods: {
    nuevoJuego: function() {
      this.$emit("nuevoJuego");
    },
    genChart: function() {
      this.$emit("genChart");
    },
    deshacerUltimoPuntaje: function() {
      this.$emit("deshacerUltimoPuntaje");
    }
  }
};
</script>

<style scoped>
.container-fluid {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
  background: #3588dae1;
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