<script setup>
import JobListing from "@/components/JobListing.vue";
import { ref, onMounted } from "vue";
import { RouterLink } from "vue-router";
import axios from "axios";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
});
const jobs = ref([]);
const loading = ref(true);
const error = ref(null);

onMounted(async () => {
  try {
    const res = await axios.get("/api/jobs");
    jobs.value = res.data;
  } catch (error) {
    error.value = "Error in fetching data: " + error.message;
  } finally {
    loading.value = false;
  }
});
</script>

<template>
  <section class="bg-blue-50 px-4 py-10">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
        Browse Jobs
      </h2>
      <!-- show loading while loading is true -->
      <div v-if="loading" class="text-center text-gray-500">
        <PulseLoader />
      </div>
      <div v-if="error" class="text-center text-red-500">{{ error }}</div>
      <div v-else class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in jobs.slice(0, limit || jobs.length)"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>
  <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
    <RouterLink
      to="/jobs"
      class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
      >View All Jobs</RouterLink
    >
  </section>
</template>
