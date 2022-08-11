<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="getSubmittedExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>

      <p v-if="isLoading">Loading...</p>

      <p v-else-if="!isLoading && error">{{ error }}</p>

      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No results found, make some!
      </p>

      <ul v-else>
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
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      error: null,
      isLoading: false,
      results: [],
    };
  },
  methods: {
    getSubmittedExperiences() {
      this.error = null;
      this.isLoading = true;

      fetch(
        'https://vue-sending-http-request-5563f-default-rtdb.firebaseio.com/surveys.json'
      )
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          const results = [];

          console.log(data);

          for (const id in data) {
            results.push({
              id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }

          this.results = results;

          this.isLoading = false;
        })
        .catch((error) => {
          this.error = 'Failed to fetch data due to an error - please try again.';

          console.error(error);

          this.isLoading = false;
        });
    },
  },
  mounted() {
    this.getSubmittedExperiences();
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
