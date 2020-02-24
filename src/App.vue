<template>
  <div id="app">
    <h1>Today's Your Lucky Parrot</h1>
    <img alt="parrot" :src="path" />
    <span>{{ parrot_name }}</span>
    <button @click="show_random_parrot">ランダムなparrotを表示</button>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function() {
    return {
      path: "",
      parrot_name: "test"
    };
  },
  methods: {
    show_random_parrot: async function() {
      const response = await fetch("/list.txt");
      const parrot_list = await response.text();
      const parrot_array = parrot_list.split("\n");
      function getRandomInt(max) {
        return Math.floor(Math.random() * Math.floor(max));
      }
      const path = parrot_array[getRandomInt(parrot_array.length)];
      this.path = path;
      this.parrot_name = path.slice(8, -4);
    }
  }
};
</script>

<style>
</style>
