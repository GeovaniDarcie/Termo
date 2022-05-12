<template>
  <div id="app">
    <board @actionBoard="actionBoard" :keypress="keypress" />
    <key-board :paint="paintLetters" @tiping="tiping"/>
  </div>
</template>

<script>
import board from './components/Board.vue';
import KeyBoard from './components/KeyBoard.vue';

export default {
  name: 'App',
  components: {
    board,
    KeyBoard,
  },
  data() {
    return {
      paintLetters: {},
      keypress: {}
    }
  },
  methods: {
    actionBoard(payload) {
      this.paintLetters = payload
    },
    tiping(payload) {
      console.log(payload)
      this.keypress = payload;
    }
  }
}
</script>

<style>
</style>
