<template>
  <div id="app">
    <loading class="loading" v-if="!hasError && isLoading"/>
    <p v-if="hasError" class="error-message">Oops! Something wrong happened.</p>
    <img class="cat-image" v-if="!hasError && !isLoading && src" :src="src">
    <button class="btn" @click="loadNewCatImage">New cat picture</button>
  </div>
</template>

<script>
import axios from "axios";

import Loading from "./components/Loading";

export default {
  name: "app",
  components: {
    Loading
  },
  data() {
    return {
      src: null,
      hasError: false,
      isLoading: false
    };
  },
  async mounted() {
    await this.loadNewCatImage();
  },
  methods: {
    async loadNewCatImage() {
      try {
        this.hasError = false;
        this.isLoading = true;
        const cat = await axios.get("https://aws.random.cat/meow");
        this.src = cat.data.file;
        this.isLoading = false;
      } catch (error) {
        this.isLoading = false;
        this.hasError = true;
      }
    }
  }
};
</script>

<style>
@import url("https://rsms.me/inter/inter.css");
html {
  font-family: "Inter", sans-serif;
}
@supports (font-variation-settings: normal) {
  html {
    font-family: "Inter var", sans-serif;
  }
}
</style>

<style scoped>
#app {
  background-color: #edf2f7;
  border-radius: 0.5rem;
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.error-message {
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  font-size: 2rem;
  font-weight: bold;
  color: #2d3748;
  width: 100%;
  height: 20rem;
}

.loading {
  height: 30rem;
}

.cat-image {
  object-fit: cover;
  max-width: 90%;
  width: 30rem;
  height: 30rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
    0 2px 4px -1px rgba(0, 0, 0, 0.06);
}

.btn {
  display: block;
  background-color: #fff;
  border: none;
  cursor: pointer;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
  color: #4a5568;
  font-weight: bold;
  padding: 0.5rem 1rem;
  margin-top: 1rem;
  border-radius: 0.5rem;
}
</style>
