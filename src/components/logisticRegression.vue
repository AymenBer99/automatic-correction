<template>
  <form @submit.prevent="correctQuestions">
    <br />
    <div>
      <label for="logCaractere">Coefficient log Caractère</label>
      <input
        id="logCaractere"
        name="logCaractere"
        type="number"
        step="any"
        v-model.number="coeffLogCaractere"
        ref="logCaractereInput"
      />
    </div>
    <br />
    <div>
      <label for="coeff0_1">Coefficient 0|1</label>
      <input
        id="coeff0_1"
        name="coeff0_1"
        type="number"
        step="any"
        v-model.number="coeff0_1"
        ref="coeff0_1Input"
      />
    </div>
    <br />
    <div>
      <label for="coeff0_1">Coefficient 1|2</label>
      <input
        id="coeff1_2"
        name="coeff1_2"
        type="number"
        step="any"
        v-model.number="coeff1_2"
        ref="coeff1_2Input"
      />
    </div>
    <br />
    <div>
      <label for="seuilDeCorrectionAutomatique"
        >Seuil de correction Automatique (%)</label
      >
      <input
        id="seuilDeCorrectionAutomatique"
        name="seuilDeCorrectionAutomatique"
        type="number"
        step="any"
        v-model.number="seuilDeCorrectionAutomatique"
        ref="seuilDeCorrectionAutomatiqueInput"
      />
    </div>
    <br />
    <div class="correction">
      <button>Corriger la question</button>

      <span>
        <downloadCsv :data="csv">Download correction</downloadCsv>
      </span>
    </div>
  </form>
</template>

<script>
import JsonCSV from "./JsonCSV.vue";

export default {
  name: "logisticRegression",
  components: {
    downloadCsv: JsonCSV,
  },
  props: {
    csv: Array,
  },
  data() {
    return {
      coeffLogCaractere: 2.2,
      coeff0_1: -0.95,
      coeff1_2: 2.87,
      seuilDeCorrectionAutomatique: 60,
    };
  },
  methods: {
    standardDeviation(mean) {
      return Math.sqrt(
        this.csv
          .reduce((acc, val) => acc.concat((val.logChar - mean) ** 2), [])
          .reduce((acc, val) => acc + val, 0) /
          (this.csv.length - 1)
      );
    },
    computeNumberOfChar() {
      var charNumber = 0;
      for (const element of this.csv) {
        charNumber = element.soumission.length;
        element.logChar = Math.log(charNumber);
      }
      var meanLogCaractere =
        this.csv.reduce((total, next) => total + next.logChar, 0) /
        this.csv.length;
      var standardDevLogCaractere = this.standardDeviation(meanLogCaractere);
      for (const element of this.csv) {
        element.logChar =
          (element.logChar - meanLogCaractere) / standardDevLogCaractere;
      }
    },
    correctQuestions() {
      this.computeNumberOfChar();
      var logitInf0 = 0;
      var logitInf1 = 0;
      var pLeq0 = 0;
      var pLeq1 = 0;
      var arrayOfProba = [];
      for (const element of this.csv) {
        logitInf0 = this.coeff0_1 - this.coeffLogCaractere * element.logChar;
        logitInf1 = this.coeff1_2 - this.coeffLogCaractere * element.logChar;
        pLeq0 = 1 / (1 + Math.exp(-logitInf0));
        pLeq1 = 1 / (1 + Math.exp(-logitInf1));
        arrayOfProba = [pLeq0, pLeq1 - pLeq0, 1 - pLeq1];
        element.proba0 = pLeq0;
        element.proba1 = pLeq1 - pLeq0;
        element.proba2 = 1 - pLeq1;
        element.Score = arrayOfProba.reduce(
          (iMax, x, i, arr) => (x > arr[iMax] ? i : iMax),
          0
        );
        if (
          Math.max(element.proba0, element.proba1, element.proba2) <
          this.seuilDeCorrectionAutomatique / 100
        ) {
          element.Recorrection = true;
        } else {
          element.Recorrection = false;
        }
      }
    },
  },
};
</script>

<style scoped>
form {
  margin: 2rem auto;
  max-width: 40rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 2rem;
  background-color: #ffffff;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
}

h2 {
  font-size: 1rem;
  margin: 0.5rem 0;
}

input,
select {
  display: block;
  width: 100%;
  font: inherit;
  margin-top: 0.5rem;
}

select {
  width: auto;
}

input[type="checkbox"],
input[type="radio"] {
  display: inline-block;
  width: auto;
  margin-right: 1rem;
}

input[type="checkbox"] + label,
input[type="radio"] + label {
  font-weight: normal;
}

button {
  font: inherit;
  border: 1px solid #0076bb;
  background-color: #0076bb;
  color: white;
  cursor: pointer;
  padding: 0.75rem 2rem;
  border-radius: 30px;
}

button:hover,
button:active {
  border-color: #002350;
  background-color: #002350;
}

span {
  font: inherit;
  border: 1px solid #0076bb;
  background-color: #0076bb;
  color: white;
  cursor: pointer;
  padding: 0.75rem 2rem;
  border-radius: 30px;
}

span:hover,
span:active {
  border-color: #002350;
  background-color: #002350;
}

.correction {
  display: flex;
  justify-content: space-evenly;
}
</style>