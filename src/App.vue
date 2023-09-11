<script setup>
import { ref, computed } from "vue";

const temperature = ref(0);
const unit = ref("C");
const converted = ref(false);


const celsius = computed(() => {
  if (unit.value === "C") return temperature.value;
  else if (unit.value === "F") return (temperature.value - 32) * (5 / 9);
  else if (unit.value === "K") return temperature.value - 273.15;
  else if (unit.value === "R")
    return (temperature.value - 32 - 459.67) * (5 / 9);
});

const fahrenheit = computed(() => {
  if (unit.value === "C") return temperature.value * (9 / 5) + 32;
  else if (unit.value === "F") return temperature.value;
  else if (unit.value === "K") return temperature.value * (9 / 5) - 459.67;
  else if (unit.value === "R") return temperature.value - 459.67;
});

const kelvin = computed(() => {
  if (unit.value === "C") return temperature.value + 273.15;
  else if (unit.value === "F") return (temperature.value + 459.67) * (5 / 9);
  else if (unit.value === "K") return temperature.value;
  else if (unit.value === "R") return temperature.value * (5 / 9);
});

const rankine = computed(() => {
  if (unit.value === "C") return (temperature.value + 273.15) * (9 / 5);
  else if (unit.value === "F") return temperature.value + 459.67;
  else if (unit.value === "K") return temperature.value * (9 / 5);
  else if (unit.value === "R") return temperature.value;
});

const convertTemperature = () => {
  converted.value = true;
};

const reset = () => {
  temperature.value = 0;
  converted.value = false;
};

const isIcePoint = (unit, temperature) => {
  switch (unit) {
    case "C":
      return temperature === 0;
    case "F":
      return temperature === 32;
    case "K":
      return temperature === 273.15;
    case "R":
      return temperature === 491.67;
  }
};

const isSteamPoint = (unit, temperature) => {
  switch (unit) {
    case "C":
      return temperature === 100;
    case "F":
      return temperature === 212;
    case "K":
      return temperature === 373.15;
    case "R":
      return temperature === 671.67;
  }
};

const searchTerm = ref("");
const formulas = [
  {
    from: "CelsiustoFahrenheit",
    to: "Fahrenheit",
    formula: "F = C * 9/5 + 32",
  },
  {
    from: "Celsius",
    to: "Kelvin",
    formula: "K = C + 273.15",
  },
  {
    from: "Celsius",
    to: "Rankine",
    formula: "R = (C + 273.15) * 9/5",
  },
  {
    from: "Fahrenheit",
    to: "Celsius",
    formula: "C = (F - 32) * 5/9",
  },
  {
    from: "Fahrenheit",
    to: "Kelvin",
    formula: "K = (F + 459.67) * 5/9",
  },
  {
    from: "Fahrenheit",
    to: "Rankine",
    formula: "R = F + 459.67",
  },
  {
    from: "Kelvin",
    to: "Celsius",
    formula: "C = K - 273.15",
  },
  {
    from: "Kelvin",
    to: "Fahrenheit",
    formula: "F = K * 9/5 - 459.67",
  },
  {
    from: "Kelvin",
    to: "Rankine",
    formula: "R = K * 9/5",
  },
  {
    from: "Rankine",
    to: "Celsius",
    formula: "C = (R - 491.67) * 5/9",
  },
  {
    from: "Rankine",
    to: "Fahrenheit",
    formula: "F = R - 459.67",
  },
  {
    from: "Rankine",
    to: "Kelvin",
    formula: "K = R * 5/9",
  },
];

const filteredFormulas = computed(() => {
  return formulas.filter(
    (formula) =>
      formula.to.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
      formula.from.toLowerCase().includes(searchTerm.value.toLowerCase())
  );
});
</script>

<template>
  <div class="w-5/6 my-32 py-12 px-32 mx-32 flex flex-col bg-white rounded-2xl shadow-2xl">
    <h1 class="text-6xl">Temperature</h1>
    <div class="p-4 space-y-4">
      <div class="bg-white p-4 rounded-md shadow-md">
        <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="flex items-center mb-2">
            <label class="mr-2 font-medium">Temperature</label>
            <input type="number" v-model="temperature" @keyup.enter="convertTemperature"
              class="w-32 p-2 border border-gray-300 rounded-md" />
          </div>
          <div class="flex items-center mb-2">
            <label class="mr-2 font-medium">From</label>
            <button v-on:click="unit = 'C'" class="btn btn-outline btn-warning font-bold">
              Celsius
            </button>
            <button v-on:click="unit = 'F'" class="btn btn-outline btn-warning font-bold">
              Fahrenheit
            </button>
            <button v-on:click="unit = 'R'" class="btn btn-outline btn-warning font-bold">
              Rankine
            </button>
            <button v-on:click="unit = 'K'" class="btn btn-outline btn-warning font-bold">
              Kelvin
            </button>
          </div>
          <div class="btn-group">
            <button v-on:click="convertTemperature" class="btn btn-outline btn-success font-bold">
              Convert
            </button>
            <button v-on:click="reset" class="btn btn-outline btn-error font-bold">
              Reset
            </button>
          </div>
        </div>
        <div class="flex-1 mt-4 md:mt-0">
          <div v-if="converted" class="bg-gray-100 p-4 rounded-md shadow-md">
            <p class="mb-4">
              {{ celsius.toFixed(2) }} °C = {{ fahrenheit.toFixed(2) }} °F =
              {{ kelvin.toFixed(2) }} K = {{ rankine.toFixed(2) }} °R
            </p>
            <p v-if="isIcePoint(unit, temperature)" class="alert alert-info">
              At ice point!
            </p>
            <p v-if="isSteamPoint(unit, temperature)" class="alert alert-error">
              At steam point!
            </p>
          </div>
        </div>
      </div>
    </div>

    <label for="my-modal-5" class="btn font-bold bg-yellow-500">formula</label>
    <input type="checkbox" id="my-modal-5" class="modal-toggle" />
    <div class="modal">
      <div class="modal-box w-11/12 max-w-5xl">
        <div class="bg-white p-4 rounded-md shadow-md">
          <input type="text" v-model="searchTerm" placeholder="Search formulas"
            class="w-full p-2 border border-gray-300 rounded-md mb-4" />
          <div v-if="filteredFormulas.length > 0">
            <table class="table-fixed w-full">
              <thead>
                <tr class="bg-gray-200">
                  <th class="w-1/2 p-2 border border-gray-400">From</th>
                  <th class="w-1/2 p-2 border border-gray-400">Formula</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(formula, index) in filteredFormulas" :key="index">
                  <td class="p-2 border border-gray-400">
                    {{ formula.to }} to {{ formula.from }}
                  </td>
                  <td class="p-2 border border-gray-400">
                    {{ formula.formula }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <div v-else>
            <p>No results found.</p>
          </div>
        </div>
        <div class="modal-action">
          <label for="my-modal-5" class="btn bg-yellow-500 font-bold">ok</label>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>

</style>
