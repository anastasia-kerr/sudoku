<template>
  <v-container>
    <template v-if="gridIsReady">
    <v-row v-for="num in count" :key="num">
      <v-chip v-for="number in grid[num]" :key="getKey(number,num)">{{number}}</v-chip>
    </v-row>
    </template>
  </v-container>
</template>

<script>
export default {
  name: "Region",
 data: function () {
    return {
      grid: [],
      count: 9,
      gridIsReady:false,
    };
  },
  created() {
    this.initializeGrid();
  },
  mounted(){
    this.populateGrid();
    this.gridIsReady=true;
  },
  methods: {
    getKey(number,index)
    {
      return `${number}+${index}+${Date.now()}`
    },
    getRandom(min = 1, max = 9) {
      let num = Math.random() * (max - min) + min;
      return Math.round(num);
    },
    populateRow(index) {
      let i = 0;
      while (i <9) {
        let random = this.getRandom();
        if(this.isInTheColumn(i,random) ||this.isInTheRow(i,random))
        {
          continue
        }
        this.grid[index][i]=random;
        i++
      }
    },
    populateGrid() {
      let i;
      for  (i = 0; i < 9; i++){
      this.populateRow(i);
      }
    },
    isInTheRow(index,item) {
     const foundItem = this.grid[index].find(x=>x === item);
      return foundItem;
   },
    isInTheColumn(index, item) {
      const foundItem = this.grid.find(x=>x[index] === item)
      return foundItem;
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
