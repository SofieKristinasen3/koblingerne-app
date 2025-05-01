<script setup>
import { ref, watch } from 'vue'; 

const trains = ref([]); 
const currentIndex = ref(0); 
const emit = defineEmits(['update-weight'])

const getTrains = () => {
  fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/trains.json')
    .then((res) => res.json())
    .then((res) => {
      const trainsKeys = Object.keys(res); 
      for (let i in trainsKeys) {
        trains.value.push({
          ...res[trainsKeys[i]],
          id: trainsKeys[i],
        });
      }

      // Emit initial weight
      if (trains.value.length > 0) {
        emit('update-weight', trains.value[currentIndex.value].trainsWeight)
        emit('update-speed', trains.value[currentIndex.value].trainsSpeed); 
      }
    })
    .catch((err) => {
      console.error(err);
    });
};

getTrains();

watch(currentIndex, (newIndex) => {
  if (trains.value.length > 0) {
    emit('update-weight', trains.value[newIndex].trainsWeight);
    emit('update-speed', trains.value[currentIndex.value].trainsSpeed); 
  }
});

const nextBtn = () => {
  currentIndex.value = (currentIndex.value + 1) % trains.value.length;
};

const previousBtn = () => {
  currentIndex.value =
    currentIndex.value <= 0 ? trains.value.length - 1 : currentIndex.value - 1;
};



</script>

<template>
  <div v-if="trains.length > 0">
    <button @click="previousBtn">Previous train</button>
    <img :src="trains[currentIndex].imgURL" alt="Togbillede">
    <button @click="nextBtn">Next train</button>
  </div>
</template>
