<template>
  <div>
    <breadcrumbs
      back="/statistics"
      :title="$t('LABELS.statistics')"
      :items="breads"
    />

    <div class="flex gap-4 flex-wrap mt-6">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />

        <template v-else>
          <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
            <div
              v-for="(value, key) in initialValues"
              :key="key"
              class="p-6 bg-gradient-to-r from-gray-50 to-gray rounded-lg shadow-md border border-gray-200 hover:shadow-lg hover:border-blue-500 transition-all duration-300 flex flex-col items-start"
            >
              <!-- Icon for each item -->
              <div class="flex items-center justify-between w-full mb-2">
                <span class="text-merged text-sm font-medium">
                  {{ formatLabel(key) }}
                </span>
                
              </div>

              <!-- Main value -->
              <div
                class="text-xl font-bold text-blue mt-2"
                :class="{
                  'text-green-500': value > 100, // Dynamic color for positive values
                  'text-red-500': value == 0, // Dynamic color for zero values
                }"
              >
                {{ value }}
              </div>

              <!-- Tooltip or Helper Text -->
              <!-- <div
                v-if="key === 'daily_request'"
                class="text-gray-400 text-xs mt-1"
              >
                {{ $t("TOOLTIPS.dailyRequest") }}
              </div> -->
            </div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, onBeforeMount } from "vue";
import { useI18n } from "vue-i18n";
import axios from "axios";
import { toast } from "vue3-toastify";

const { t } = useI18n();

const initialValues = reactive({
  daily_request: 0,
  completed_request: 0,
  rejected_request: 0,
  client_count: 0,
  vendor_most_request_count: 0,
  vendor_most_review_count: 0,
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

      // Update values from the response
      initialValues.daily_request = result.orders.daily_request || 0;
      initialValues.completed_request = result.orders.completed_request || 0;
      initialValues.rejected_request = result.orders.rejected_request || 0;
      initialValues.client_count = result.users.client_count || 0;
      initialValues.vendor_most_request_count =
        result.vendor.most_request.length || 0;
      initialValues.vendor_most_review_count =
        result.vendor.most_review.length || 0;

      loading.value = false;
    })
    .catch((error) => {
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
