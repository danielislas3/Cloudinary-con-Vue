<template>
  <div id="app">
    <img alt="Vue logo" :src="imageUrl" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
    <!-- <UploadCloudinary @imagenSubida="setUrl"></UploadCloudinary> -->
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
// import UploadCloudinary from "@/components/UploadCloudinary";
export default {
  name: "App",
  components: {
    HelloWorld,
    // UploadCloudinary,
  },
  data() {
    return {
      url: "",
    };
  },
  computed: {
    imageUrl() {
      return this.url === "" ? require("./assets/logo.png") : this.url;
    },
  },
  methods: {
    setUrl(e) {
      this.url = e;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
