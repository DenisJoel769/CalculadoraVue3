<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-12 col-md-8">
        <q-card>
          <q-card-section class="bg-primary text-white">
            <div class="text-h6">Calculadora</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5 text-grey-5 text-right">
              {{ acumulador + actual }}
            </div>
            <div class="text-h3 text-right">{{ resultado }}</div>
          </q-card-section>

          <q-card-section class="bg-grey-4">
            <div class="row q-col-gutter-sm">
              <div class="col-3" v-for="(btn, index) in botones" :key="index">
                <q-btn
                  class="full-width text-h6"
                  :color="noesNumero(btn) ? 'indigo' : 'grey-2'"
                  :text-color="noesNumero(btn) ? 'white' : 'grey-8'"
                  @click="btnAccion(btn)"
                >
                  {{ btn }}
                </q-btn>
              </div>
              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="indigo"
                  @click="btnReset()"
                >
                  Reset
                </q-btn>
              </div>

              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="orange"
                  @click="btnResultado()"
                >
                  =
                </q-btn>
              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
      <pre>{{ arrayCalculos }}</pre>
    </div>
  </q-page>
</template>

<script>
// import { defineComponent } from '@vue/composition-api'
import { boolean, evaluate, round } from "mathjs";
const botones = [7, 8, 9, "%", 4, 5, 6, "+", 1, 2, 3, "-", ".", 0, "/", "*"];
import { ref } from "vue";
export default {
  setup() {
    //ref devuelve el tipo primitivo number string o boolean como un objeto reactivo
    const arrayCalculos = ref([]);
    const operadorClick = ref(true);
    const actual = ref("");
    const acumulador = ref("");
    const resultado = ref("");
    /*isNaN intenta convertir el parametro pasado a un numero, si el parametro pasado no se puede convertir
      devuelve true en caso contrario devuelve false
    */
    const noesNumero = (valor) => isNaN(valor);

    /*en este metodo realizamos las acciones si es numero al dar click se mostrara el numero,
      si operadorClick es false entonces muestra el numero y cambia de estado el operadorClick
    */
    const btnAccion = (valor) => {
      if (!noesNumero(valor)) {
        if (operadorClick.value) {
          actual.value = "";
          operadorClick.value = false;
        }
        actual.value = `${actual.value}${valor}`;
        console.log("Metodo de accion", actual.value);
      } else {
        ejecutarOperacion(valor);
      }
    };

    const ejecutarOperacion = (valor) => {
      if (valor === ".") {
        //buscamos el punto mediante el indexof si no encuentra devuelve el -1 y agrega los numeros
        if (actual.value.indexOf(".") === -1) {
          console.log("Metodo punto", actual.value);
          actual.value = `${actual.value}${valor}`;
        }
        return;
      }
      if (valor === "%") {
        if (actual.value !== "") {
          //si el valor es igual al porcentaje le divido por 100 directamente
          actual.value = `${parseFloat(actual.value) / 100}`;
        }
        return;
      }
      addOperador(valor);
    };

    const addOperador = (valor) => {
      if (!operadorClick.value) {
        acumulador.value += `${actual.value} ${valor}`;
        console.log("Acumulador", acumulador.value);
        actual.value = "";

        operadorClick.value = true;
      }
    };

    const btnReset = () => {
      acumulador.value = "";
      actual.value = "";
      resultado.value = "";
      operadorClick.value = true;
    };

    const btnResultado = () => {
      if (!operadorClick.value) {
        resultado.value = evaluate(acumulador.value + actual.value);

        arrayCalculos.value.push(
          `${acumulador.value} ${actual.value} = ${resultado.value}`
        );
        console.log(arrayCalculos.value);
      } else resultado.value = "Error!!";
    };

    return {
      botones,
      noesNumero,
      btnAccion,
      actual,
      btnReset,
      acumulador,
      resultado,
      btnResultado,
    };
  },
};
</script>
<style scoped>
.text-h5 {
  height: 20px;
}
</style>
