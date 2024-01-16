<template>
  <main>
    <h1 class="text-4xl font-black tracking-wide text-center">HEALTY TRACKER</h1>
    <div v-show="showpanel" class="grid grid-cols-1 md:grid-cols-12 gap-4">
      <div class="col-span-12 md:col-span-9">
        <div class="grid grid-cols-1 md:grid-cols-12 gap-4">
       
          <div class="col-span-12 md:col-span-12">
            <div
              style="max-height: 155px;padding: 1rem;border-radius: 10px;"
              :style="{ border: '2px solid ' + getColorAndMessageForImc(imc), color: getColorAndMessageForImc(imc)}"
            >
              <div>
                <span style="font-size: 1rem; font-weight: 600"
                  >{{ msgImc }}
                </span>
              </div>
            </div>
          </div>

          <div class="col-span-12 flex justify-end md:hidden">
            <button type="button" class="bg-indigo-500 text-white diplay-block rounded p-3" @click="showForm()">Update Data</button>
          </div>

          <div class="col-span-12 md:col-span-6">
            <div class="box" style="max-height: 135px">
              <label for="">NAME</label>
              <div>
                <span style="font-size: 2rem; font-weight: 600"
                  >{{ nameInput }}
                </span>
              </div>
            </div>
          </div>

          <div class="col-span-12 md:col-span-3">
            <div class="box" style="max-height: 135px">
              <label for="">SEX</label>
              <div>
                <span style="font-size: 2rem; font-weight: 600">{{
                  sexInput
                }}</span>
              </div>
            </div>
          </div>

          <div class="col-span-12 md:col-span-3">
            <div class="box" style="max-height: 135px">
              <label for="">AGE</label>
              <div>
                <span style="font-size: 2rem; font-weight: 600"
                  >{{ ageInput }}
                  <span style="font-size: 0.8rem">years</span></span
                >
              </div>
            </div>
          </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-12 gap-4 mt-4">
          <div class="col-span-12 md:col-span-6">
            <div class="grid grid-cols-1 md:grid-cols-12 gap-4 mt-4">
              <div class="col-span-12 md:col-span-6">
                <div class="box" style="height: 150px">
                  <label for="">HEIGHT</label>
                  <div class="flex justify-center items-center min-h-full">
                    <span style="font-size: 3.5rem; font-weight: 600"
                      >{{ heightInput }}
                      <span style="font-size: 0.8rem">cm</span></span
                    >
                  </div>
                </div>
              </div>

              <div class="col-span-12 md:col-span-6">
                <div class="box" style="height: 150px">
                  <label for="">WEIGHT</label>
                  <div class="flex justify-center items-center min-h-full">
                    <span style="font-size: 3.5rem; font-weight: 600"
                      >{{ weightInput }}
                      <span style="font-size: 0.8rem">kg</span></span
                    >
                  </div>
                </div>
              </div>

              <div class="col-span-12 hidden md:block">
                <div class="box" v-if="weights && weights.length > 0">
                  <label>LAST 7 DAYS</label>
                  <div class="canvas-box">
                    <canvas ref="weightChartEl"></canvas>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="col-span-12 md:col-span-6">
            <div class="box mb-4">
              <label>BODY MASS INDEX</label>
              <div class="flex justify-center content-center">
                <div
                  class="current"
                  :style="{
                    border: '5px solid ' + getColorAndMessageForImc(imc),
                  }"
                >
                  <span>{{ imc.toFixed(2) }}</span>
                  <small>IMC</small>
                </div>
              </div>
            </div>

            <div class="box">
              <label>BODY FAT INDEX</label>
              <div class="flex justify-center content-center">
                <div
                  class="current"
                  :style="{
                    border: '5px solid ' + getColorAndMessageForImc(imc),
                  }"
                >
                  <span v-show="sexInput == 'Male'">{{
                    igcMen.toFixed(2)
                  }}</span>
                  <span v-show="sexInput == 'Female'">{{
                    igcWomen.toFixed(2)
                  }}</span>
                  <small>IGC</small>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-span-12 md:col-span-3">
        <div class="box hidden md:block">
          <form @submit.prevent="addWeight">
            <div class="input-group">
              <label for="">NAME</label>
              <input
                type="text"
                v-model="nameInput"
                placeholder="Ingresa tu edad"
              />
            </div>
            <div class="input-group">
              <label for="">SEX</label>
              <select name="" id="" v-model="sexInput">
                <option value="Male">MALE</option>
                <option value="Female">FEMALE</option>
              </select>
            </div>
            <div class="input-group">
              <label for="">AGE</label>
              <input
                type="number"
                v-model="ageInput"
                placeholder="Ingresa tu edad"
              />
            </div>

            <div class="input-group">
              <label for="">WEIGHT(kg)</label>
              <input
                type="number"
                v-model="weightInput"
                placeholder="Ingreso tu peso en kg"
                step="0.1"
              />
            </div>
            <div class="input-group">
              <label for="">HEIGHT (cm)</label>
              <input
                type="number"
                v-model="heightInput"
                placeholder="Ingresa tu altura en cm"
              />
            </div>

            <input
              type="submit"
              class="bg-indigo-500 text-white diplay-block"
              value="REGISTER"
            />
          </form>
        </div>

        <div class="box weight-history mt-6">
          <label>WEIGHT HISTORY</label>
          <ul>
            <li v-for="(weight, index) in filteredWeights" :key="index">
              <span>
                <span style="font-size: 6px">WEIGHT</span>
                {{ weight.weight }}
              </span>
              <span>
                <span style="font-size: 6px">HEIGHT</span>
                {{ weight.height }}
              </span>
              <span>
                <span style="font-size: 6px">IMC</span>
                {{ weight.imc.toFixed(2) }}
              </span>
              <span>
                <span style="font-size: 6px">DATE</span>
                {{ new Date(weight.date).toLocaleDateString() }}
              </span>
            </li>
          </ul>
          <div class="flex justify-between mt-3">
            <button
              class="text-xs"
              @click.prevent="showLess"
              v-if="currentPage > 0"
            >
              Atras
            </button>
            <button
              class="text-xs"
              @click.prevent="showMore"
              v-if="weights.length > (currentPage + 1) * 3"
            >
              Siguiente
            </button>
          </div>
        </div>
      </div>
    </div>
    <div v-show="!showpanel" class="flex justify-center items-center">
      <div class="box" style="width: 400px">
        <form @submit.prevent="addWeight">
          <div class="input-group">
            <label for="">NAME</label>
            <input
              type="text"
              v-model="nameInput"
              placeholder="Ingresa tu edad"
            />
          </div>
          <div class="input-group">
            <label for="">SEX</label>
            <select name="" id="" v-model="sexInput">
              <option value="Male">MALE</option>
              <option value="Female">FEMALE</option>
            </select>
          </div>
          <div class="input-group">
            <label for="">AGE</label>
            <input
              type="number"
              v-model="ageInput"
              placeholder="Ingresa tu edad"
            />
          </div>

          <div class="input-group">
            <label for="">WEIGHT(kg)</label>
            <input
              type="number"
              v-model="weightInput"
              placeholder="Ingreso tu peso en kg"
              step="0.1"
            />
          </div>
          <div class="input-group">
            <label for="">HEIGHT (cm)</label>
            <input
              type="number"
              v-model="heightInput"
              placeholder="Ingresa tu altura en cm"
            />
          </div>

          <input
            type="submit"
            class="bg-indigo-500 text-white diplay-block"
            value="REGISTER"
          />
        </form>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, shallowRef, computed, watch, nextTick } from "vue";
import { Chart } from "chart.js/auto";

const weights = ref([]);

const displayCount = ref(7);

const weightChartEl = ref(null);

const weightChart = shallowRef(null);

const nameInput = ref("Alex Gutierrez");
const sexInput = ref("Male");
const weightInput = ref(80.0);
const heightInput = ref(170.0);
const ageInput = ref(24);
const showpanel = ref(false);

const currentHeight = ref(0);

const currentPage = ref(0);

// Computed property para filtrar los pesos a mostrar
const filteredWeights = computed(() => {
  const startIndex = currentPage.value * 3;
  const endIndex = (currentPage.value + 1) * 3;
  return weights.value.slice(startIndex, endIndex);
});

// Función para mostrar formulario
const showForm = () => {
  showpanel.value = false;

};

// Función para mostrar más registros
const showMore = () => {
  if (weights.value.length > (currentPage.value + 1) * 3) {
    currentPage.value += 1;
  }
};

// Función para mostrar menos registros
const showLess = () => {
  if (currentPage.value > 0) {
    currentPage.value -= 1;
  }
};

const msgImc = ref("");

const colorsImc = {
  0: "#3ba3de",
  1: "#64c4fc",
  2: "#DEBD36",
  3: "#F7BD35",
  4: "#E28036",
  5: "#d2242b",
};

const messageImc = {
  0: "Underweight - Your weight is below the healthy range. It's important to consult a healthcare professional.",
  1: "Normal Weight - Your weight falls within the healthy range for your height. Maintain a healthy lifestyle!",
  2: "Overweight - You have excess weight relative to your height. It's recommended to make dietary changes and increase physical activity.",
  3: "Obesity Grade 1 - You have a moderate level of obesity. Consult a healthcare professional for guidance on improving your health.",
  4: "Obesity Grade 2 - You have severe obesity, which can significantly impact your health. Seek medical attention and make lifestyle changes.",
  5: "Obesity Grade 3 - Grade 3 obesity is the most severe form and can be dangerous to your health. Consult a doctor urgently."
};

const getColorAndMessageForImc = (imc) => {
  let color, message;
  if (imc < 18.5) {
    color = colorsImc[0];
    message = messageImc[0];
  } else if (imc >= 18.5 && imc <= 24.9) {
    color = colorsImc[1];
    message = messageImc[1];
  } else if (imc >= 25 && imc <= 29.9) {
    color = colorsImc[2];
    message = messageImc[2];
  } else if (imc >= 30 && imc <= 34.9) {
    color = colorsImc[3];
    message = messageImc[3];
  } else if (imc >= 35 && imc <= 39.9) {
    color = colorsImc[4];
    message = messageImc[4];
  } else {
    color = colorsImc[5];
    message = messageImc[5];
  }

  msgImc.value = message; // Asigna el mensaje al ref msgImc
  return color;
};

const currentWeight = computed(() => {
  return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 };
});

const igcMen = computed(() => {
  if (ageInput.value === 0) return 0;
  return 1.2 * imc.value + 0.23 * ageInput.value - 16.2;
});

const igcWomen = computed(() => {
  if (ageInput.value === 0) return 0;
  return 1.2 * imc.value + 0.23 * ageInput.value - 5.4;
});

const imc = computed(() => {
  if (heightInput.value === 0) return 0;
  // Usa currentWeight.value.weight para acceder al valor del peso
  return weightInput.value / (heightInput.value / 100) ** 2;
});

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    height: heightInput.value,
    imc: imc.value,
    date: new Date().getTime(),
  });
};

watch(
  weights,
  (newValue) => {
    console.log(newValue.length);

    if (newValue.length > 0) {
      showpanel.value = true;
    }
  },
  { deep: true }
);

watch(
  weights,
  (newWeights) => {
    const ws = [...newWeights];

    if (weightChart.value) {
      weightChart.value.data.labels = ws
        .sort((a, b) => a.date - b.date)
        .map((w) => new Date(w.date).toLocaleDateString())
        .slice(-7);

      weightChart.value.data.datasets[0].data = ws
        .sort((a, b) => a.date - b.date)
        .map((w) => w.weight)
        .slice(-7);

      weightChart.value.update();

      return;
    }
    nextTick(() => {
      weightChart.value = new Chart(weightChartEl.value.getContext("2d"), {
        type: "line",
        data: {
          labels: ws
            .sort((a, b) => a.date - b.date)
            .map((w) => new Date(w.date).toLocaleDateString()),
          datasets: [
            {
              label: "Weight",
              data: ws.sort((a, b) => a.date - b.date).map((w) => w.weight),
              backgroundColor: "rgba(255, 105, 180, 0.2 )",
              borderColor: "rgb(255, 105, 180)",
              borderWidth: 1,
              fill: true,
            },
          ],
        },
      });
    });
  },
  { deep: true }
);
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;700;800&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}

body {
  background-color: #eee;
}

main {
  padding: 1.5rem;
}

h1 {
  margin-bottom: 2rem;
}

h2 {
  margin-bottom: 1rem;
  color: #888;
  font-weight: 400;
}

.current {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  width: 225px;
  height: 225px;

  text-align: center;
  background-color: white;
  color: #464646;
  border-radius: 999px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: 5px solid hwb(330 41% 0%);
}

.current span {
  display: block;
  font-size: 2.5em;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

input {
  border: 1px solid gray;
  padding: 0.5rem;
  border-radius: 6px;
}

select {
  border: 1px solid gray;
  padding: 0.5rem 1rem;
  border-radius: 6px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

label {
  font-weight: 600;
  font-size: 13px;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.current small {
  color: #888;
}

.canvas-box {
  width: 100%;
  max-width: 720px;
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.weight-history ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.weight-history ul li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  cursor: pointer;
}

.weight-history ul li:nth-child(even) {
  background-color: #dfdfdf;
}

.weight-history ul li:hover {
  background-color: #f8f8f8;
}

.weight-history ul li:last-of-type {
  border-bottom: none;
}

.weight-history ul li span {
  display: block;
  font-size: 1.25rem;
  font-weight: 700;
  margin-right: 1rem;
}

.weight-history ul li small {
  color: #888;
  font-style: italic;
}

.box {
  border: 1px solid #edebeb;
  padding: 1rem;
  border-radius: 20px;
  box-shadow: 0px 0px 20px 1px rgba(0, 0, 0, 0.1);
}
</style>
