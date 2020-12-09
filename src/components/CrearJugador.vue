<template>
  <div class="container-fluid">

        <b-form @submit.stop.prevent="onSubmit">
          <b-form-group
            id=" input-group-1"
            label-for=" input-1"
          >
            <b-form-input
              id=" input-1"
              name=" input-1"
              v-model="$v.jugadorInicializado.nombre.$model"
              :state="validateState('nombre')"
              aria-describedby="input-1-live-feedback"
              placeholder="Nombre del jugador"
            ></b-form-input>
            <b-form-invalid-feedback id="input-1-live-feedback"
              >Nombre requerido con un mínimo de 3 caracteres
              .</b-form-invalid-feedback
            >
          </b-form-group>

          <b-button
            class="btn btn-primary btn-sm"
            type="submit"
            variant="primary"
            >Agregar jugador <b-icon icon="arrow-down" color: white> </b-icon> </b-button
          >
          <b-button class="btn btn-primary btn-sm" @click="resetForm()"
            >Limpiar</b-button
          >
        </b-form>

  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, minLength } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  data() {
    return {
      jugadorInicializado: {
        nombre: null,
        puntos: 0
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
      this.agregarJugador();
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