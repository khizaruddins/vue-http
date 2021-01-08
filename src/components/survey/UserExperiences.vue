<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored Experiences found, Please start adding some surveys.
      </p>
      <p v-else-if="!isLoading && errorMessage">
        {{ errorMessage }}
      </p>
      <ul v-else-if="!isLoading && results && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from "./SurveyResult.vue";

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      errorMessage: null,
    };
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      fetch("https://vue-http-demo-d1250-default-rtdb.firebaseio.com/survey")
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          const result = [];
          this.isLoading = false;
          for (const id in data) {
            if (Object.hasOwnProperty.call(data, id)) {
              result.push({
                id,
                name: data[id].name,
                rating: data[id].rating,
              });
            }
          }
          this.results = result;
        })
        .catch((err) => {
          console.log(err);
          this.errorMessage = "Failed to fetch- Please try again later";
        });
    },
  },
  mounted() {
    this.loadExperiences();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
