<template>
  <div id="app">
    <div id="sides">
      <div id="attacker" class="side">
        <div @click="toggleDice('attacker', 1)">
          <dice ref="attacker1" :class="{ disabled: !isDiceEnabled('attacker', 1) }"></dice>
        </div>
        <div @click="toggleDice('attacker', 2)">
          <dice ref="attacker2" :class="{ disabled: !isDiceEnabled('attacker', 2) }"></dice>
        </div>
        <div @click="toggleDice('attacker', 3)">
          <dice ref="attacker3" :class="{ disabled: !isDiceEnabled('attacker', 3) }"></dice>
        </div>
        <div v-if="loss" class="loss">
          {{ loss.attacker }}
          <div class="loss__total">{{ totalLoss.attacker }}</div>
        </div>
      </div>
      <div id="defender" class="side">
        <div @click="toggleDice('defender', 1)">
          <dice ref="defender1" :class="{ disabled: !isDiceEnabled('defender', 1) }"></dice>
        </div>
        <div @click="toggleDice('defender', 2)">
          <dice ref="defender2" :class="{ disabled: !isDiceEnabled('defender', 2) }"></dice>
        </div>
        <div @click="toggleDice('defender', 3)">
          <dice ref="defender3" :class="{ disabled: !isDiceEnabled('defender', 3) }"></dice>
        </div>
        <div v-if="loss" class="loss">
          {{ loss.defender }}
          <div class="loss__total">{{ totalLoss.defender }}</div>
        </div>
      </div>
    </div>

    <button id="button" @click='roll'>Roll</button>

    <div id="about">
      <h1>Risk Dices</h1>
      <a href='https://github.com/lumnn/risk-dices'>Source code</a>
      <a href='https://github.com/lumnn/risk-dices/issues'>Feedback / Issues</a>
    </div>
  </div>
</template>

<script>
import Dice from './components/Dice.vue'

export default {
  name: 'app',
  components: {
    Dice
  },

  data: function () {
    return {
      enabled: {
        attacker: [1, 2, 3],
        defender: [1, 2, 3]
      },
      result: {
        attacker: [],
        defender: []
      },
      totalLoss: {
        attacker: 0,
        defender: 0
      },
      resultsRemaining: 0
    }
  },

  computed: {
    loss: function () {
      var attacker = this.result.attacker.slice(0).sort().reverse()
      var defender = this.result.defender.slice(0).sort().reverse()

      var result = {
        attacker: 0,
        defender: 0
      }

      for (var i = attacker.length - 1; i >= 0; i--) {
        if (defender[i] === undefined) {
          continue
        }

        if (attacker[i] <= defender[i]) {
          result.attacker++
          continue
        }

        result.defender++
      }

      return result
    }
  },

  methods: {
    roll: function () {
      this.result.attacker = []
      this.result.defender = []
      this.resultsRemaining = 0

      for (var i = this.enabled.attacker.length - 1; i >= 0; i--) {
        let ref = 'attacker' + this.enabled.attacker[i]

        this.$refs[ref].$off('rolled')
        this.$refs[ref].roll()
        this.resultsRemaining++
        this.$refs[ref].$once('rolled', (result) => this.onRolled('attacker', result))
      }
      for (var j = this.enabled.defender.length - 1; j >= 0; j--) {
        let ref = 'defender' + this.enabled.defender[j]

        this.$refs[ref].$off('rolled')
        this.$refs[ref].roll()
        this.resultsRemaining++
        this.$refs[ref].$once('rolled', (result) => this.onRolled('defender', result))
      }
    },

    onRolled: function (side, result) {
      this.result[side].push(result)
      this.resultsRemaining--

      if (this.resultsRemaining === 0) {
        this.totalLoss = {
          attacker: this.totalLoss.attacker + this.loss.attacker,
          defender: this.totalLoss.defender + this.loss.defender
        }
      }
    },

    isDiceEnabled: function (side, id) {
      return this.enabled[side].indexOf(id) > -1
    },

    toggleDice: function (side, id) {
      var index = this.enabled[side].indexOf(id)

      if (index === -1) {
        this.enabled[side].push(id)
        return
      }

      if (this.enabled[side].length === 1) {
        return
      }

      this.enabled[side].splice(index, 1)
    }
  }
}
</script>

<style lang="scss">
html, body {
  background: black;
  color: #fff;
  margin: 0;
  padding: 0;
  height: 100%;
}

*, *:before, *:after {
  box-sizing: border-box;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  max-width: 480px;
  font-size: 10vh;
  margin: auto;
  display: flex;
  flex-direction: column;
  height: 100%;
  padding: .25em;
}

#sides {
  display: flex;
  justify-content: space-between;
}

.loss {
  text-align: center;
  width: 1.5em;
  padding: .25em;
  margin-bottom: .25em;
  position: relative;

  &__total {
    right: 0;
    font-size: .5em;
  }
}

.dice {
  margin-bottom: .25em;
  color: #000;

  &.disabled {
    opacity: 0.3;
  }
}

#attacker {
  .dice {
    transition: .2s ease-out;
    background: #ee5253;
  }

  .loss {
    color: #ee5253;
  }
}

#defender {
  .dice {
    background: #2e86de;
  }

  .loss {
    color: #2e86de;
  }
}

#button {
  flex-grow: 1;
  font-size: .75em;
  line-height: 1em;
  border-radius: .1em;
  width: 100%;
  border: 0;
  background: #10ac84;
  color: #000;
  font-weight: bold;
  display: block;
  margin: auto;
  outline: 0;

  &:active {
    background: darken(#10ac84, 10);
  }
}

#about {
  font-size: 2vh;
  display: flex;
  justify-content: space-between;
  padding: 1em 0;

  h1 {
    color: #10ac84;
    font-weight: bold;
    font-size: 1em;
    display: inline-block;
    margin: 0;
  }

  a {
    color: #666;
  }
}
</style>
