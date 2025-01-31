<script setup>
  import { ref, computed, watch } from 'vue';
  import Header from './components/Header.vue';
  import Button from './components/Button.vue';
  import { calcularTotalPagar } from './helpers';

  //Definir el state
  const cantidad = ref(10000);
  const meses = ref(6)
  const total = ref(0)

  //Variables
  const MIN = 0;
  const MAX= 20000;
  const STEP= 100;

  //Al ser computed usaremos javascript normal y el state estar al tanto de los cambios
  const formatearDinero = (valor) =>{
    const formatter = new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD'
    });

    return formatter.format(valor)
  }

  watch([cantidad, meses], () =>{
    //Al cambiar la cantidad y al cambiar los meses...
    total.value = calcularTotalPagar(cantidad.value, meses.value)
  })

  const pagoMensual = computed(() =>{
    return total.value / meses.value;
  })

  const handleChangeDecremento = () =>{
    const valor = cantidad.value - STEP
    if(valor < MIN){
      alert('Cantidad no es valida');
      return
    }

    cantidad.value = valor;
  }
  
  const handleChangeIncremento = () =>{
    const valor = cantidad.value + STEP
    if(valor > MAX){
      alert('Cantidad no es valida');
      return
    }

    cantidad.value = valor;
  }

</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header/>

    <div class="flex justify-between mt-10">
        <Button
          :operador="'-'"
          :fn="handleChangeDecremento"
        />
        <Button
          :operador="'+'"
          :fn="handleChangeIncremento"
        />
    </div>

    <div class="my-5">
        <input 
          :min="MIN"
          :max="MAX"
          :step="STEP"
          type="range"
          class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
          v-model.number="cantidad" 
        />

        <p class="text-center my-10 text-5xl font-extrabold text-indigo-600"> {{ formatearDinero(cantidad) }}</p>

        <h2 class="text-2xl font-extrabold text-gray-500 text-center">
            Elige un <span class="text-indigo-600">Plazo</span> a pagar
        </h2>

        <select class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center
         text-xl font-bold text-gray-500 mt-5" :value="meses" v-model.number="meses">
          <option value="6">6 Meses</option>
          <option value="12">12 Meses</option>
          <option value="24">24 Meses</option>
        </select>
    </div>

    <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5">
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Resumen <span class="text-indigo-600">de Pagos</span>
      </h2>

      <p class="text-xl text-gray-500 text-center font-bold">{{ meses }} Meses</p>
      <p class="text-xl text-gray-500 text-center font-bold">Total a Pagar: {{ formatearDinero(total) }} </p>
      <p class="text-xl text-gray-500 text-center font-bold">Mensualidad a Pagar: {{ formatearDinero(pagoMensual) }} </p>
    </div>

    <p v-else class="text-center text-xl font-bold text-indigo-600">Añade una cantidad y un plazo a pagar</p>
  </div>
</template>
