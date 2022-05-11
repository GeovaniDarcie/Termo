<template>
  <div id="board">
      <h3>TERMO</h3>
      <div class="container-squad" v-for="attempt in attempts" :key="attempt">
        <div class="squad" v-for="squad in squads" :key="squad" :ref="squad">
        </div>
      </div>
      <h3 v-show="endSucess">Você venceu!</h3>
      <h3 v-show="endLost">Você perdeu! =( </h3>
  </div>
</template>

<script>
import words from '../constants/wordlist.js';
import validguest from '../constants/validguest.js'

export default {
  name: 'board',
  data() {
    return {
      squads: 5,
      attempts: 6,
      value: [],
      column: 1,
      row: 0,
      word: '',
      correct: 0
    }
  },
  computed: {
    wordsList() {
      return words;
    },
    endSucess() {
      return this.correct == 5;
    },
    endLost() {
      console.log(this.correct)
      return this.row >= 6 && this.correct != 5;
    }
  },
  mounted() {
    window.addEventListener('keydown', this.typing)
    const position = Math.floor(Math.random() * 1465)
    this.word = this.wordsList[position]
    console.log(this.word)
  },
  methods: {
    typing(keyboard) {
      if ((/[a-zA-Z]/).test(keyboard.key) && keyboard.key.length == 1 && this.$refs[this.column]) {
        if (this.$refs[this.column][this.row].innerText == '') {
          this.$refs[this.column][this.row].innerText = keyboard.key;
          this.value.push(keyboard.key);
        } else {
          this.column++;
          if (this.$refs[this.column]) {
            this.$refs[this.column][this.row].innerText = keyboard.key;
            this.value.push(keyboard.key);
          } else {
            this.column = 5;
          }
        }
      }

      if (keyboard.key == 'Backspace' && this.column >= 1 && this.row >= 0) {
        this.$refs[this.column][this.row].innerText = ''
        this.value.pop();
        this.column--;

        if (this.column == 0) {
          this.column = 1;
        }
      }

      if (keyboard.key == 'Enter') {
        if (this.value.length === 5) {
          if (validguest.join('').includes(this.value.join(''))) {
            const word = this.word.split('');

            for(let i=0; i<5; i++) {
              if (this.value[i] == word[i]) {
                this.$refs[i+1][this.row].style.background = '#3aa394'
                this.correct++;
              } else if(this.word.includes(this.value[i])) {
                this.$refs[i+1][this.row].style.background = '#d3ad69'
              } else {
                this.$refs[i+1][this.row].style.background = '#312a2c'
              }

              this.value.splice(i, 1)
            }

            this.row++;
            this.column = 1;
            this.value = [];

            if (this.correct < 5) {
              this.correct = 0;
            }
          }
        }
      }
    }
  }
}
</script>

<style>
    h3 {
      text-align: center;
      font-family: Mitr,sans-serif;
      color: white;
      font-size: 1.5rem;
    }

    .container-squad {
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
    }

    .squad {
      display: flex;
      font-family: 'Mitr', sans-serif;
      justify-content: center;
      align-items: center;
      height: 53px;
      width: 53px;
      background-color: #615458;
      margin: 2px;
      border-radius: 5px;
      color: white;
      font-weight: 600;
      text-transform: uppercase;
      font-size: 1.6em;
    }

    input {
      border:none;
      background-image:none;
      background-color:transparent;
      -webkit-box-shadow: none;
      -moz-box-shadow: none;
      box-shadow: none;
    }
</style>