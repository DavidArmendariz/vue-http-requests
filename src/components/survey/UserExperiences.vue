<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <ul v-else-if="!isLoading && (!results || results.length === 0)">
        <p>No stored experiences found. Start adding some survey results.</p>
      </ul>
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
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: false
    };
  },
  mounted() {
    this.loadExperiences();
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      fetch(process.env.VUE_APP_FIREBASE_REALTIME_DATABASE)
        .then(response => {
          if (response.ok) {
            return response.json();
          }
        })
        .then(data => {
          this.isLoading = false;
          const results = [];
          for (const id in data) {
            const { name, rating } = data[id];
            results.push({
              id,
              name,
              rating
            });
          }
          this.results = results;
        });
    }
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
