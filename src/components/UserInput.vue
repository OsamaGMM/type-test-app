<script setup>
import { ref, watch,computed } from 'vue';

const sampleText = `Vue js est un framework`;
const spacer = '-';
const splitSampleText = sampleText.split(' ').flatMap((word, index, array) => index === array.length - 1 ? [word] : [word, spacer]);

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
  // console.log(word);
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
//  console.log("userInput", userInput.value);
});


watch(activeWordValue, () => {
  if (activeWordValue.value.activeLettre) {
    // console.log("still has letters", activeWordValue.value.word);
  }else{
    endOfWord()
    activeWordIndex.value += 1;
    activeLettreIndex.value = 0; // Set activeLettreIndex to the beginning of the spacer word
  }
});

watch(activeLettreIndex, () => {
 console.log("Active Letter is changing:", activeLettreIndex.value);
});

const handleKeyDown = (event) => {
  if (event.key === 'Backspace') {
    if (activeLettreIndex.value === 0 && activeWordIndex.value > 0) {
      // Move to the previous word
      activeWordIndex.value -= 1;
      // Set activeLettreIndex to the last index of the new word
      activeLettreIndex.value = splitSampleText[activeWordIndex.value].length - 1;
      console.log( activeLettreIndex.value);
    } else if (activeLettreIndex.value > 0) {
      // Move to the previous letter within the same word
      activeLettreIndex.value -= 1;
    }
  }

  if (event.key === ' ') {
    activeWordIndex.value++
    activeLettreIndex.value = 0; // Set activeLettreIndex to the beginning of the new word
  }
};

const result = ref({ text: '', class: '' });
console.log(result.value);


function compareLetters(event) {
  if (event.data === ' ' || event.data === null) {
    return;
  }

  const latestInput = event.target.value.slice(-1); 
  const activeLetter = activeWordValue.value.activeLettre; // Assuming this property name is correct

  const isCorrect = latestInput === activeLetter;

 if (isCorrect) {
    result.value = { text: 'Correct!', class: 'correct' };
    console.log(result.value);
  } else {
    result.value = { text: 'Incorrect.', class: 'incorrect' };
    console.log(result.value);
  }

  activeLettreIndex.value++;
}


function endOfWord() {
  const userInputValue = userInput.value;
  const activeWord = activeWordValue.value.word;

  console.log("User Input:", userInputValue);
  console.log("Active Word:", activeWord);

  if (userInputValue === activeWord) {
    // Save the word or perform any other actions
    console.log("Word Matched! Saving the word:", userInputValue);
    // Your saving logic goes here
  }else{
    console.log("error in the word:", userInputValue);
  }
}


</script>

<template>
<div class="user-input-container">

  <!-- <input v-model="sampleText" id="user-input" type="text"> -->

  <textarea v-model="userInput" @input="compareLetters" @keydown="handleKeyDown"></textarea>

    <div class="sample-text">
      <div class="text-container" >

      <span v-for="(word, index) in splitSampleText" :key="index" :class="{'pop': isActive(index) }">
        <span v-if="isActive(index)">
          <span v-for="(letter, letterIndex) in activeWordValue.lettres" :key="letterIndex"
           :class="{
            'active-letter': isActiveLetter(index, letterIndex),
          }"
           >
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
  color: blue; 
  position: relative;
}

.active-letter::after {
  content: "_";
  position: absolute;
  left: 50%;
  top: 5px;
  transform: translate(-50%, 0);
}

.correct {
  color: green;
}

.incorrect {
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
  align-items: center;
  gap: 0.5rem;
}

.user-input {
  white-space: nowrap; /* Prevents words from wrapping within themselves */
}

</style>


