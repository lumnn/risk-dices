<template>
  <div id="app">
    <div id="sides">
      <div id="attacker" class="side">
        <div @click="toggleDice('attacker', 1)">
          <dice ref="attacker1" :class="{ disabled: !isDiceEnabled('attacker', 1) }" style="background: red"></dice>
        </div>
        <div @click="toggleDice('attacker', 2)">
          <dice ref="attacker2" :class="{ disabled: !isDiceEnabled('attacker', 2) }" style="background: red"></dice>
        </div>
        <div @click="toggleDice('attacker', 3)">
          <dice ref="attacker3" :class="{ disabled: !isDiceEnabled('attacker', 3) }" style="background: red"></dice>
        </div>
        <div class="side__loss">{{ loss.attacker }}</div>
      </div>
      <div id="defender" class="side">
        <div @click="toggleDice('defender', 1)">
          <dice ref="defender1" :class="{ disabled: !isDiceEnabled('defender', 1) }" style="background: blue"></dice>
        </div>
        <div @click="toggleDice('defender', 2)">
          <dice ref="defender2" :class="{ disabled: !isDiceEnabled('defender', 2) }" style="background: blue"></dice>
        </div>
        <div @click="toggleDice('defender', 3)">
          <dice ref="defender3" :class="{ disabled: !isDiceEnabled('defender', 3) }" style="background: blue"></dice>
        </div>
        <div class="side__loss">{{ loss.defender }}</div>
      </div>
    </div>

    <button id="button" @click='roll'>Roll</button>
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
      attacker: [1, 2, 3],
      defender: [1, 2, 3],
      attackerResult: [],
      defenderResult: []
    }
  },

  computed: {
    loss: function () {
      var attacker = this.attackerResult.slice(0).sort().reverse()
      var defender = this.defenderResult.slice(0).sort().reverse()

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
      this.attackerResult = []
      this.defenderResult = []

      for (var i = this.attacker.length - 1; i >= 0; i--) {
        this.$refs['attacker' + this.attacker[i]].roll()
        this.$refs['attacker' + this.attacker[i]].$once('rolled', (result) => this.attackerResult.push(result))
      }
      for (var j = this.defender.length - 1; j >= 0; j--) {
        this.$refs['defender' + this.defender[j]].roll()
        this.$refs['defender' + this.defender[j]].$once('rolled', (result) => this.defenderResult.push(result))
      }
    },

    isDiceEnabled: function (type, id) {
      return this[type].indexOf(id) > -1
    },

    toggleDice: function (type, id) {
      var index = this[type].indexOf(id)

      if (index === -1) {
        this[type].push(id)
        return
      }

      if (this[type].length === 1) {
        return
      }

      this[type].splice(index, 1)
    }
  }
}
</script>

<style lang="scss">
body {
  background: black;
  color: #fff;
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  max-width: 480px;
  font-size: 10vh;
  margin: auto;
}

#sides {
  display: flex;
  justify-content: space-around;
}

.side {
  &__loss {
    text-align: center;
    width: 1em;
    height: 1em;
    line-height: 1em;
    font-size: 1em;
    padding: .25em;
    
    margin: .25em 0;
  }
}

.dice {
  margin: .25em 0;

  &.disabled {
    opacity: 0.3;
  }
}

#button {
  font-size: 1em;
  line-height: 1em;
  padding: .25em;
  border-radius: .1em;
  width: 100%;
  border: 0;
  background: green;
  color: #fff;
  display: block;
  margin: auto;

  &:active {
    background: darken(green, 10);
  }
}
</style>
