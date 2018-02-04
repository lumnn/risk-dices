<template>
  <div class="dice">
    {{ result }}
  </div>
</template>

<script>
export default {
  name: 'dice',

  data: function () {
    return {
      result: Math.floor(Math.random() * 6) + 1,
      roller: null,
      rollingIteration: 0
    }
  },

  methods: {
    roll: function () {
      if (this.roller) {
        clearInterval(this.roller)
      }

      this.rollingIteration = 0

      this.roller = setInterval(() => {
        this.result = Math.floor(Math.random() * 6) + 1
        this.rollingIteration++

        if (this.rollingIteration > 6) {
          if (this.roller) {
            clearInterval(this.roller)
            this.roller = null
          }
          this.$emit('rolled', this.result)
        }
      }, 100)
    }
  }
}
</script>

<style lang="scss">
  .dice {
    box-sizing: border-box;
    text-align: center;
    width: 1.5em;
    height: 1.5em;
    padding: .25em;
    background-color: gray;
    border-radius: .1em;
    color: white;
    cursor: pointer;
  }
</style>
