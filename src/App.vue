<script setup>
import { ref } from 'vue';

/*Initializing the variables*/
const counter = ref(1);
const success = ref(false);
const error = ref(false);
const response = ref('');
const answer = ref('');

const loadNewGIF = (async () => {
  /*This function makes the request to the API and updates the variables to load the correct information in the template*/
  const request = await fetch('https://yesno.wtf/api');
  /*This verifies if the request was successful*/
  if (request.ok) {
    /*This is used to get the json object from the response (it is an asynchronous function ! We need to use await*/
    const data = await request.json();
    response.value = data;
    /*Just some formating on the answer to display*/
    answer.value = data.answer.toUpperCase();
    success.value = true;
    /*These last lines are used to change the page's background to the GIF we got with the request*/
    document.body.style.background = `url('${data.image}') no-repeat fixed center`;
    document.body.style.backgroundSize = 'cover';
  } else {
    /*In case the request failed, we don't display the last answer anymore*/
    success.value = false;
  }
});

const clickManager = (async () => {
  /*This function is used to manage the number of clicks needed to request a new GIF*/
  if (counter.value - 1 == 0) {
    /*As loadNewGIF is an asynchronous function, we need to use await*/
    await loadNewGIF();
    if (success.value) {
      /*If the request was successful, the number of times the user needs to press the button is defined randomly (between 1 and 5)*/
      error.value = false;
      counter.value = Math.ceil(Math.random()*4+1);
    } else {
      /*If there was an error, we display an error on the page and we reset the counter to 1*/
      error.value = true;
      counter.value = 1;
    }
  } else {
    counter.value = counter.value - 1;
  }
});
</script>

<template>
<div id="container">
  <div id="button-container">
    <button @click="clickManager">Load a GIF</button>
    <p>Please click {{ counter }} more times on the button to load a new GIF !</p>
  </div>

  <h4 v-if="success" id="answer">{{ answer }}</h4>
  <h4 v-if="error" id="error">An error occured ! We couldn't load a new GIF, please try again !</h4>
</div>
</template>

<style scoped>
@import '/src/assets/base.css';

#container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100vw;
}

#answer{
  text-align: center;
  align-self: center;
  font-size: 100px;
}

#error {
  display: block;
  width: 100vw;
  backdrop-filter: blur(3px);
  color: hsl(17.61,91.09%,39.61%);
  text-align: center;
  align-self: center;
  font-size: xx-large;
}

#button-container, #button-container button {
  justify-self:flex-end;
  font-size: large;
  display: flex;
  flex-direction: column;
  align-items: center;
}

#button-container button {
  color: var(--color-text);
  background-color: var(--color-background);
  border-radius: 10px;
}

#button-container p { 
  border-radius: 10px;
}
</style>
