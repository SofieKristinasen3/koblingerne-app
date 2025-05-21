<script setup>

//Importerer ref og computed, computed bruges til at beregne ud fra de værdier vi giver den, og beregner automatisk en ny værdi når tallene ændres. 
// Ellers gemmes den gamle værdi i en cache, indtil der sker en ændring.
import {ref, computed} from 'vue'; 
import GetTrainsComponent from './components/getTrainsComponent.vue';
import GetFuelComponent from './components/getFuelComponent.vue';
import getCarsComponent from './components/getCarsComponent.vue';
import GetExtrasComponent from './components/getExtrasComponent.vue';

//variabler til at holde de forskellige værdier når de bliver sendt 
const trainsWeight = ref(0); 
const fuelWeight = ref(0); 
const carsWeight = ref(0); 
const extraWeight = ref(0); 

//alle værdierne der bliver modtaget fra komponenterne bliver lagt sammen til en totalvægt 
const totalWeight = computed(() => (trainsWeight.value + fuelWeight.value + carsWeight.value + extraWeight.value)); 
//opretter en computed værdi, der bliver gemt i iconCountWeight
 const iconCountWeight = computed(() => {
  const step = 3.5; //Hvor stor afstand der skal være mellem hvert ikon bliver lavet. der skal 3,5 enhed til, for at tjene ét ikon
  return Math.min(5, Math.floor(totalWeight.value / step));  //.floor gør at der bliver rundet ned til nærmeste heltal. 5 gør, at det er det maksimale antal steps der kan være - så selv hvis resultatet var over, er det det maks antal steps/ikoner der kan være. 
 }); 


 //Håndterer opdateringerne fra emit når der bliver trykket på knapperne, og gør at den nye vægt værdi bliver gemt i den rette variabel.
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

 //Gentag for hastighed 
const trainsSpeed = ref(0); 
const fuelSpeed = ref(0); 
const carsSpeed = ref(0); 
const extraSpeed = ref(0); 

const totalSpeed = computed(() => (trainsSpeed.value + fuelSpeed.value + carsSpeed.value + extraSpeed.value)); 
 const iconCountSpeed = computed(() => {
  const step = 3.5;
  return Math.min(5, Math.floor(totalSpeed.value / step));  
 }); 

 //Håndterer opdateringerne fra emit når der bliver trykket på knapperne
 const handleTrainsSpeed = (speed) => {
  trainsSpeed.value = speed;
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
const starsCount = computed(() => {
  const step = 350; 
  return Math.max(1, Math.min(5, Math.floor(totalPoints.value / step)));
}); 
const isModalOpen = ref(false) //Ved at skifte false/true vises modalen

function openModal() {
  isModalOpen.value = true
}

function closeModal() {
  isModalOpen.value = false
}

// Genstartsknap, sætter alle værdier til 0
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

//VIEWER - se sammensætningen af dit tog 
const trainViewer = ref('');
const extraViewer = ref(''); //Modtager URL fra emit og indsætter i variabel trainViewer

const handleTrainViewer = (imageUrl) => {
  trainViewer.value = imageUrl;
};
const handleExtraViewer = (imageUrl) => {
  extraViewer.value = imageUrl;
};

//Styrer hvad der kan ses på mobilversion 
const selectedTab = ref('train');


</script>

<template>
  <div class="stats">
    <div class="display-train-total" >
      <img v-if="trainViewer" :src="trainViewer" alt="Tog billede" class="stacked-image"/>
      <img v-if="extraViewer" :src="extraViewer" alt="Accessory" class="stacked-image"/>
    </div>

    <div class="stats-icons">
      <div class="weight">
      <!-- loop der genererer 5x af mit image tag. v-for starter loopet, n fungerer som tæller, og går op til 5. Key=n gør at vue ved hvor langt vi er-->
      <img v-for="n in 5" 
      :key="n" 
      src=".\components\icons\weight.png" alt=""
      :style="{opacity: n <= iconCountWeight ? 1 : 0.2}"
      width ="65">
    </div>
    <!-- style siger, at hvis n er mindre end eller lig med iconCountWeight, skal opaciteten være 1. Ellers skal de være 0.2. -->

    <div class="speed">
      <img v-for="n in 5"
      :key="n"
      src="./components/icons/speed.png" alt=""
      :style="{opacity: n <= iconCountSpeed ? 1 : 0.2} "
      width ="65">
    </div>
    </div>
    <div class="stats-buttons">
       <button @click="openModal" class="testBtn"><img src=".\components\icons\checkmark.svg" alt=""></button>
       <button @click="restart" class="restartBtn"><img src=".\components\icons\repeat.png" alt=""></button>
    </div>
    
  </div>

<!-- RESULTAT modal -->
    <div v-if="isModalOpen" class="modal-overlay">
      <div class="modal-content">
        <h3> DU HAR KØRT {{ totalPoints }} KILOMETER!</h3>
        <div class="stars">
          <img v-for="n in starsCount"
            :key="n"
            src="./components/icons/star.svg"
            alt=""
            width="100">
        </div>
        <button @click="closeModal"><img src=".\components\icons\repeat.png" alt="">
        </button>
      </div>
    </div>

<!-- Selector elementer -->
  <div class="trainpart-selection-element">
    <GetTrainsComponent @update-speed="handleTrainsSpeed" @update-weight="handleTrainsWeight" @update-image="handleTrainViewer"/>
    <GetFuelComponent @update-speed="handleFuelSpeed" @update-weight="handleFuelWeight"/>
    <getCarsComponent @update-speed="handleCarsSpeed" @update-weight="handleCarsWeight"/>
    <GetExtrasComponent @update-speed="handleExtraSpeed" @update-weight="handleExtraWeight" @update-image="handleExtraViewer"/>
  </div>

  
</template>