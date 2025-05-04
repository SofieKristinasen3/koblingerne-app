<script setup>
import { ref, watch } from 'vue'; 

const cars = ref([]); 
const currentIndex = ref(0); 
const emit = defineEmits(['update-weight', 'update-speed'])

const getCars = () => {
  fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/cars.json')
    .then((res) => res.json())
    .then((res) => {
      const carsKeys = Object.keys(res); 
      for (let i in carsKeys) {
        cars.value.push({
          ...res[carsKeys[i]],
          id: carsKeys[i],
        });
      }

      // Emit initial weight
      if (cars.value.length > 0) {
        emit('update-weight', cars.value[currentIndex.value].carsWeight)
        emit('update-speed', cars.value[currentIndex.value].carsSpeed); 
      }
    })
    .catch((err) => {
      console.error(err);
    });
};

getCars();

watch(currentIndex, (newIndex) => {
  if (cars.value.length > 0) {
    emit('update-weight', cars.value[newIndex].carsWeight);
    emit('update-speed', cars.value[currentIndex.value].carsSpeed)
  }
});

const nextBtn = () => {
  currentIndex.value = (currentIndex.value + 1) % cars.value.length;
};

const previousBtn = () => {
  currentIndex.value =
    (currentIndex.value - 1 + cars.value.length) % cars.value.length;
};
</script>

<template>
  <div v-if="cars.length > 0">
    <img src="./icons/up-arrow.svg" alt="" @click="previousBtn" class="arrow">
    <img :src="cars[currentIndex].imgUrl" alt="Vognbillede">
    <img src="./icons/down-arrow.svg" alt="" @click="nextBtn" class="arrow">
    
  </div>
</template>
