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
      correct: 0,
      colors: []
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
  },
  props: {
    keypress: Object
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
              if (this.$refs[i][this.row].style.background == '') {
                  this.$refs[i][this.row].style.background = '#312a2c'
                  this.colors.push('#594B4F')
              } else {
                this.colors.push(this.$refs[i][this.row].style.background)
              }
            }



            this.$emit('actionBoard', { colors: this.colors, keys: this.value})

            this.row++;
            this.column = 1;
            this.value = [];
            this.colors = [];

            if (this.correct < 5) {
              this.correct = 0;
            }

           /*  for(let i=0; i<this.value.length; i++) {
              let key = this.value[i]
              if (map[key]) {
                map[key].forEach((m) => {
                  let index = this.value.findIndex(v => v == key)
                  if (index == m) {
                    this.tint(index, '#3aa394')
                    this.value[index] = ' '
                  } else {
                    this.tint(index, '#d3ad69')
                    this.value[index] = ' '
                  }
                })
              } else {
                this.tint(i, '#312a2c')
              }
            } */

            /* for(let [key, value] of Object.entries(map)) {
                for (let i=0; i<value.length; i++) {
                   let index = this.word.split('').findIndex(w => w == key)
                    if(this.word.includes() && index == value[i]) {
                      this.tint(value[i], '#3aa394')
                      this.word = this.word.replace(key, ' ')
                    } else if (this.word.includes(key)) {
                      this.tint(value[i], '#d3ad69')
                      this.word = this.word.replace(key, ' ')
                    } else {
                      this.tint(value[i], '#312a2c')
                    }
                }
            } */

            /* const word = []
            for(let i=0; i<5; i++) {
              let index = word.findIndex(w => w == this.value[i])
              if (index != -1 && word[index] == this.value[index]) {
                this.$refs[index+1][this.row].style.background = '#3aa394'
                this.$refs[i+1][this.row].style.background = '#312a2c'
                this.colors.push('#3aa394')
                word[index] = ''

                if(this.$refs[i+1][this.row].style.background == ''){
                  this.$refs[i+1][this.row].style.background = '#312a2c'
                  this.colors.push('#594B4F')
                  this.correct++;
                } 
              } else if(index != -1) {
                this.$refs[i+1][this.row].style.background = '#d3ad69'
                this.colors.push('#d3ad69')
                word[index] = ''
              } else if(this.$refs[i+1][this.row].style.background == ''){
                this.$refs[i+1][this.row].style.background = '#312a2c'
                this.colors.push('#594B4F')
              }
            }

            this.$emit('actionBoard', { colors: this.colors, keys: this.value})

            this.row++;
            this.column = 1;
            this.value = [];
            this.colors = [];

            if (this.correct < 5) {
              this.correct = 0;
            } */
          }
        }
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

<style>
    h3 {
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
</style>