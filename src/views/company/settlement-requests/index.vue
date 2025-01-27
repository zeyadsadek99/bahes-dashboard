<template>
  <div class="h-full flex flex-col">
    <breadcrumbs
      :items="breadItems"
      :title="$t('LABELS.settlement-requests')"
    />
    <div
      class="bg-white rounded-3xl h-full shadow-[0_7px_6px_0px,rgba(#B1B1B11A)] md:p-7 flex-1 flex flex-col"
    >
      <v-data-table-virtual
        :headers="headers"
        :items="items"
        :loading="loading"
        item-value="id"
        class="project-table"
        item-key="id"
        :no-data-text="$t('TEXTS.noData')"
      >
        <template v-slot:loading>
          <loader class="py-7" />
        </template>

        <template v-slot:no-data>
          <div
            class="text-center"
            v-if="!$route.query.keyword && !$route.query.status"
          >
            <h3 class="mt-4 font-semibold text-text text-center">
              {{
                $t("TITLES.No have been added yet", {
                  name: $t("LABELS.settlement-requests"),
                })
              }}
            </h3>
            <div class="flex items-center justify-center mt-7 gap-2 flex-wrap">
              <router-link
                to="/settlement-requests/form"
                class="base-btn rounded-xl self-end"
              >
                <i class="fas fa-plus"></i>
                {{
                  $t(`BUTTONS.add`, { name: $t("LABELS.settlement-requests") })
                }}
              </router-link>
            </div>
          </div>
          <h3 v-else class="mt-4 font-semibold text-text text-center">
            {{ $t("TITLES.No Search result") }}
          </h3>

          <div>
            <button @click="handleRequest('reject')">
              <svg-icon class="text-primary" name="close" filled />
            </button>
            <button @click="handleRequest('accept')">
              <svg-icon class="text-primary" name="checked" filled />
            </button>
          </div>

          <Teleport to="body">
            <Modal
              class="z-[1009]"
              :title="$t('TITLES.confirmProcess')"
              v-if="openModal"
              @close="openModal = false"
            >
              <img
                width="60"
                class="mx-auto"
                src="@/assets/images/icons/list.png"
                alt="list"
              />
              <h4 class="text-center font-bold text-lg mt-4">
                {{ $t("TITLES.areYouSureToConfirm") }}
              </h4>
              <div v-if="status === 'accept'" class="mt-4 px-4">
                <label class="block text-sm font-medium text-gray-700 mb-1">
                  {{ $t("LABELS.amount") }}
                </label>
                <input
                  type="number"
                  v-model="amount"
                  class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-primary focus:border-primary"
                  :placeholder="$t('PLACEHOLDERS.enterAmount')"
                />
              </div>
              <div v-else class="mt-4 px-4">
                <label class="block text-sm font-medium text-gray-700 mb-1">
                  {{ $t("LABELS.note") }}
                </label>
                <textarea
                  v-model="note"
                  class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-primary focus:border-primary"
                  :placeholder="$t('PLACEHOLDERS.enterNote')"
                ></textarea>
              </div>

              <div class="flex items-center justify-center gap-2 mt-5">
                <button
                  type="button"
                  class="capitalize base-btn-white"
                  @click="openModal = false"
                >
                  {{ $t("BUTTONS.cancel") }}
                </button>
                <button
                  type="button"
                  class="capitalize base-btn"
                  @click="confirm"
                >
                  {{ $t("BUTTONS.confirm") }}
                </button>
              </div>
            </Modal>
          </Teleport>
        </template>

      </v-data-table-virtual>
    </div>
    <base-pagination :item="paginator" v-if="paginator" />
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted, watch } from "vue";
import { useI18n } from "vue-i18n";
import { useRoute } from "vue-router";

const route = useRoute();
const { t } = useI18n();

const breadItems = [
  {
    name: t("TITLES.home"),
    path: "/",
    imgIcon: "settings.svg",
  },
  {
    name: t("LABELS.settlement-requests"),
    path: "/settlement-requests",
    imgIcon: "",
  },
];

const items = ref([]);
const loading = ref(false);
const paginator = ref(null);
const amount = ref(0);
const note = ref("");
const openModal = ref(false);
const status = ref("");
const headers = [
  {
    title: t("LABELS.Name", { name: t("LABELS.settlement-requests") }),
    align: "start",
    sortable: false,
    key: "name",
  },
  {
    title: t("LABELS.country"),
    align: "start",
    sortable: false,
    key: "country",
  },
];

function handleRequest(requestStatus) {
  status.value = requestStatus;
  amount.value = 0; // Reset amount
  note.value = "";  // Reset note
  openModal.value = true;
}
function closeModal() {
  openModal.value = false;
  amount.value = 0; // Reset amount
  note.value = "";  // Reset note
}
function confirm() {
  const id = 1;
  let url = `settlement-requests/${id}?status=${status.value}`;
  if (status.value === "accept") {
    url += `&amount=${amount.value}`;
  } else if (status.value === "reject") {
    url += `&note=${note.value}`;
  }

  axios
    .patch(url)
    .then((res) => {
      console.log(res.data.message);
      closeModal();

    })
    .catch((e) => {
      console.error(e.response.data.message);
      // closeModal();

    });
}

function fetchData() {
  loading.value = true;
  axios
    .get("settlement-requests", {
      params: {
        keyword: route.query.keyword || "",
      },
    })
    .then((res) => {
      items.value = res.data.data;
      paginator.value = res.data.meta;
      loading.value = false;
    })
    .catch(() => (loading.value = false));
}

watch(
  () => route.query,
  () => fetchData()
);

onMounted(() => {
  fetchData();
});
</script>