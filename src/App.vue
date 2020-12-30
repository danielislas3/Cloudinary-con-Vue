<template>
  <div id="app">
    <img alt="Vue logo" :src="imageUrl" />
    <HelloWorld msg="Subir archivos con cloudinary" />
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
      preset: process.env.VUE_APP_HOLA,
      cloudName: process.env.VUE_APP_CLOUD_NAME,
      isLoading: false,
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
