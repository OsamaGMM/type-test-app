<script setup>
import { ref, watch } from 'vue';

const sampleText = `Vue js est un framework JavaScript polyvalent pour la création d'interfaces utilisateur dynamiques connu pour sa simplicité et sa flexibilité.
`;
// const splitSampleText = sampleText.split("")

const userInput = ref('');
const result = ref('');
// console.log(splitSampleText);


function compareLetters(event) {
  const latestInput = event.target.value.slice(-1); 
  const currentIndex = userInput.value.length - 1;

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

  <input v-model="userInput" @input="compareLetters" id="user-input" type="text">

  <label for="user-input">
    <div class="sample-text">
      <div class="text-container">
      <span class="user-input" v-html="formattedUserInput"></span>
      <br>
      <span>{{ sampleText }}</span>
    </div>
    </div>
   </label>


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


</style>


