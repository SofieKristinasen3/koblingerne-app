<script setup>

import {ref, computed} from 'vue'; 
import GetTrainsComponent from './components/getTrainsComponent.vue';
import GetFuelComponent from './components/getFuelComponent.vue';
import getCarsComponent from './components/getCarsComponent.vue';
import GetExtrasComponent from './components/getExtrasComponent.vue';

const trainsWeight = ref(0); 
const fuelWeight = ref(0); 
const carsWeight = ref(0); 
const extraWeight = ref(0); 


const totalWeight = computed(() => (trainsWeight.value + fuelWeight.value + carsWeight.value + extraWeight.value)); 
 const iconCountWeight = computed(() => {
  const step = 3.5;
  return Math.min(5, Math.floor(totalWeight.value / step));  
 }); 


 //Håndterer opdateringerne fra emit når der bliver trykket på knapperne
 const handleTrainsWeight = (weight) => {
  trainsWeight.value = weight;
 }; 

 const handleFuelWeight = (weight) => {
  fuelWeight.value = weight;
 }; 

 const handleCarsWeight = (weight) => {
  carsWeight.value = weight;
 }; 

 const handleExtraWeight = (weight) => {
  extraWeight.value = weight;
 }; 

const trainsSpeed = ref(0); 
const fuelSpeed = ref(0); 
const carsSpeed = ref(0); 
const extraSpeed = ref(0); 

const totalSpeed = computed(() => (trainsSpeed.value + fuelSpeed.value + carsSpeed.value + extraSpeed.value)); 
 const iconCountSpeed = computed(() => {
  const step = 2;
  return Math.min(5, Math.floor(totalSpeed.value / step));  
 }); 

 //Håndterer opdateringerne fra emit når der bliver trykket på knapperne
 const handleTrainsSpeed = (speed) => {
  trainsSpeedSpeed.value = speed;
 }; 

 const handleFuelSpeed = (speed) => {
  fuelSpeed.value = speed;
 }; 

 const handleCarsSpeed = (speed) => {
  carsSpeed.value = speed;
 }; 

 const handleExtraSpeed = (speed) => {
  extraSpeed.value = speed;
 }; 

 
//RESULTATSIDE
const totalPoints = computed (() => totalWeight.value + totalSpeed.value * 100);
const isModalOpen = ref(false)

function openModal() {
  isModalOpen.value = true
}

function closeModal() {
  isModalOpen.value = false
}

// Genstartsknap
const restart = () => {
  trainsWeight.value = 0;
  fuelWeight.value = 0;
  carsWeight.value = 0;
  extraWeight.value = 0;

  trainsSpeed.value = 0;
  fuelSpeed.value = 0;
  carsSpeed.value = 0;
  extraSpeed.value = 0;

  isModalOpen.value = false;
};

</script>

<template>
  <div class="stats">
    <div class="display-train-total"></div>
    <div class="stats-icons">
      <div class="icons">
      <h2>VÆGT</h2>
      <img v-for="n in 5"
      :key="n"
      src="./components/icons/weight.svg" alt=""
      :style="{opacity: n <= iconCountWeight ? 1 : 0.2}"
      width ="50">
    </div>

    <div class="icons">
      <H2>FART</H2>
      <img v-for="n in 5"
      :key="n"
      src="./components/icons/speed.svg" alt=""
      :style="{opacity: n <= iconCountSpeed ? 1 : 0.2}"
      width ="50">
    </div>
    </div>
    <div class="stats-buttons">
       <button @click="openModal"> TEST</button>
       <button @click="restart">Prøv igen</button>
    </div>
   
  </div>

<!-- RESULTAT modal -->
    <div v-if="isModalOpen" class="modal-overlay">
      <div class="modal-content">
        <h2> Du har kørt {{ totalPoints }} kilometer!</h2>
        <button @click="restart">Prøv igen</button>
      </div>
    </div>

<!-- Selector elementer -->
  <div class="trainpart-selection-element">
    <GetTrainsComponent @update-speed="handleTrainsSpeed" @update-weight="handleTrainsWeight"/>
    <GetFuelComponent @update-speed="handleFuelSpeed" @update-weight="handleFuelWeight"/>
    <getCarsComponent @update-speed="handleCarsSpeed" @update-weight="handleCarsWeight"/>
    <GetExtrasComponent @update-speed="handleExtraSpeed" @update-weight="handleExtraWeight"/>
  </div>
</template>