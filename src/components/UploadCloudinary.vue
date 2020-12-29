<template>
  <div>
    <div>
      <button @click="openUploadModal">Upload files</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: "",
    };
  },

  methods: {
    openUploadModal() {
      window.cloudinary
        .openUploadWidget(
          { cloud_name: "dcx0tg0qb", upload_preset: "xjcp86uk" },
          (error, result) => {
            if (!error && result && result.event === "success") {
              console.log("Done uploading..: ", result.info);
              this.$emit("imagenSubida", result.info.url);
            }
          }
        )
        .open();
    },
  },
};
</script>
