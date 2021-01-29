<template>
  <v-container>
    <v-chip class="ma-2">{{ this.number }}</v-chip>
  </v-container>
</template>

<script>
export default {
  name: "Region",
  props: {
    number: Number,
  },
};
</script>
