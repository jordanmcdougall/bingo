<template>
  <v-row justify="center" align="center">
    <v-overlay v-model="alert" dismissible prominent type="error" opacity="0.9">
      <v-row justify="center" align="center">
        <v-row justify="center" align="center"
          >Would you like to start another game? All existing progress will be
          lost.</v-row
        >

        <v-col justify="center" align="center" cols="6">
          <v-btn color="green" @click="newgame()">Start a new game</v-btn>
        </v-col>
        <v-col justify="center" align="center" cols="6">
          <v-btn color="red" @click="alert = false">Resume Current Game</v-btn>
        </v-col>
      </v-row>
    </v-overlay>
    <v-col cols="12" justify="center" align="center">
      <v-btn :disabled="gameOver" @click="drawNumber()">Next Number</v-btn>
    </v-col>
    <v-col cols="12" justify="center" align="center">
      <span class="currentNumber">
        <v-avatar :color="currentNumberColor" size="150">
          <span class="currentNumber">{{ currentNumber }}</span>
        </v-avatar>
      </span>
    </v-col>
    <v-col v-if="drawnNumbers" cols="12" justify="center" align="center">
      <v-avatar
        v-for="number in drawnNumbers"
        :key="number.id"
        size="50"
        class="drawnNumbers"
        color="white"
      >
        {{ number }}
      </v-avatar>
    </v-col>
    <v-col v-if="currentNumber">
      <v-tooltip left>
        <template #activator="{ on, attrs }">
          <v-btn
            color="pink"
            v-bind="attrs"
            dark
            small
            absolute
            bottom
            right
            fab
            v-on="on"
            @click="alert = true"
          >
            <v-icon>mdi-plus</v-icon></v-btn
          >
        </template>
        <span>Start a new game</span>
      </v-tooltip>
    </v-col>
  </v-row>
</template>

<script>
export default {
  components: {},
  filters: {
    colors() {
      // return color
    },
  },
  data() {
    return {
      gameExists: false,
      numbers: [],
      drawnNumbers: [],
      alert: false,
      currentNumberColor: null,
    }
  },
  computed: {
    gameOver() {
      return this.drawnNumbers.length === 90
    },
    currentNumber() {
      return this.drawnNumbers.length > 0
        ? this.drawnNumbers[this.drawnNumbers.length - 1]
        : null
    },
  },
  created() {
    // TODO: CHECK IF EXISTING GAME EXISTS
    if (this.$storage.getLocalStorage('previousGame')) {
      this.numbers = this.$storage.getLocalStorage('previousGame').numbers
      this.drawnNumbers = this.$storage.getLocalStorage(
        'previousGame'
      ).drawnNumbers
      let color = '#'

      for (let i = 0; i < 6; i++) {
        color += ((Math.random() * 16) | 0).toString(16)
      }

      this.currentNumberColor = color
    } else {
      // init blank array

      // create an array from 1 to 90
      for (let i = 1; i <= 90; i++) this.numbers.push(i)
    }
  },
  methods: {
    drawNumber() {
      let color = '#'
      for (let i = 0; i < 6; i++) {
        color += ((Math.random() * 16) | 0).toString(16)
      }

      this.currentNumberColor = color

      // announce randowm number and remove it from the array
      this.drawnNumbers.push(
        this.numbers.splice(
          Math.floor(Math.random() * this.numbers.length),
          1
        )[0]
      )
      this.$storage.setLocalStorage('previousGame', {
        drawnNumbers: this.drawnNumbers,
        numbers: this.numbers,
      })
    },
    newgame() {
      // create an array from 1 to 90
      for (let i = 1; i <= 90; i++) this.numbers.push(i)

      // null all other vars
      this.drawnNumbers = []
      this.alert = false
      this.currentNumberColor = null
      this.$storage.removeLocalStorage('previousGame')
    },
  },
}
</script>
<style scoped>
.currentNumber {
  font-size: 3rem;
  color: white;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000;
}

.drawnNumbers {
  margin-left: 10px;
  margin-bottom: 5px;
  color: #000;
}

.newGame {
  position: initial;
  float: bottom;
  bottom: 10px;
}
</style>
