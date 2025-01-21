<template>
  <div>
    <breadcrumbs
      back="/statistics"
      :title="$t('LABELS.statistics')"
      :items="breads"
    />

    <div class="flex flex-col gap-6 mt-6">
      <!-- Loading Skeleton -->
      <div v-if="loading">
        <FormSkelton />
      </div>

      <!-- Vendors Section -->
      <section
        v-if="vendors && Object.keys(vendors).length"
        class="bg-white shadow-lg rounded-lg p-8"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-gray-800">
            {{ t('LABELS.vendors') }}
          </h2>
        </header>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div
            v-for="(value, key) in vendors"
            :key="key"
            class="p-6 bg-blue-50 rounded-lg shadow-md hover:shadow-lg transition-shadow"
          >
            <h3 class="text-md font-semibold text-blue-800 mb-2">
              {{ formatLabel(key) }}
            </h3>
            <p class="text-2xl font-extrabold text-blue-600">{{ value }}</p>
          </div>
        </div>
      </section>

      <!-- Orders Section -->
      <section
        v-if="orders && Object.keys(orders).length"
        class="bg-white shadow-lg rounded-lg p-8"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-gray-800">
            {{ t('LABELS.orders') }}
          </h2>
        </header>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div
            v-for="(value, key) in orders"
            :key="key"
            class="p-6 bg-green-50 rounded-lg shadow-md hover:shadow-lg transition-shadow"
          >
            <h3 class="text-md font-semibold text-green-800 mb-2">
              {{ formatLabel(key) }}
            </h3>
            <p class="text-2xl font-extrabold text-green-600">{{ value }}</p>
          </div>
        </div>
      </section>

      <!-- Users Section -->
      <section
        v-if="users && Object.keys(users).length"
        class="bg-white shadow-lg rounded-lg p-8"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-gray-800">
            {{ t('LABELS.users') }}
          </h2>
        </header>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div
            v-for="(value, key) in users"
            :key="key"
            class="p-6 bg-yellow-50 rounded-lg shadow-md hover:shadow-lg transition-shadow"
          >
            <h3 class="text-md font-semibold text-yellow-800 mb-2">
              {{ formatLabel(key) }}
            </h3>
            <p class="text-2xl font-extrabold text-yellow-600">{{ value }}</p>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>


<script setup>
import { reactive, ref, onBeforeMount } from "vue";
import { useI18n } from "vue-i18n";
import axios from "axios";
import { toast } from "vue3-toastify";

const { t } = useI18n();

const vendors = reactive({
  most_request_count: 0,
  most_review_count: 0,
});

const orders = reactive({
  daily_request: 0,
  completed_request: 0,
  rejected_request: 0,
});

const users = reactive({
  client_count: 0,
  admin_count: 0,
});

const loading = ref(true);

const breads = [
  {
    path: "/",
    imgIcon: "menu-setup.svg",
    name: t("TITLES.home"),
  },
  {
    name: t("LABELS.statistics"),
    path: "/statistics",
    imgIcon: "",
  },
];

function getData() {
  axios
    .get(`statistics`)
    .then((res) => {
      const result = res.data.data;

      // Populate Vendors Data
      vendors.most_request_count = result.vendor.most_request.length || 0;
      vendors.most_review_count = result.vendor.most_review.length || 0;

      // Populate Orders Data
      orders.daily_request = result.orders.daily_request || 0;
      orders.completed_request = result.orders.completed_request || 0;
      orders.rejected_request = result.orders.rejected_request || 0;

      // Populate Users Data
      users.client_count = result.users.client_count || 0;
      users.admin_count = result.users.admin_count || 0;

      loading.value = false;
    })
    .catch(() => {
      toast.error(t("STATUS.failedToLoadData"));
      loading.value = false;
    });
}

onBeforeMount(() => {
  getData();
});

const formatLabel = (key) => {
  return t(`LABELS.${key}`);
};
</script>

<style scoped>
/* Add custom styles here if needed */
</style>
