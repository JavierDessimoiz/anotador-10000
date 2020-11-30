<template>
  <div>
                                       
    <b-form @submit.stop.prevent="onSubmit">
      <b-form-group id=" input-group-1" label="Nombre del jugador" label-for=" input-1">
        <b-form-input
          id=" input-1"
          name=" input-1"
          v-model="$v.jugadorInicializado.nombre.$model"
          :state="validateState('nombre')"
          aria-describedby="input-1-live-feedback"
        ></b-form-input>

        <b-form-invalid-feedback
          id="input-1-live-feedback"
        >Nombre requerido con un mínimo de 3 caracteres .</b-form-invalid-feedback>
      </b-form-group>

      <b-button type="submit" variant="primary">Agregar jugador</b-button>
      <b-button class="ml-2" @click="resetForm()">Limpiar</b-button>
    </b-form>

  </div>
</template>

<style>

</style>

<script>
import { validationMixin } from "vuelidate";
import { required, minLength } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  data() {
    return {
      jugadorInicializado: {
        nombre: null,
        puntos:0
      }
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
      this.jugadorInicializado.nombre = null;
    },
    validateState(nombre) {
      const { $dirty, $error } = this.$v.jugadorInicializado[nombre];
      return $dirty ? !$error : null;
    },
    resetForm() {
      this.jugadorInicializado = {
        nombre: null,
        puntos:0
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
      this.agregarJugador();
    }
  }
};
</script>