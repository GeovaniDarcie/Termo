<template>
  <div>
      <div class="key-board">
          <button v-for="key in keysLine1" :key="key" :ref="key" class="key">
              {{ key }}
          </button>
      </div>
      <div class="key-board">
          <button v-for="key in keysLine2" :key="key" :ref="key" class="key">
              {{ key }}
          </button>
      </div>
      <div class="key-board">
          <button v-for="key in keysLine3" :key="key" :ref="key" class="key">
              {{ key }}
          </button>
      </div>
  </div>
</template>

<script>
export default {
    name: "KeyBoard",
    data() {
        return {
            keysLine1: ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
            keysLine2: ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L'],
            keysLine3: ['Z', 'X', 'C', 'V', 'B', 'N', 'M']
        }
    },
    props: ['paint'],
    watch: {
        paint() {
            console.log(this.paint)
            this.paint.keys.forEach((letter, index) => {
                this.$refs[letter.toUpperCase()][0].style.background = this.paint.colors[index];

                if (this.paint.colors[index] == '#594B4F') {
                    this.$refs[letter.toUpperCase()][0].style.color = '#6E5C62'
                }
            })
        }
    }
}
</script>

<style>
    .key-board {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: row;
    }

    .key {
        height: 44px;
        width: 47px;
        background-color: #4C4347;
        border: none;
        margin: 3px;
        color: white;
        font-family: 'Mitr', sans-serif;
        cursor: pointer;
    }
</style>