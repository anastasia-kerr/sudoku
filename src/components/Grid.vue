<template>
  <v-container>
    <v-card width="390">
      <v-card-text v-if="gridIsReady">
        <template v-for="rowIndex in 3">
          <v-row :key="rowIndex">
            <v-col v-for="col in 3" :key="col">
              <v-card hover outlined rounded>
                <v-card-text>
                  <v-row
                    v-for="littlRow in getAvilablIndexesByRegionNumber(rowIndex)"
                    :key="littlRow"
                  >
                    <v-chip
                      v-for="chips in getAvilablIndexesByRegionNumber(col)"
                      :key="chips"
                      >{{ grid[littlRow][chips] }}
                    </v-chip>
                  </v-row>
                </v-card-text>
              </v-card>
            </v-col>
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
      regionIndex: [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
      ],
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
    getAvilablIndexesByRegionNumber(index) {
      return this.regionIndex[index - 1];
    },
    getRegion(index) {
      for (let i = 0; i < this.regionIndex.length; i++) {
        if (this.regionIndex[i].some((r) => r === index)) {
          return this.regionIndex[i];
        }
      }
    },
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
      this.getRegion(index);
      for (let i = 0; i < 8; i++) {
        let clone = [...this.grid[i]];
        clone[index] = null;
        this.$set(this.grid, i, clone);
      }
    },
    resetRegionCols(index) {
      const region = this.getRegion(index);
      for (let x of region) {
        this.resetColumn(x);
      }
      this.counter = region[0];
    },
    populateColumn(index) {
      this.resetColumn(index);
      let i = 0;
      let numbers = this.generateArrayOfNumbers(10);
      let numberOfTries = 0;
      while (i < 9) {
        let indexOfNumbers = this.getRandom(0, numbers.length - 1);
        let random = numbers[indexOfNumbers];
        if (
          !this.isInTheRow(i, random) &&
          !this.isInTheColumn(index, random) &&
          !this.isInTheRegion(i, index, random)
        ) {
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
          this.resetRegionCols(index);
          numbers = this.generateArrayOfNumbers(10);
          i = 0;
        }
      }
    },
    populateGrid() {
      for (this.counter; this.counter < 9; this.counter++) {
        this.populateColumn(this.counter);
      }
    },
    isInTheRow(index, item) {
      return this.grid[index].some((x) => x === item);
    },
    isInTheColumn(index, item) {
      return this.grid.some((x) => x[index] === item);
    },
    isInTheRegion(xIndex, yIndex, item) {
      for (let x of this.getRegion(xIndex)) {
        for (let y of this.getRegion(yIndex)) {
          if (this.grid[y][x] === item) {
            return true;
          }
        }
      }
      return false;
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
