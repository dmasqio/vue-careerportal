<template>
  <div class="flex flex-col items-center">
    <h1>Job Search</h1>
    <div class="mb-4">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search jobs..."
        class="p-2 border border-gray-300 rounded-md w-full"
      />
      <button
        @click="fetchJobs"
        class="ml-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Search
      </button>
    </div>
  </div>
  <div class="flex">
    <div class="w-1/2 p-4">
      <h1>Job Search Results</h1>
      <div
        v-for="job in jobs"
        :key="job.id"
        :class="{ active: selectedJob === job }"
        class="rounded-lg bg-white shadow my-4"
      >
        <div class="p-6">
          <div class="flex items-center space-x-3">
            <h3 class="truncate text-sm font-medium text-gray-900">
              <a @click="selectJob(job)">{{ job.title }}</a>
            </h3>
          </div>
          <p class="mt-1 truncate text-sm text-gray-500">
            {{ truncateDescription(stripHTML(job.publicDescription), 250) }}
          </p>
          <div class="flex items-center justify-between mt-4">
            <div class="flex items-center">
              <svg
                class="h-4 w-4 fill-current text-gray-400"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path d="M10 12a2 2 0 100-4 2 2 0 000 4z" />
                <path
                  fill-rule="evenodd"
                  d="M10 18a8 8 0 100-16 8 8 0 000 16zm0-2a6 6 0 100-12 6 6 0 000 12zm0-10a2 2 0 110 4 2 2 0 010-4z"
                  clip-rule="evenodd"
                />
              </svg>
              <span class="text-xs"
                >{{ job.address.city }}, {{ job.address.state }},
                {{ job.address.zip }}</span
              >
            </div>
            <span class="text-xs">{{ job.employmentType }}</span>
          </div>
        </div>
      </div>
    </div>
    <div class="w-1/2 p-8">
      <h1>Job Details</h1>
      <div v-if="selectedJob">
        <div class="overflow-hidden bg-white shadow sm:rounded-lg">
          <div class="px-4 py-6 sm:px-6">
            <h3 class="text-base font-semibold leading-7 text-gray-900">
              {{ selectedJob.title }}
            </h3>
            <p class="mt-1 max-w-2xl text-sm leading-6 text-gray-500">
              Job details and description.
            </p>
          </div>
          <div class="border-t border-gray-100">
            <dl class="divide-y divide-gray-100">
              <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
                <dt class="text-sm font-medium text-gray-900">Location</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2">
                  {{ selectedJob.address.city }},
                  {{ selectedJob.address.state }}, {{ selectedJob.address.zip }}
                </dd>
              </div>
              <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
                <dt class="text-sm font-medium text-gray-900">
                  Employment Type
                </dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2">
                  {{ selectedJob.employmentType }}
                </dd>
              </div>
              <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
                <dt class="text-sm font-medium text-gray-900">
                  Public Description
                </dt>
                <dd
                  class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2"
                  style="text-align: left"
                >
                  {{ stripHTML(selectedJob.publicDescription) }}
                </dd>
              </div>
            </dl>
          </div>
          <div class="px-4 py-4 sm:px-6">
            <button
              @click="openApplyModal(selectedJob)"
              class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
            >
              Apply
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Apply Modal -->
    <div
      v-if="isApplyModalOpen"
      class="fixed inset-0 flex items-center justify-center z-50"
    >
      <div class="absolute inset-0 bg-gray-800 opacity-75"></div>
      <div class="relative bg-white rounded-lg px-8 py-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">
          Apply for {{ selectedJob.title }}
        </h2>
        <form>
          <div class="mb-4">
            <label
              for="first-name"
              class="block text-sm font-medium text-gray-700"
              >First Name</label
            >
            <input
              id="first-name"
              type="text"
              v-model="applicant.firstName"
              class="mt-1 focus:ring-indigo-500 focus:border-indigo-500 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="mb-4">
            <label
              for="last-name"
              class="block text-sm font-medium text-gray-700"
              >Last Name</label
            >
            <input
              id="last-name"
              type="text"
              v-model="applicant.lastName"
              class="mt-1 focus:ring-indigo-500 focus:border-indigo-500 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="mb-4">
            <label for="email" class="block text-sm font-medium text-gray-700"
              >Email</label
            >
            <input
              id="email"
              type="email"
              v-model="applicant.email"
              class="mt-1 focus:ring-indigo-500 focus:border-indigo-500 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="mb-4">
            <label for="phone" class="block text-sm font-medium text-gray-700"
              >Phone</label
            >
            <input
              id="phone"
              type="tel"
              v-model="applicant.phone"
              class="mt-1 focus:ring-indigo-500 focus:border-indigo-500 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="mb-4">
            <label for="resume" class="block text-sm font-medium text-gray-700"
              >Resume</label
            >
            <input
              id="resume"
              type="file"
              @change="handleResumeUpload"
              accept=".pdf,.doc,.docx"
              class="mt-1 focus:ring-indigo-500 focus:border-indigo-500 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="flex justify-end">
            <button
              type="submit"
              @click.prevent="submitApplication"
              class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
            >
              Submit
            </button>
            <button
              @click.prevent="closeApplyModal"
              class="ml-2 px-4 py-2 bg-gray-300 text-gray-700 rounded hover:bg-gray-400"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jobs: [],
      selectedJob: null,
      isApplyModalOpen: false,
      applicant: {
        firstName: "",
        lastName: "",
        email: "",
        phone: "",
        resume: null,
      },
    };
  },
  mounted() {
    this.fetchJobs();
  },
  methods: {
    async fetchJobs() {
      try {
        const response = await fetch(
          "https://public-rest41.bullhornstaffing.com/rest-services/DJVM1/search/JobOrder?query=(isOpen:1)&fields=id,title,publishedCategory(id,name),address(city,state,zip),employmentType,dateLastPublished,publicDescription&count=500&sort=-dateLastPublished&start=0"
        );
        const data = await response.json();
        this.jobs = data.data;
        this.selectedJob = data.data[0]; // Select the first job by default
      } catch (error) {
        console.error("Error fetching job data:", error);
      }
    },
    selectJob(job) {
      this.selectedJob = job;
    },
    truncateDescription(description, maxLength) {
      if (description.length <= maxLength) {
        return description;
      }
      return description.slice(0, maxLength) + "...";
    },
    stripHTML(html) {
      const doc = new DOMParser().parseFromString(html, "text/html");
      return doc.body.textContent || "";
    },
    openApplyModal(job) {
      this.selectedJob = job;
      this.isApplyModalOpen = true;
    },
    closeApplyModal() {
      this.isApplyModalOpen = false;
    },
    handleResumeUpload(event) {
      const file = event.target.files[0];
      this.applicant.resume = file;
    },
    async submitApplication() {
      const { firstName, lastName, email, phone, resume } = this.applicant;

      // Send the applicant data and resume to Bullhorn API
      const formData = new FormData();
      formData.append("externalID", "Resume");
      formData.append("type", "Resume");
      formData.append("firstName", firstName);
      formData.append("lastName", lastName);
      formData.append("email", email);
      formData.append("phone", phone);
      formData.append("resume", resume);

      try {
        const response = await fetch(
          `https://public-rest41.bullhornstaffing.com/rest-services/DJVM1/apply/${this.selectedJob.id}/raw?format=docx`,
          {
            method: "POST",
            body: formData,
          }
        );

        const data = await response.json();
        console.log(data); // Handle the response data as per your requirements
      } catch (error) {
        console.error("Error submitting application:", error);
      }

      // Reset the applicant data
      this.applicant = {
        firstName: "",
        lastName: "",
        email: "",
        phone: "",
        resume: null,
      };

      // Close the modal
      this.isApplyModalOpen = false;
    },
  },
};
</script>

<style scoped>
.job-description {
  padding: 10px;
  border: 1px solid rgb(236, 236, 236);
  background-color: grey-100;
  margin-bottom: 10px;
}
.job-description p {
  margin: 10px 0;
}
</style>
