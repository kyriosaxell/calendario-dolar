<template>
  <v-container>
    <v-layout :wrap="true">
      <v-flex xs12>
        <v-card>
          <v-date-picker
            color="red darken-3"
            v-model="fecha"
            :min="minimo"
            :max="maximo"
            full-width
            locale="es-MX"
            @change="getDolar(fecha)"
          ></v-date-picker>
        </v-card>
        <v-card color="red darken-2" dark>
          <v-card-text class="title text-center">{{valor}}</v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
export default {
  data: () => ({
    fecha: new Date().toISOString().substring(0, 10),
    minimo: "1984",
    maximo: new Date().toISOString().substring(0, 10),
    valor: ""
  }),
  methods: {
    async getDolar(dia) {
      this.mostrarLoading({
        titulo: "Recuperando datos...",
        color: "success"
      });
      try {
        let arrayFecha = dia.split(["-"]);
        let nuevoDia =
          arrayFecha[2] + "-" + arrayFecha[1] + "-" + arrayFecha[0];
        let datos = await axios.get(
          `https://mindicador.cl/api/dolar/${nuevoDia}`
        );
        this.valor = await datos.data.serie[0].valor;
      } catch (error) {
        this.valor = "Sin datos";
      } finally {
        this.ocultarLoading();
      }
    },
    ...mapMutations(["mostrarLoading", "ocultarLoading"])
  },
  created() {
    this.getDolar(this.fecha);
  }
};
</script>
