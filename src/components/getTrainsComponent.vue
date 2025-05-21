<script setup>
//importerer ref, som gør at vue holder øje med hvis værdien ændre sig, og 
//opdaterer automatisk på siden hvor den er brugt. Med en let variabel vil vue ikke automatisk opdater UI, som den gør med en ref. 
//Watch bruges til at udføre kode når noget ændre sig, og bliver kaldt automatisk. 
import { ref, watch } from 'vue'; 

//tomt array til at holde værdier der bliver importeret senere
const trains = ref([]); 
//Bruges til at holde øje med hvor vi står i arrayet. Default plads 0
const currentIndex = ref(0); 
//defineEmits gør os i stand til at sende data "opad" til en parent file. 
//Her bliver det defineret, at den skal sende den data der er indeni "update-weight"
const emit = defineEmits(['update-weight', 'update-speed', 'update-image'])

//Funktion til at hente data fra firebase, her .trains specifikt  
const getTrains = () => {
  fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/trains.json')
  //Konverter indhentet data til json objekt   
  .then((res) => res.json())
  //når ovenstående er sket, sendes det videre til næste then 
    .then((res) => {
      //udtrækker alle ID'er (keys) fra firebase objektet
      const trainsKeys = Object.keys(res); 

      //Looper igennem alle keys der er blevet udtrukket, dvs alle trains objekter. Med trains.value.push bliver 
      //de sat ind i vores første trains array 
      for (let i in trainsKeys) {
        trains.value.push({
          ...res[trainsKeys[i]], //henter toget med det pågældende ID ift den plads [i] vi står på, og "pakker ud" med ... og kopiere ind i nyt objekt
        });
      }

      //Loopet ovenover går altså igennem alle togene indhentet fra firebase, henter hvert tog-data med trainsKeys,
      //opretter nyt objekt for dem alle, og propper dem ind i trains Arrayet fra start. 

      
      if (trains.value.length > 0) { //Hvis listen ikke er tom, udfør:
        emit('update-weight', trains.value[currentIndex.value].trainsWeight) //sender data op til parent komponent, med værdier for togvægt og hastighed
        emit('update-speed', trains.value[currentIndex.value].trainsSpeed); 
        emit('update-image', trains.value[currentIndex.value].viewerURL);

      }
    })
    //Fejlhåndtering
    .catch((err) => {
      console.error(err);
    });
};

getTrains();

//watch holder øje med current index, og kører funktionen når index ændrer sig, og opdaterer data 
watch(currentIndex, (newIndex) => {
  if (trains.value.length > 0) {
    emit('update-weight', trains.value[newIndex].trainsWeight);
    emit('update-speed', trains.value[currentIndex.value].trainsSpeed)
    emit('update-image', trains.value[currentIndex.value].viewerURL);

  }
});

//Funktion til at få knapperne til at loope gennem array og opdatere værdierne for hvert tog vi står på. 
// % dividerere currentIndex.value med trains.value.length, og tager "resten" dvs. 4/3, har en rest på 1. 
// Derfor vil index blive 1. Det gør altså, at når vi står på den sidste index i arrayet, at den starter forfra. 
const nextBtn = () => {
  currentIndex.value = (currentIndex.value + 1) % trains.value.length; 
};

//Samme som ovenover, men omvendt. Dog med et tillæg af trains.value.length, for at undgå negative tal - så virker modulus % ikke. 
const previousBtn = () => {
  currentIndex.value =
    (currentIndex.value - 1 + trains.value.length) % trains.value.length;
};

</script>

<template>
  <div v-if="trains.length > 0">
    <img src="./icons/up-arrow.png" alt="" @click="previousBtn" class="arrow">
     <img :src="trains[currentIndex].imgURL" alt="Togbillede">
    <img src="./icons/down-arrow.png" alt="" @click="nextBtn" class="arrow">
  </div>
</template>
