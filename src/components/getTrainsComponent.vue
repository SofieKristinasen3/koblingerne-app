<script setup>

import {ref} from 'vue'; 

const trains = ref([]); //Tomt array den indhentede data bliver sat ind i 
const currentIndex = ref(0); //For at tracke hvilken placering vi står på i arrayet 

const getTrains = () => {
    //Udsender et promise, der enten returnerer 200 eller med fejl. 
fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/trains.json', {
method: 'GET',
})
//Hvis 200, konverter data til .json formati
.then((res) => {
  return res.json();
})
//For hvert json object (key), tag indholdet og push til array. 
.then((res) => {
  const trainsKeys = Object.keys(res); 
  for (let i in trainsKeys) {
    trains.value.push({
      ...res[trainsKeys[i]],
      id: trainsKeys[i],
    });
  }
})
//Fejlhåndtering
.catch((err) => {
  console.error(err);
});
};

//kalder funktionen 
getTrains();

const nextBtn = () => {
    if (currentIndex.value >= trains.value.length -1) {
        currentIndex.value = 0; 
    } else {
        currentIndex.value++;
    }
};


const previousBtn = () => {
  if (currentIndex.value <= 0) {
    currentIndex.value = trains.value.length - 1;
  } else {
    currentIndex.value--;
  }
};


</script>

<template>
   <div v-if="trains.length > 0">
    <p>{{ trains[currentIndex].trainName }}</p>
    <button @click="nextBtn"> Next Train</button>
    <button @click="previousBtn"> Previous Train</button>
   </div>
</template>