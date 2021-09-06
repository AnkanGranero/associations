<template>
  <div class="flex flex-col items-center w-full">
    <input
      placeholder="WRITE A WORD"
      class="
        bg-gray-100
        h-20
        w-1/5
        mt-20
        text-center text-2xl
        focus:outline-black
      "
      v-model="searchWord"
    />
    <button class="outline-black w-1/7 mt-4" @click="requestLoop">
      ASSOCIATE
    </button>
    <div v-for="result in Object.values(results)" :key="result[0]">
      <span v-for="value in result" :key="value"> -{{ value }}-</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "WordTree",
  data() {
    return {
      searchWord: "",
      results: {},
      index: 0,
      limit: 30,
    };
  },

  methods: {
    turnResponseIntoArray(res) {
      console.log("res", res);
      return res[0]?.meanings[0]?.definitions[0]?.definition
        .split(" ")
        .map((str) => str.replace(/[^a-zA-Z]/g, ""))
        .filter((str) => str.length > 4)
        .slice(0, this.index + 1);
    },

    async makeRequest() {
      return fetch(this.url, {});
      /* .then((res) => res.json())
        .then((res) => res)
        .catch((error) => {
          console.log(error);
        }); */
    },
    async requestLoop() {
      await this.makeRequest()
        .then((res) => {
          return res.json();
        })
        .then((res) => {
          return this.turnResponseIntoArray(res);
        })
        .then((res) => {
          this.results[this.index] = res;
          this.searchWord = res[0];
        })
        .then(() => {
          if (this.index > this.limit) {
            return;
          } else {
            this.index++;
            this.requestLoop();
          }
        })
        .finally()
        .catch(
          console.log("oops something went wrong"),
          (this.index = 0),
          (this.searchWord = "")
        );
    },
  },
  computed: {
    url() {
      return `https://api.dictionaryapi.dev/api/v2/entries/en/${this.searchWord}`;
    },
  },
};
</script>