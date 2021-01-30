<template>
  <v-container>
    <v-card width="300">
      <v-card-text>
        <template v-if="gridIsReady">
          <v-row v-for="num in count" :key="num">
            <v-chip v-for="number in grid[num]" :key="getKey(number, num)">{{
              number
            }}</v-chip>
          </v-row>
        </template>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: "Region",
  data: function () {
    return {
      counter: 0,
      grid: [],
      count: 9,
      gridIsReady: false,
    };
  },
  created() {
    this.initializeGrid();
  },
  mounted() {
    this.populateGrid();
    this.gridIsReady = true;
  },
  methods: {
    getKey(number, index) {
      return `${this.getRandom(0, 99992)}- ${number}+${index}+${Date.now()}`;
    },
    getRandom(min = 1, max = 9) {
      let num = Math.random() * (max - min) + min;
      return Math.round(num);
    },
    generateArrayOfNumbers(numbers) {
      return [...Array(numbers).keys()].slice(1);
    },
    resetColumn(index) {
      for (let i = 0; i < 8; i++) {
        let clone = [...this.grid[i]];
        clone[index] = null;
        this.$set(this.grid, i, clone);
      }
    },
    populateColumn(index) {
      this.resetColumn(index);
      let i = 0;
      let numbers = this.generateArrayOfNumbers(10);
      let numberOfTries = 0;
      while (i < 9) {
        let indexOfNumbers = this.getRandom(0, numbers.length - 1);
        let random = numbers[indexOfNumbers];
        if (!this.isInTheRow(i, random) && !this.isInTheColumn(index, random)) {
          let clone = [...this.grid[i]];
          clone[index] = random;
          this.$set(this.grid, i, clone);
          numbers.splice(indexOfNumbers, 1);
          numberOfTries = 0;
          i++;
        } else {
          numberOfTries++;
        }
        if (numberOfTries >= 8) {
          numberOfTries = 0;
          this.resetColumn(index);
          numbers = this.generateArrayOfNumbers(10);
          i = 0;
        }
      }
      this.counter++;
    },
    reset() {
      let i = 0;
      while (i < this.count) {
        this.resetColumn(i);
        i++;
      }
      this.populateGrid();
    },
    populateGrid() {
      for (let i = 0; i < 9; i++) {
        this.populateColumn(i);
      }
    },
    isInTheRow(index, item) {
      return this.grid[index].some((x) => x === item);
    },
    isInTheColumn(index, item) {
      return this.grid.some((x) => x[index] === item);
    },
    initializeGrid() {
      let i = 0;
      while (i < this.count) {
        this.grid.push(new Array(this.count));
        i++;
      }
    },
  },
};
</script>
