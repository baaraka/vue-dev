<script setup>
import PulseLoader from "vue-spinner/src/PulseLoader.vue";
import axios from "axios";
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import BackButton from "@/components/BackButton.vue";
import { useToast } from "vue-toastification";

const route = useRoute();
const router = useRouter();
const toast = useToast();

const jobId = route.params.id;

const job = ref({});
const loading = ref(true);
const error = ref(null);

onMounted(async () => {
  try {
    const res = await axios.get(`/api/jobs/${jobId}`);
    job.value = res.data;
    console.log(job.value);
  } catch (err) {
    error.value = "Error in fetching data: " + err.message;
  } finally {
    loading.value = false;
  }
});

const deleteJob = async () => {
  try {
    const confirm = window.confirm("Are you sure you want to delete this job?");
    if (confirm) {
      await axios.delete(`/api/jobs/${jobId}`);
      toast.success("Job deleted successfully");
      router.push("/jobs");
    }
  } catch (error) {
    console.error("Error:", error);
    toast.error("Job failed to be deleted");
  }
};
</script>

<template>
  <BackButton />
  <!-- Show loading spinner while loading is true -->
  <div v-if="loading" class="text-center text-gray-500 justify-center">
    <PulseLoader />
  </div>

  <!-- Show error message if there was an error -->
  <div v-if="error" class="text-center text-red-500">
    {{ error }}
  </div>

  <!-- Show job details once data is fetched and there's no error -->
  <section v-else class="bg-green-50">
    <div class="container m-auto py-10 px-6">
      <div class="grid grid-cols-1 md:grid-cols-70/30 w-full gap-6">
        <main>
          <div
            class="bg-white p-6 rounded-lg shadow-md text-center md:text-left"
          >
            <div class="text-gray-500 mb-4">{{ job.type }}</div>
            <h1 class="text-3xl font-bold mb-4">{{ job.title }}</h1>
            <div
              class="text-gray-500 mb-4 flex align-middle justify-center md:justify-start"
            >
              <i class="pi pi-map-marker text-lg text-orange-700 mr-2"></i>
              <p class="text-orange-700">{{ job.location }}</p>
            </div>
          </div>

          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-green-800 text-lg font-bold mb-6">
              Job Description
            </h3>

            <p class="mb-4">
              {{ job.description }}
            </p>

            <h3 class="text-green-800 text-lg font-bold mb-2">Salary</h3>

            <p class="mb-4">{{ job.salary }} / Year</p>
          </div>
        </main>

        <!-- Sidebar -->
        <aside>
          <!-- Company Info -->
          <div class="bg-white p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-bold mb-6">Company Info</h3>

            <h2 class="text-2xl">{{ job.company?.name }}</h2>

            <p class="my-2">{{ job.company?.description }}</p>

            <hr class="my-4" />

            <h3 class="text-xl">Contact Email:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ job.company?.contactEmail }}
            </p>

            <h3 class="text-xl">Contact Phone:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ job.company?.contactPhone }}
            </p>
          </div>

          <!-- Manage -->
          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-xl font-bold mb-6">Manage Job</h3>
            <RouterLink
              :to="`/jobs/edit/${job.id}`"
              class="bg-green-500 hover:bg-green-600 text-white text-center font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
              >Edit Job</RouterLink
            >
            <button
              @click="deleteJob"
              class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
            >
              Delete Job
            </button>
          </div>
        </aside>
      </div>
    </div>
  </section>
</template>
