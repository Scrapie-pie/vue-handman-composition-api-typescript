<template>
  <GameHeader />
  <div class="game-container">
    <GameFigure :wrongLettersCount="wrongLetters.length" />
    <GameWrongLetters :wrongLetters="wrongLetters" />
    <GameWord :word="word" :correctLetters="correctLetters" />
  </div>
  <GamePopup ref="popup" :word="word" @restart="restart" />
 <GameNotification ref="notification" />
</template>

<script setup lang="ts">
import GameHeader from "@/components/GameHeader.vue";
import GameFigure from "@/components/GameFigure.vue";
import GameWrongLetters from "@/components/GameWrongLetters.vue";
import GameWord from "@/components/GameWord.vue";
import GamePopup from "@/components/GamePopup.vue";
import GameNotification from "@/components/GameNotification.vue";
import {ref, watch} from "vue";
import {useRandomWord} from "@/composables/useRandomWord";
import {useLetters} from "@/composables/useLetters";
import {useNotification} from "@/composables/useNotification";

const { word, getRandomWord } = useRandomWord()

const { letters, correctLetters, wrongLetters, isLose, isWin, addLetter, resetLetters } = useLetters(word)

const { notification, showNotification } = useNotification()
const popup = ref<InstanceType<typeof GamePopup> | null>(null)

const restart = async () => {
  await getRandomWord()
  resetLetters()
  popup.value?.close();

}

watch(wrongLetters, () => {
  if (isLose.value) {
    popup.value?.open('lose')
  }


  if (isWin.value) {
    popup.value?.open('win')
  }

})


window.addEventListener('keydown', ({ key }) => {
  if (isLose.value || isWin.value) {
    return
  }

  if (letters.value.includes(key)) {

    showNotification()

    return
  }

  addLetter(key)
})

</script>

<style>
* {
  box-sizing: border-box;
}

body {
  background-color: #ffffff;
  color: #3a3a3a;
  font-family: Arial, Helvetica, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 80vh;
  margin: 0;
  overflow: hidden;
}

#app {
  max-width: 450px;
  width: 100%;
  padding: 20px 30px;
  box-sizing: border-box;
}

h1 {
  margin: 20px 0 0;
}

.game-container {
  padding: 20px 0;
  position: relative;
  margin: auto;
  height: 350px;
}

.figure-container {
  fill: transparent;
  stroke: #3a3a3a;
  stroke-width: 4px;
  stroke-linecap: round;
}

.figure-part {
  display: none;
}

.wrong-letters-container {
  position: absolute;
  top: 20px;
  right: 20px;
  display: flex;
  flex-direction: column;
  text-align: right;
}

.wrong-letters-container p {
  margin: 0 0 5px;
}

.wrong-letters-container span {
  font-size: 24px;
}

.word {
  display: flex;
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
}

.letter {
  border-bottom: 3px solid #54bc6c;
  display: inline-flex;
  font-size: 30px;
  align-items: center;
  justify-content: center;
  margin: 0 3px;
  height: 50px;
  width: 20px;
}

.popup-container {
  background-color: rgba(0, 0, 0, 0.3);
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.popup {
  background: #54bc6c;
  border-radius: 5px;
  box-shadow: 0 15px 10px 3px rgba(0, 0, 0, 0.1);
  padding: 20px;
  text-align: center;
}

.popup h2,
.popup h3 {
  color: #fff;
}

.popup button {
  cursor: pointer;
  background-color: #fff;
  color: #54bc6c;
  border: 0;
  margin-top: 20px;
  padding: 12px 20px;
  font-size: 16px;
}

.popup button:active {
  transform: scale(0.98);
}

.popup button:focus {
  outline: 0;
}

.notification-container {
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 10px 10px 0 0;
  padding: 15px 20px;
  position: absolute;
  bottom: -50px;
  transition: transform 0.3s ease-in-out;
}

.notification-container p {
  margin: 0;
}

.notification-container.show {
  transform: translateY(-50px);
}
</style>