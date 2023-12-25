<script setup>
import { ref, watch,computed } from 'vue';

const sampleText = `Vue js est un framework`;
const splitSampleText = sampleText.split(" ")

const activeWordIndex = ref(0);
const activeLettreIndex = ref(0);

const isActive = (index) => {
  return index === activeWordIndex.value;
};

const isActiveLetter = (wordIndex, letterIndex) => {
    return isActive(wordIndex) && letterIndex === activeLettreIndex.value;
};

const activeWordValue = computed(() => {
  // console.log("word is split", splitSampleText[activeWordIndex.value].split(""));
  const word = splitSampleText[activeWordIndex.value]
  const lettres = word.split("")
  const activeLettre = lettres[activeLettreIndex.value] 
  return {
    word,
    lettres,
    activeLettre,
  }
})

watch(activeWordIndex, (newIndex, oldIndex) => {
  // Check if the change in activeWordIndex is due to a user moving to the next word
  if (newIndex > oldIndex) {
    // Set activeLettreIndex to 0 when moving to a new word
    activeLettreIndex.value = 0;
  }
});

const userInput = ref('');
watch(userInput, () => {
//  console.log("userInput",userInput.value);
});


watch(activeWordValue, () => {
//  console.log("Active Letter:", activeWordValue.value.activeLettre);
});

watch(activeLettreIndex, () => {
//  console.log("Active Letter is changing:", activeLettreIndex.value);
});

const handleKeyDown = (event) => {
  if (event.key === 'Backspace') {
    if (activeLettreIndex.value === 0 && activeWordIndex.value > 0) {
      // Move to the previous word
      activeWordIndex.value -= 1;

      // Log the values for debugging
      console.log('Active Word Index:', activeWordIndex.value);
      console.log('Active Word Length:', splitSampleText[activeWordIndex.value].length);

      // Set activeLettreIndex to the last index of the new word
      activeLettreIndex.value = splitSampleText[activeWordIndex.value].length - 1;

      // Log the updated value for activeLettreIndex
      console.log('Updated Active Letter Index:', activeLettreIndex.value);
    } else if (activeLettreIndex.value > 0) {
      // Move to the previous letter within the same word
      activeLettreIndex.value -= 1;
    }
  }
};

const result = ref('');

function compareLetters(event) {
  if (event.data === ' ' || event.data === null) {
    return;
  }
  // Increment activeLettreIndex for other keys
  activeLettreIndex.value++;

  const latestInput = event.target.value.slice(-1); 
  const currentIndex = userInput.value.length - 1;
  // console.log("working " + latestInput);

  if (latestInput === sampleText[currentIndex]) {
    result.value = { text: 'Correct!', color: 'green' };
  } else {
    result.value = { text: 'Incorrect.', color: 'red' };
  }
}

const formattedUserInput = ref('');
watch(userInput, () => {
  formattedUserInput.value = highlightLetters();
});

function highlightLetters() {
  const formatted = [];
  for (let i = 0; i < Math.min(userInput.value.length, sampleText.length); i++) {
    if (userInput.value[i] === sampleText[i]) {
      formatted.push(`<span style="color: green">${userInput.value[i]}</span>`);
    } else {
      formatted.push(`<span style="color: red;">${userInput.value[i]}</span>`);
    }
  }
  return formatted.join('');
}

</script>

<template>
<div class="user-input-container">

  <!-- <input v-model="sampleText" id="user-input" type="text"> -->

  <textarea v-model="userInput" @input="compareLetters" @keyup.space="activeWordIndex++" @keydown="handleKeyDown"></textarea>

    <div class="sample-text">
      <div class="text-container" >

      <span v-for="(word, index) in splitSampleText" :key="index" :class="{'pop': isActive(index) }">
        <span v-if="isActive(index)">
          <span v-for="(letter, letterIndex) in activeWordValue.lettres" :key="letterIndex" :class="{'active-letter': isActiveLetter(index, letterIndex) }">
            {{ letter }}
          </span>
        </span>
        <span v-else>
          {{ word }}
        </span>
      </span>

      <h1>{{ activeWordValue.activeLettre }}</h1>
      <br>
    </div>
    </div>

   <div>   
    <h3>Fontionnalités futurs...</h3>
    <ul>
      <li>calculateur de mot par minute</li>
      <li>cursor pour savoir quelle lettre on doit "input"</li>
      <li>différents texts à écrire</li>
      <li>différents timers ex: 15s, 30s, 1min</li>
  
    </ul>
  </div>


</div>

</template>

<style>
.pop {
  background-color: yellow;
}

.active-letter {
  color: red; 
}

html{
  overflow: hidden;
  font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}
.sample-text{
  padding: 2rem;
  margin-left: 2rem;
}

.user-input-container{
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.text-container {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.user-input {
  white-space: nowrap; /* Prevents words from wrapping within themselves */
}

</style>


