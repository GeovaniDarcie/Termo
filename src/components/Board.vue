<template>
  <div id="board">
      <h3>TERMO</h3>
      <div class="container-squad" v-for="attempt in attempts" :key="attempt">
        <div class="squad" v-for="squad in squads" :key="squad" :ref="squad">
        </div>
      </div>
      <h3 v-show="endSucess">Você venceu!</h3>
      <h3 v-show="endLost">Você perdeu! =( </h3>
      <h3 v-show="endLost">A palavra era <span class="corret"> {{ word.toUpperCase() }} </span> </h3>
  </div>
</template>

<script>
import words from '../constants/wordlist.js';
import validguest from '../constants/validguest.js'
import 'animate.css';

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
      correct: 0,
      colors: [],
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
      return this.row >= 6 && this.correct != 5;
    },
  },
  mounted() {
    window.addEventListener('keydown', this.typing)
    const position = Math.floor(Math.random() * 1465)
    this.word = this.wordsList[position]
    console.log(this.word)
    this.word = this.word.normalize('NFD').replace(/[\u0300-\u036f]/g, '');
  },
  props: {
    keypress: Object
  },
  methods: {
    typing(keyboard) {
      if (!this.endSucess && !this.endLost) {
        if ((/[a-zA-Z]/).test(keyboard.key) && keyboard.key.length == 1 && this.$refs[this.column]) {
          this.letterPress(keyboard);
        }
  
        if (keyboard.key == 'Backspace' && this.column >= 1 && this.row >= 0) {
          this.backSpacePress();
        }
  
        if (keyboard.key == 'Enter') {
          if (this.value.length === 5) {
            if (validguest.join('').includes(this.value.join(''))) {
              const mapValues = {};
              this.value.forEach((v, i) => {
                if (mapValues[v]) {
                   mapValues[v].letters.push(i)
                } else {
                  mapValues[v] = { letters: [], colors: [] }
                  mapValues[v].letters.push(i)
                }
              })
          
              const map = {}
  
              this.word.split('').forEach((w, index) => {
                if (map[w]) {
                  map[w].push(index)
                } else {
                  map[w] = []
                  map[w].push(index)
                }
              })
  
              for(let [key, values] of Object.entries(map)) {
                values.forEach(position => {
                  let letter = this.value[position]
  
                  if (letter == key) {
                    this.tint(position, '#3aa394')
                    this.value[position] = ''
                    this.correct++;
                  } else if (this.word.includes(key)) {
                    let index = this.value.findIndex(v => v == key)
                    if (index != -1) {
                      this.tint(index, '#d3ad69')
                    }
                  }
                })
              }
  
              for(let i=1; i<=5; i++) {
                this.$refs[i][this.row].classList.add('shake')
                if (this.$refs[i][this.row].style.background == '') {
                  this.$refs[i][this.row].style.background = '#312a2c'
                
                  this.colors.push('#594B4F')
                } else {
                  this.colors.push(this.$refs[i][this.row].style.background)
                }
              }
  
              for(let [, values] of Object.entries(mapValues)) {
                values.letters.forEach(position => {
                  values.colors.push(this.colors[position])
                })
              }
  
              const mapColors = {}
              for(let [key, values] of Object.entries(mapValues)) {
                const green = values.colors.filter(c => c == 'rgb(58, 163, 148)')
                const yellow = values.colors.filter(c => c == 'rgb(211, 173, 105)')
  
                if (green.length > 0) {
                  mapColors[key] = 'rgb(58, 163, 148)'
                } else if (yellow.length > 0) {
                  mapColors[key] = 'rgb(211, 173, 105)'
                } else {
                  mapColors[key] = '#594B4F'
                }
              }
              this.$emit('actionBoard', mapColors)
  
              this.row++;
              this.column = 1;
              this.value = [];
              this.colors = [];
  
              if (this.correct < 5) {
                this.correct = 0;
              }
            } else {
              alert("Palavra inválida!!")
              for(let i=1; i<=5; i++) {
                if (this.$refs[i][this.row].innerText != '') {
                  this.$refs[i][this.row].innerText = '';
                }
              }
  
              this.column = 1;
              this.value = [];
              this.colors = [];
            }
          }
        }
      }
    },

    letterPress(keyboard) {
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
    },

    backSpacePress() {
      this.$refs[this.column][this.row].innerText = ''
      this.value.pop();
      this.column--;

      if (this.column == 0) {
        this.column = 1;
      }
    },

    tint(index, color) {
      this.$refs[index+1][this.row].style.background = color
    },
  },
  watch: {
    'keypress.random'() {
      let key = this.keypress.key
      if (key == '<==') {
        key = 'Backspace'
      }
      this.typing({ key })
    }
  }
}
</script>

<style scoped>
    h3 {
      text-align: center;
      font-family: Mitr,sans-serif;
      color: white;
      font-size: 2rem;
    }

    button {
      text-align: center;
      font-family: Mitr,sans-serif;
      color: white;
      font-size: 2rem;
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

    .corret {
      color: DarkRed;
    }

    .shake {
      animation: linear flip 0s forwards;
      animation-duration: 2s;
      animation-timing-function: linear;
      animation-delay: 0s;
      animation-iteration-count: 1;
      animation-direction: normal;
      animation-fill-mode: forwards;
      animation-play-state: running;
      animation-name: flip;
      transform: perspective(200px) rotateY(360deg);
    }

    @keyframes flip{
      0% {
        border: 0.125em solid #4C4347;
        transform: perspective(200px) rotateY(0deg);
      }

      25% {
        transform: perspective(200px) rotateY(90deg);
      }
      50% {
        border: 0.125em solid #4C4347;
        transform: perspective(200px) rotateY(180deg);
      }

      75% {
        border: none;
        transform: perspective(200px) rotateY(270deg);
      }

      100% {
        transform: perspective(200px) rotateY(360deg);
      }
    } 

</style>