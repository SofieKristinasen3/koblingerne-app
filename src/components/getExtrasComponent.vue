<script setup>
import { ref, watch } from 'vue'; 

const extra = ref([]); 
const currentIndex = ref(0); 
const emit = defineEmits(['update-weight', 'update-speed', 'update-image'])

const getExtra = () => {
  fetch('https://koblingerne-bf202-default-rtdb.europe-west1.firebasedatabase.app/extra.json')
    .then((res) => res.json())
    .then((res) => {
      const extraKeys = Object.keys(res); 
      for (let i in extraKeys) {
        extra.value.push({
          ...res[extraKeys[i]],
          id: extraKeys[i],
        });
      }

      // Emit initial weight
      if (extra.value.length > 0) {
        emit('update-weight', extra.value[currentIndex.value].extraWeight); 
        emit('update-speed', extra.value[currentIndex.value].extraSpeed);
        emit('update-image', extra.value[currentIndex.value].viewerURL); 
      }
    })
    .catch((err) => {
      console.error(err);
    });
};

getExtra();

watch(currentIndex, (newIndex) => {
  if (extra.value.length > 0) {
    emit('update-weight', extra.value[newIndex].extraWeight);
    emit('update-speed', extra.value[currentIndex.value].extraSpeed); 
    emit('update-image', extra.value[currentIndex.value].viewerURL); 
  }
});

const nextBtn = () => {
  currentIndex.value = (currentIndex.value + 1) % extra.value.length;
};

const previousBtn = () => {
  currentIndex.value =
    (currentIndex.value - 1 + extra.value.length) % extra.value.length;
};

</script>

<template>
  <div v-if="extra.length > 0">
    <img src="./icons/up-arrow.png" alt="" @click="previousBtn" class="arrow">
    <img :src="extra[currentIndex].imgUrl" alt="accessories">
    <img src="./icons/down-arrow.png" alt="" @click="nextBtn" class="arrow">
    
  </div>
</template>
