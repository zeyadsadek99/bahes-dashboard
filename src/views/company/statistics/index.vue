<template>
  <div class="container mx-auto p-6">
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

      <!-- Orders Section -->
      <section
        v-if="orders && Object.keys(orders).length"
        class="bg-white shadow-lg rounded-lg p-6"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-text">
            {{ t('LABELS.orders') }}
          </h2>
        </header>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div
            v-for="(value, key) in orders"
            :key="key"
            class="p-4 bg-mainBg rounded-lg shadow-card"
          >
            <h3 class="text-md font-semibold text-primary mb-2">
              {{ formatLabel(key) }}
            </h3>
            <p class="text-2xl font-extrabold text-reserved">{{ value }}</p>
          </div>
        </div>
      </section>

      <!-- Users Section -->
      <section
        v-if="users && Object.keys(users).length"
        class="bg-white shadow-lg rounded-lg p-6"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-text">
            {{ t("LABELS.users") }}
          </h2>
        </header>

        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="(user, index) in users.most_request"
            :key="index"
            class="bg-empty p-4 rounded-lg shadow-sm"
          >
            <h3 class="text-lg font-semibold text-text">
              {{ user.full_name || "Unknown" }}
            </h3>
            <p class="text-sm text-sub">
              {{ user.email || "No email provided" }}
            </p>
            <p class="text-sm text-sub mt-2">
              {{ t("LABELS.orderCount") }}: 
              <span class="font-bold">{{ user.client_orders_count || 0 }}</span>
            </p>
          </div>
        </div>
      </section>

      <!-- Vendors Section -->
      <section
        v-if="vendors && Object.keys(vendors).length"
        class="bg-white shadow-lg rounded-lg p-6"
      >
        <header class="border-b pb-4 mb-4">
          <h2 class="text-xl font-bold text-text">
            {{ t("LABELS.vendors") }}
          </h2>
        </header>

        <!-- Most Requests Section -->
        <h3 class="text-lg font-semibold text-label mb-4">
          {{ t("LABELS.mostRequests") }}
        </h3>
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
          <div
            v-for="(vendor, index) in vendors.most_request"
            :key="index"
            class="bg-empty p-4 rounded-lg shadow-sm"
          >
            <h3 class="text-lg font-semibold text-text">
              {{ vendor.full_name || "Unknown" }}
            </h3>
            <p class="text-sm text-sub">
              {{ vendor.email || "No email provided" }}
            </p>
            <p class="text-sm text-sub mt-2">
              {{ t("LABELS.orderCount") }}: 
              <span class="font-bold">{{ vendor.vendor_orders_count || 0 }}</span>
            </p>
          </div>
        </div>

        <!-- Most Reviews Section -->
        <h3 class="text-lg font-semibold text-label mb-4">
          {{ t("LABELS.mostReviews") }}
        </h3>
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="(vendor, index) in vendors.most_review"
            :key="index"
            class="bg-empty p-4 rounded-lg shadow-sm"
          >
            <h3 class="text-lg font-semibold text-text">
              {{ vendor.full_name || "Unknown" }}
            </h3>
            <p class="text-sm text-sub">
              {{ vendor.email || "No email provided" }}
            </p>
            <p class="text-sm text-sub mt-2">
              {{ t("LABELS.reviewCount") }}: 
              <span class="font-bold">{{ vendor.vendor_reviews_count || 0 }}</span>
            </p>
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
  most_request: [],
  most_review: [],
});

const orders = reactive({
  daily_request: 0,
  completed_request: 0,
  rejected_request: 0,
});

const users = reactive({
  most_request: [],
  client_count: 0,
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
      vendors.most_request = result.vendor.most_request || [];
      vendors.most_review = result.vendor.most_review || [];

      // Populate Orders Data
      orders.daily_request = result.orders.daily_request || 0;
      orders.completed_request = result.orders.completed_request || 0;
      orders.rejected_request = result.orders.rejected_request || 0;

      // Populate Users Data
      users.most_request = result.users.most_request || [];
      users.client_count = result.users.client_count || 0;

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
.bg-empty {
  background-color: #f9fafb; /* Light gray background */
}
</style>
