<script setup>
import { ref, watch } from 'vue'; 

const fuel = ref([]); 
const currentIndex = ref(0); 
const emit = defineEmits(['update-weight', 'update-speed'])

const getFuel = () => {
  fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/fuel.json')
    .then((res) => res.json())
    .then((res) => {
      const fuelKeys = Object.keys(res); 
      for (let i in fuelKeys) {
        fuel.value.push({
          ...res[fuelKeys[i]],
          id: fuelKeys[i],
        });
      }

      // Emit initial weight
      if (fuel.value.length > 0) {
        emit('update-weight', fuel.value[currentIndex.value].fuelWeight); 
        emit('update-speed', fuel.value[currentIndex.value].fuelSpeed); 
      }
    })
    .catch((err) => {
      console.error(err);
    });
};

getFuel();

watch(currentIndex, (newIndex) => {
  if (fuel.value.length > 0) {
    emit('update-weight', fuel.value[newIndex].fuelWeight);
    emit('update-speed', fuel.value[currentIndex.value].fuelSpeed); 
  }
});

const nextBtn = () => {
  currentIndex.value = (currentIndex.value + 1) % fuel.value.length;
};

const previousBtn = () => {
  currentIndex.value =
    (currentIndex.value - 1 + fuel.value.length) % fuel.value.length;
};

</script>

<template>
  <div v-if="fuel.length > 0">
    <img src="./icons/up-arrow.png" alt="" @click="previousBtn" class="arrow">
    <img :src="fuel[currentIndex].imgUrl" alt="accessories">
    <img src="./icons/down-arrow.png" alt="" @click="nextBtn" class="arrow">
    
  </div>
</template>
