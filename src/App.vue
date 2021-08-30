<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">Navbar</a>
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNavAltMarkup"
        aria-controls="navbarNavAltMarkup"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
        <div class="navbar-nav">
          <a class="nav-link active" aria-current="page" href="#">Home</a>
          <a class="nav-link" href="#">Features</a>
          <a class="nav-link" href="#">Pricing</a>
          <a
            class="nav-link disabled"
            href="#"
            tabindex="-1"
            aria-disabled="true"
            >Disabled</a
          >
        </div>
      </div>
    </div>
  </nav>

  <div class="bloc">
    <h1>Correction automatique des réponses d'un élève</h1>
    <p>
      Le but de cette interface est de permettre à difféerentes personnes
      d'évaluer l'investissement de la réponse d'un élève. Elle permet d'entrer
      un fichier csv qui doit contenir les colonnes suivantes :
    </p>
    <ul>
      <li>Nom de l'élève</li>
      <li>Prénom de l'élève</li>
      <li>Adresse mail de l'élève</li>
      <li>Réponse de l'élève</li>
    </ul>
    <p>
      Ensuite on choisit l'algorithme de correction dans la liste en bas à
      gauche et les différents paramètres nécessaires. Les boutons en-dessous
      des paramètres permettent de lancer la correction et de télécharger un
      fichier csv contenant les résultats.
    </p>
  </div>

  <div class="center">
    <vue-csv-import
      v-model="csv"
      :fields="{
        nom: { required: true, label: 'Nom' },
        prenom: { required: true, label: 'Prénom' },
        email: { required: true, label: 'Adresse de courriel' },
        soumission: { required: true, label: 'Réponse à corriger' },
      }"
      class="center"
    >
      <vue-csv-input class="center"></vue-csv-input>
      <br />
      <vue-csv-toggle-headers class="center"></vue-csv-toggle-headers>
      <br />
      <vue-csv-errors class="center"></vue-csv-errors>
      <br />
      <vue-csv-map class="center"></vue-csv-map>
      <br />
    </vue-csv-import>
  </div>

  <table class="correction">
    <tbody>
      <tr class="container">
        <td class="colonneAlgo col-1 col-sm-1 col-md-1 col-lg-1 col-xl-1">
          <div>
            <ul class="algoList">
              <li v-for="(algo, index) in algorithmes" :key="algo.id">
                <a
                  class="algo"
                  :class="{ active: algo.isActive }"
                  @click="changeAlgo(index)"
                >
                  {{ algo.name }}
                </a>
              </li>
            </ul>
          </div>
        </td>
        <td class="colonneCorrection col-4 col-sm-4 col-md-4 col-lg-4 col-xl-4">
          <div v-if="csv != null">
            <component :is="activeAlgo" v-bind="currentProperties"></component>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import logisticRegression from "./components/logisticRegression.vue";
import {
  VueCsvToggleHeaders,
  VueCsvMap,
  VueCsvInput,
  VueCsvErrors,
  VueCsvImport,
} from "vue-csv-import";

export default {
  name: "App",
  components: {
    logisticRegression,
    VueCsvImport,
    VueCsvToggleHeaders,
    VueCsvMap,
    VueCsvInput,
    VueCsvErrors,
  },
  data() {
    return {
      algorithmes: [
        {
          id: "logisticRegression",
          isActive: true,
          name: "Regression logistique",
        },
        {
          id: "neuralNetwork",
          isActive: false,
          name: "Reseau de neurones",
        },
      ],
      locationOfActiveAlgo: 0,
      activeAlgo: "logisticRegression",
      csv: null,
    };
  },
  computed: {
    currentProperties: function () {
      if (this.activeAlgo === "logisticRegression") {
        return { csv: this.csv };
      } else {
        return {};
      }
    },
  },
  methods: {
    changeAlgo(index) {
      this.algorithmes[this.locationOfActiveAlgo].isActive = false;
      this.algorithmes[index].isActive = true;
      this.locationOfActiveAlgo = index;
      this.activeAlgo = this.algorithmes[index].id;
    },
  },
};
</script>

<style scoped>
body {
  margin: 0;
}

ul.algoList {
  list-style-type: none;
  margin: 0;
  padding: 0;
  background-color: #f1f1f1;
  height: 100%;
  overflow: auto;
}

li a.algo {
  display: block;
  color: #000;
  padding: 8px 16px;
  text-decoration: none;
}

li a.active.algo {
  background-color: #04aa6d;
  color: white;
}

li a:hover:not(.active).algo {
  background-color: #555;
  color: white;
}

.colonneCorrection {
  background-color: #f1f1f1;
  overflow: auto;
}

.colonneAlgo {
  padding: 0;
  background-color: #f1f1f1;
  overflow: auto;
}

table td,
table td * {
  vertical-align: top;
}

.colonneCorrection {
  background-color: #f1f1f1;
  overflow: auto;
}

.correction {
  display: table;
  width: 100%;
}

.bloc {
  border: solid;
  margin-inline: 20%;
  margin-block: 5%;
  border-radius: 3%;
  word-wrap: break-word;
  text-align: center;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
<style>
.center {
  margin-left: auto;
  margin-right: auto;
  text-align: center;
}
</style>