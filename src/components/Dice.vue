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
      this.rollingIteration = 0

      this.roller = setInterval(() => {
        this.result = Math.floor(Math.random() * 6) + 1
        this.rollingIteration++

        if (this.rollingIteration > 6) {
          clearInterval(this.roller)
          this.$emit('rolled', this.result)
        }
      }, 100)
    }
  }
}
</script>

<style lang="scss">
  .dice {
    text-align: center;
    width: 1em;
    height: 1em;
    line-height: 1em;
    font-size: 1em;
    padding: .25em;
    border-radius: .1em;
    color: white;
    cursor: pointer;
  }
</style>
