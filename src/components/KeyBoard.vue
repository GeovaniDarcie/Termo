<template>
  <div>
      <div class="key-board">
          <button @click="$emit('tiping', { key, random: Math.random() * 1000 } )" v-for="key in keysLine1" :key="key" :ref="key" class="key">
              {{ key }}
          </button>
      </div>
      <div class="key-board">
          <button @click="$emit('tiping', { key, random: Math.random() * 1000 })" v-for="key in keysLine2" :key="key" :ref="key" class="key">
              {{ key }}
          </button>
      </div>
      <div class="key-board">
          <button @click="$emit('tiping', { key, random: Math.random() * 1000 })" v-for="key in keysLine3" :key="key" :ref="key" :class="{ enter: key == 'Enter', key: key != 'Enter'}">
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
            keysLine1: ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'],
            keysLine2: ['a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l', '<=='],
            keysLine3: ['z', 'x', 'c', 'v', 'b', 'n', 'm', 'Enter']
        }
    },
    props: ['paint'],
    watch: {
        paint() {
            this.paint.keys.forEach((letter, index) => {
                this.$refs[letter][0].style.background = this.paint.colors[index];

                if (this.paint.colors[index] == '#594B4F') {
                    this.$refs[letter][0].style.color = '#6E5C62'
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
        text-transform: uppercase;
    }

    .enter {
        height: 44px;
        width: 60px;
        background-color: #4C4347;
        border: none;
        margin: 3px;
        color: white;
        font-family: 'Mitr', sans-serif;
        cursor: pointer;
    }
</style>