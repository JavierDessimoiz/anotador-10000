<!-- Devuelve un jugador recien creado --> 
<template>
  <div class="contJug">
    {{jugadorInicializado.nombre}}
    <b-form @submit.stop.prevent="onSubmit">
      <b-form-group
        id="example-input-group-1"
        label="Name"
        label-for="example-input-1"
      >
        <b-form-input
          id="example-input-1"
          nombre="example-input-1"
          v-model="$v.jugadorInicializado.nombre.$model"
          :state="validateState('nombre')"
          aria-describedby="input-1-live-feedback"
        ></b-form-input>

        <b-form-invalid-feedback id="input-1-live-feedback"
          >Nombre obligatorio con un minimo de 3
          caracteres.</b-form-invalid-feedback
        >
      </b-form-group>
      <!-- <input type="text" v-model= jugadorInicializado.nombre>
    <button v-on:click="agregarJugador() ">Agregar jugador</button>  -->
      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button class="ml-2" @click="resetForm()">Reset</b-button>
    </b-form>
  </div>
</template>

<script>
import { required, minLength } from "vuelidate/lib/validators";

export default {
  name: "CrearJugador",
  data() {
    return {
      jugadorInicializado: { nombre: 'Javier', puntos: 0 }
    };
  },
  validations: {
    jugadorInicializado: {
      nombre: {
        required,
        minLength: minLength(3)
      }
    }
  },
  methods: {
    agregarJugador() {
      this.$emit("agregaJugador", this.jugadorInicializado);
      this.jugadorInicializado.nombre = "";
    },
    validateState(nombre) {
       const { $dirty, $error } = this.$v.jugadorInicializado[nombre];
      return $dirty ? !$error : null;
    },
    resetForm() {
      this.jugadorInicializado = {
        nombre: null,
        puntos: 0
      };

      this.$nextTick(() => {
        this.$v.$reset();
      });
    },
    onSubmit() {
      this.$v.jugadorInicializado.$touch();
      if (this.$v.jugadorInicializado.$anyError) {
        return;
      }

      alert("Form submitted!");
      this.agregarJugador();
    }
  }
};
</script>

<style scoped>
</style>