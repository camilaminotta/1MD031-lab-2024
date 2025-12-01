<template>
  <div class="burger"> 
    <h3>{{burger.name}}</h3>
    <p>{{burger.kCal}} kCal</p>
    <img v-bind:src="burger.img_url" class="burgerimage">
    <ul class="ingredients">
      <li 
        v-for="ingredient in burger.ingredients" 
        v-bind:key="ingredient"
        >
        {{ingredient}}
      </li>
    </ul>
    <div>
      <span>Amount:</span>
      <button type="button" v-on:click="decreaseAmount">-</button>
      {{amountOrdered}}
      <button type="button" v-on:click="increaseAmount">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    decreaseAmount: function () {
      if (this.amountOrdered > 0) {
      this.amountOrdered -= 1;
      this.$emit('orderedBurgers', {name: this.burger.name, amount: this.amountOrdered});
      }
    },
    increaseAmount: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurgers', {name: this.burger.name, amount: this.amountOrdered});
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .burger {
      padding: 15px;
      margin: 15px;
  }

  .burgerimage {
      width: 80%;
  }
</style>