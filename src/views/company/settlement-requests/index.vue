<template>
  <div class="h-full flex flex-col">
    <breadcrumbs :items="breadItems" :title="$t('LABELS.settlement-requests')" />
    <div
      class="bg-white rounded-3xl h-full shadow-[0_7px_6px_0px,rgba(#B1B1B11A)] md:p-7 flex-1 flex flex-col"
    >
      <base-filter
        name="settlement-requests"
        :inputs="[]"
        :btn-name="t(`BUTTONS.Edit`, { name: t('LABELS.settlement-requests') })"
        icon="fas fa-plus"
        :keyword="true"
        @action="$router.push('/settlement-requests/form')"
      />
      <v-data-table-virtual
        :headers="headers"
        :items="[items]"
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
                {{ $t(`BUTTONS.add`, { name: $t("LABELS.settlement-requests") }) }}
              </router-link>
            </div>
          </div>
          <h3 v-else class="mt-4 font-semibold text-text text-center">
            {{ $t("TITLES.No Search result") }}
          </h3>
        </template>
        <!-- {{ item.full_name }} -->
        <template v-slot:[`item.name`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card
              :title="`${item.full_name}`"
              :image="item.image"
              :text="item.email"
            />
          </div>

        </template>

        <template v-slot:[`item.country`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card :title="`${item.country?.name}`" :image="item.country?.flag"
            />
          </div>
        </template>
        <div
              class="flex items-center mt-1 gap-2"
              v-if="product.status == 'pending'"
            >
              <button
                type="button"
                class="bg-error rounded-full p-2 w-[32px] h-[32px]"
                @click="openCancelModel = true"
              >
                <svg-icon name="refuse" class="m-auto" />
              </button>
              <button
                type="button"
                class="bg-collected rounded-full w-[32px] h-[32px] p-1"
                @click="openConfirmModel = true"
              >
                <svg-icon name="accept" class="m-auto" />
              </button>
              <Teleport to="body">
                <ConfirmModal
                  :disabled="btnConfirmLoading"
                  v-if="openConfirmModel"
                  message=""
                  @cancel="openConfirmModel = false"
                  btn-confirm="base-btn py-4 rounded-xl"
                  button-name="BUTTONS.Yes Accept"
                  @confirm="acceptOrder(product.id)"
                />
              </Teleport>
              <teleport to="body">
                <modal
                  :persist="true"
                  v-if="openCancelModel"
                  :title="t('LABELS.reject order')"
                  @close="resetCancelForm"
                >
                  <VeeForm
                    :validation-schema="schema"
                    @submit="(values) => cancelOrder(values, product.id)"
                    :initial-values="initialValues"
                  >
                    <base-input
                      id="reject_reason"
                      name="reject_reason"
                      :placeholder="$t('LABELS.choose')"
                      :label="$t('LABELS.Rejection reason')"
                      type="textarea"
                      rows="4"
                    />

                    <div class="flex items-center justify-end gap-2">
                      <button
                        class="white-btn rounded-lg"
                        type="button"
                        @click="openCancelModel = false"
                      >
                        {{ $t("BUTTONS.cancel") }}
                      </button>
                      <button
                        class="base-btn rounded-lg py-3"
                        :disabled="btnLoading"
                        type="submit"
                      >
                        {{ $t("BUTTONS.save") }}
                      </button>
                    </div>
                  </VeeForm>
                </modal>
              </teleport>
            </div>
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
  // {
  //   title: t("LABELS.activation"),
  //   align: "start",
  //   sortable: false,
  //   key: "is_admin_active_user",
  // },

  // {
  //   title: t("LABELS.Actions"),
  //   align: "start",
  //   sortable: false,
  //   key: "actions",
  // },
];

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
