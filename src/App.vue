<script setup>
  import {ref,computed, watch} from "vue"
  import Header from "./components/Header.vue"
  import Button from "./components/Button.vue"
  import {calcularTotalPagar} from "./helpers"

  //states
  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0);

  //variables que no se cambiaran
  const MIN = 0,
  MAX = 20000,
  STEP = 500;

  const formatearDinero = (valor) => {
    const formatter = new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD'
    });
    return formatter.format(valor);
  };

  watch([cantidad, meses], () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value);
  });

  const pagoMensual = computed(() => {
    return total.value / meses.value;
  });

  const handleChangeDecremento = () => {
    const valor = cantidad.value - STEP;
    if(valor < MIN) {
      alert('Cantidad no valida');
      return;
    }
    cantidad.value = valor
  }

  const handleChangeIncremento = () => {
    const valor = cantidad.value + STEP;
    if(valor > MAX) {
      alert('Cantidad no valida');
      return;
    }
    cantidad.value = valor
  }

</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header />
    <div class="mt-10 flex justify-between">
      <Button
        :operador="'-'"
        @fn="handleChangeDecremento"
      />
      <Button
        :operador="'+'"
        @fn="handleChangeIncremento"
      />
    </div>
    <div class="my-5">
      <input
        type="range"
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
        :min="MIN"
        :max="MAX"
        :step="STEP"
        v-model.number="cantidad"
      />
      <p v-text="formatearDinero(cantidad)" class="text-indigo-600 text-center my-10 text-5xl font-extrabold"></p>
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Elige un <span class="text-indigo-600">Plazo</span> a pagar
      </h2>
      <select
        class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 outline-gray-300 mt-3"
        :value="meses"
        v-model.number="meses"
        >
        <option value="6">6 Meses</option>
        <option value="12">12 Meses</option>
        <option value="24">24 Meses</option>
      </select>
      <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 text-center">
        <h2 class="text-2xl font-extrabold text-gray-500 text-center">
          Resumen <span class="text-indigo-600">de pagos</span>
        </h2>
        <p class="text-xl text-gray-500 text-center font-bold"><span class="text-indigo-600">{{meses}}</span> Meses</p>
        <p class="text-xl text-gray-500 text-center font-bold"><span class="text-indigo-600">{{formatearDinero(total)}}</span> Total a pagar</p>
        <p class="text-xl text-gray-500 text-center font-bold"><span class="text-indigo-600">{{ formatearDinero(pagoMensual) }}</span> Mensales</p>
      </div>
      <p v-else class="text-center text-2xl text-gray-500 font-bold mt-5">Añade una <span class="text-indigo-600">Cantidad</span> o un <span class="text-indigo-600">Plazo</span> a pagar</p>
    </div>
  </div>
</template>
