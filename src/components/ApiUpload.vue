<template>
  <div class="cl-upload">
    <h2>Subiendo archivos a Cloudinary</h2>
    <div v-show="showProgress">
      <progress-bar :options="options" :value="progress" />
    </div>

    <!-- 
      Creamos el formulario evitando el submit para que no se recargue la pagina, recordemos es es un SPA.
      usaremos la funcion upload para el handle-->
    <form v-on:submit.prevent="upload">
      <label for="cloudname-input">Cloud Name:</label>
      <!-- bindeamos cloud-name al input -->
      <input
        id="cloudname-input"
        v-model="cloudName"
        placeholder="Enter cloud_name from dashboard"
      />
      <!-- bindeamos tambien el preset  -->
      <label for="preset-input">Preset:</label>
      <input
        id="preset-input"
        v-model="preset"
        placeholder="Enter preset from upload settings"
      />
      <!-- Creamos el input asignando el handle que se encargara de manejar los archivos y enviarlos-->
      <label for="file-input">Archivos:</label>
      <input
        id="file-input"
        type="file"
        accept="image/png, image/jpeg"
        @change="handleFileChange($event)"
      />
      <!--El botón de submitestara desactivado mientras se este seleccionando el archivo -->
      <button type="submit" :disabled="filesSelected < 1">Subir</button>
    </form>

    <!-- muestra la imagen si la subida es exitosa-->
    <section v-if="results && results.secure_url">
      <img :src="results.secure_url" :alt="results.public_id" />
    </section>

    <!-- muestra los errores si algo salio mal -->
    <section>
      <ul v-if="errors.length > 0">
        <li v-for="(error, index) in errors" :key="index">{{ error }}</li>
      </ul>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import ProgressBar from "vuejs-progress-bar";
export default {
  name: "ApiUpload",
  components: {
    ProgressBar,
  },
  data() {
    const progressBarOptions = {
      text: {
        shadowColor: "black",
        fontSize: 14,
        fontFamily: "Helvetica",
        dynamicPosition: true,
      },
      progress: {
        color: "#E8C401",
        backgroundColor: "#000000",
      },
      layout: {
        height: 35,
        width: 140,
        type: "line",
        progressPadding: 0,
        verticalTextAlign: 63,
      },
    };
    return {
      cloudName: "",
      preset: "",
      file: null,
      results: null,
      errors: [],
      filesSelected: 0,
      tags: "codeMx",
      progress: 0,
      showProgress: false,
      options: progressBarOptions,
      fileContents: null,
      formData: null,
    };
  },
  methods: {
    //funcion para manejar el archvo local y setearlo en el estado

    handleFileChange(event) {
      console.log("handlefilechange", event.target.files);
      //resto nos regresa un array de archivos, y setearemos el primer elemento al esado
      this.file = event.target.files[0];
      this.filesSelected = event.target.files.length;
    },
    prepareFormData() {
      this.formData = {
        upload_preset: this.preset,
        tags: this.preset,
        file: this.fileContents,
      };
    },

    // funcion que maneja el submit
    upload() {
      //verificamos si esta seteado el cloudname y el preset antes de enviar los archivos
      if (this.preset.length < 1 || this.cloudName.length < 1) {
        this.errors.push(
          "Debes ingresar el nombre del cloud y el preset para subir los archivos"
        );
        return;
      }
      // clear errors
      else {
        this.errors = [];
      }
      console.log("upload", this.file.name);

      let reader = new FileReader();
      //agregamos in listener al reader para que ejecute el callback cuando tenga la data
      reader.addEventListener(
        "load",
        () => {
          this.fileContents = reader.result;
          this.prepareFormData();

          //mostramos la barra de carga
          this.showProgress = true;
          axios({
            url: `https://api.cloudinary.com/v1_1/${this.cloudName}/upload`,
            method: "POST",
            data: this.formData,
            onUploadProgress: (progressEvent) => {
              console.log("progress", progressEvent);
              this.progress = Math.round(
                (progressEvent.loaded * 100.0) / progressEvent.total
              );
              console.log(this.progress);
            },
          })
            .then((response) => {
              this.results = response.data;
              console.log(this.results);
              console.log("public_id", this.results.public_id);
            })
            .catch((error) => {
              this.errors.push(error);
              console.log(this.error);
            })
            .finally(() => {
              setTimeout(() => {
                this.showProgress = false;
              }, 1000);
            });
        },
        false
      );
      // se llama la lectura del archivo si ya existe el mismo
      if (this.file && this.file.name) {
        reader.readAsDataURL(this.file);
      }
    },
  },
};
</script>

<style scoped>
form {
  display: grid;
  padding: 1em;
  background: #f9f9f9;
  border: 1px solid #c1c1c1;
  margin: 2rem auto 0 auto;
  max-width: 500px;
  padding: 1em;
}
form input {
  background: #fff;
  border: 1px solid #9c9c9c;
}
form button {
  background-color: blue;
  color: white;
  font-size: 1em;
  font-weight: bold;
  padding: 0.7em;
  width: 100%;
  border: 0;
}
form button:hover {
  background: gold;
  color: black;
}

label {
  padding: 0.5em 0.5em 0.5em 0;
}

input {
  padding: 0.7em;
  margin-bottom: 0.5rem;
}
input:focus {
  outline: 3px solid gold;
}

@media (min-width: 400px) {
  form {
    grid-template-columns: 150px 1fr;
    grid-gap: 16px;
  }

  label {
    text-align: right;
    grid-column: 1 / 2;
  }

  input,
  button {
    grid-column: 2 / 3;
  }
}

button {
  background-color: blue;
  color: white;
  font-weight: bold;
  border-radius: 10px;
}
button:focus {
  outline: none;
}
form button:disabled,
form button[disabled] {
  border: 1px solid #999999;
  background-color: #cccccc;
  color: #666666;
}
section {
  margin: 10px 0;
}
img {
  max-width: 300px;
  height: auto;
}
</style>
