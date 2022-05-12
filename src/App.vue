<template>
  <div id="app">
    <board @actionBoard="actionBoard"/>
    <key-board :paint="paintLetters"/>
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
      paintLetters: {}
    }
  },
  methods: {
    actionBoard(payload) {
      this.paintLetters = payload
    }
  }
}
</script>

<style>
</style>
