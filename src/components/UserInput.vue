<script setup>
import { ref, watch } from 'vue';

// Sample text for comparison
const sampleText = "Your lorem ipsum dolor sit amet";
// const splitSampleText = sampleText.split("")

const userInput = ref('');
const result = ref('');
// console.log(splitSampleText);

const wordsPerMinute = ref(null);

function compareLetters(event) {
  const latestInput = event.target.value.slice(-1); // Get the last character entered

  // Perform your comparison logic here
  const currentIndex = userInput.value.length - 1;
  if (latestInput === sampleText[currentIndex]) {
    result.value = { text: 'Correct!', color: 'green' };
  } else {
    result.value = { text: 'Incorrect.', color: 'red' };
  }
}

const formattedUserInput = ref('');

// Watch for changes in userInput and update formattedUserInput
watch(userInput, () => {
  formattedUserInput.value = highlightLetters();
});
function highlightLetters() {
  const formatted = [];
  for (let i = 0; i < Math.min(userInput.value.length, sampleText.length); i++) {
    if (userInput.value[i] === sampleText[i]) {
      formatted.push(`<span style="color: green">${userInput.value[i]}</span>`);
    } else {
      formatted.push(`<span style="color: red">${userInput.value[i]}</span>`);
    }
  }
  return formatted.join('');
}

</script>

<template>
<div class="user-input">
    <label for="user-input">Input here</label>
    <input v-model="userInput" @input="compareLetters" id="user-input" type="text">
    <p>User input: <span v-html="formattedUserInput"></span></p>

    <div class="sample-text">
      <p>Sample Text:</p>
      <pre>{{ sampleText }}</pre>
    </div>

    <div>
      <p>Typing Speed: {{ wordsPerMinute }} WPM</p>
    </div>
  </div>
</template>


