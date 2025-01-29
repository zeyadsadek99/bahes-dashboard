<template>
  <div class="h-full flex flex-col">
    <breadcrumbs :items="breadItems" :title="$t('LABELS.requests-product')" />
    <div
      class="bg-white rounded-3xl h-full shadow-[0_7px_6px_0px,rgba(#B1B1B11A)] md:p-7 flex-1 flex flex-col"
    >
      <base-filter
        name="request"
        :inputs="[]"
        icon="fas fa-plus"
        :keyword="true"
      />
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
                  name: $t("LABELS.requests-product"),
                })
              }}
            </h3>
            <div class="flex items-center justify-center mt-7 gap-2 flex-wrap">
              <router-link
                to="/requests-product/form"
                class="base-btn rounded-xl self-end"
              >
                <i class="fas fa-plus"></i>
                {{ $t(`BUTTONS.add`, { name: $t("LABELS.requests-product") }) }}
              </router-link>
            </div>
          </div>
          <h3 v-else class="mt-4 font-semibold text-text text-center">
            {{ $t("TITLES.No Search result") }}
          </h3>
        </template>

        <template v-slot:[`item.name`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card :title="`${item.vendor?.full_name}`" />
          </div>
        </template>

        <template v-slot:[`item.action`]="{ item }">
          <div class="flex flex-wrap gap-2" >
            <button
              type="button"
              class="bg-error rounded-full p-2 w-[32px] h-[32px]"
              @click="openCancelModel = item.id"
            >
              <svg-icon name="refuse" class="m-auto" />
            </button>
            <button
              type="button"
              class="bg-collected rounded-full w-[32px] h-[32px] p-1"
              @click="openConfirmModel = item.id"
            >
              <svg-icon name="accept" class="m-auto" />
            </button>
            <!-- <Teleport to="body">
              <ConfirmModal
                :disabled="btnConfirmLoading"
                v-if="openConfirmModel == item.id"
                message=""
                @cancel="openConfirmModel = ''"
                btn-confirm="base-btn py-4 rounded-xl"
                button-name="BUTTONS.Yes Accept"
                @confirm="acceptOrder(item.id)"
              />
            </Teleport> -->
            <teleport to="body">
              <modal
                :persist="true"
                v-if="openConfirmModel == item.id"
                :title="t('LABELS.Accept')"
                @close="resetConfirmForm"
              >
                <VeeForm
                  :validation-schema="schema"
                  @submit="(values) => acceptOrder(values, item.id)"
                  :initial-values="initialValues"
                >
                  <!-- <base-input
                    id="reject_reason"
                    name="reject_reason"
                    :placeholder="$t('LABELS.choose')"
                    :label="$t('LABELS.Rejection reason')"
                    type="textarea"
                    rows="4"
                  /> -->
                  <!-- <div class="w-fit relative">
                <base-file
                  @uploading="btnLoading = $event"
                  modalName="users"
                  modalType="image"
                  id="image"
                  name="image"
                  :placeholder="$t('LABELS.image')"
                  label=""
                  v-model:itemValue="initialValues.preview"
                  v-model:image="initialValues.image"
                  accept="image/png, image/webp, image/jpeg"
                  :no_preview="true"
                />

                <label
                  for="image"
                  class="w-10 h-10 end-7 bg-primary rounded-full flex justify-center items-center absolute -bottom-2"
                >
                  <img
                    class="!object-contain"
                    src="@/assets/images/icons/solar_upload-linear.png"
                    alt="solar icon"
                  />
                </label>
              </div> -->
                  <BaseFile
                    @uploading="btnLoading = $event"
                    modalName="users"
                    modalType="file"
                    v-model:itemValue="initialValues.file"
                    v-model:image="initialValues.file"
                    id="file-upload"
                    name="file"
                    label="Upload Excel File"
                    :accept="'.xlsx'"
                    :no_preview="true"
                    placeholder="Upload Excel File"


                    @update:itemValue="handleFileUpload"
                  />

                  <div class="flex items-center justify-end gap-2">
                    <button
                      class="white-btn rounded-lg"
                      type="button"
                      @click="openConfirmModel = ''"
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
            <teleport to="body">
              <modal
                :persist="true"
                v-if="openCancelModel == item.id"
                :title="t('LABELS.Reject order')"
                @close="resetCancelForm"
              >
                <VeeForm
                  :validation-schema="schema"
                  @submit="(values) => cancelOrder(values, item.id)"
                  :initial-values="initialValues"
                >
                  <base-input
                    id="nameAr"
                    name="nameAr"
                    :placeholder="$t('LABELS.nameAr')"
                    :label="$t('LABELS.nameAr')"
                    type="text"
                  />

                  <base-input
                    id="nameEn"
                    name="nameEn"
                    :placeholder="$t('LABELS.nameEn')"
                    :label="$t('LABELS.nameEn')"
                    type="text"
                  />

                  <base-input
                    id="reject_reason"
                    name="reject_reason"
                    :placeholder="$t('LABELS.choose')"
                    :label="$t('LABELS.Rejection reason')"
                    type="textarea"
                    rows="4"
                  />

                  <!-- <base-file
                    name="file"
                    :label="$t('LABELS.file')"
                    :placeholder="$t('LABELS.choose')"
                  /> -->

                  <div class="flex items-center justify-end gap-2">
                    <button
                      class="white-btn rounded-lg"
                      type="button"
                      @click="openCancelModel = ''"
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
          <!-- <div >
            <p
              :class="getStatusColor(item.status)"
              class="inline-flex p-1 rounded-full"
            >
              {{ $t(`STATUS.${item.status}`) }}
            </p>
          </div> -->
        </template>
        <!--  -->
        <!-- <template v-slot:[`item.actions`]="{ item, index }">
          <div class="flex items-center gap-4">
            <router-link :to="`/requests-product/form/${item.id}`">
              <svg-icon class="text-primary" name="edit" filled />
            </router-link>
            <Deleter
              :url="`product-requests/${item.id}`"
              :id="item.id"
              method="DELETE"
              @remove="items.splice(index, 1)"
            />
          </div>
        </template> -->
      </v-data-table-virtual>
    </div>
    <base-pagination :item="paginator" v-if="paginator" />
    <teleport to="body">
      <notify
        @close="failmodal = false"
        v-if="failmodal"
        class="z-[99999]"
        classes=" px-6  py-11 !w-full  !max-w-[350px]"
      >
        <svg-icon name="warning-2" class="mb-4 mx-auto" />
        <h3 class="text-text font-bold mb-2 text-2xl text-center">
          {{ $t("Sorry") }}
        </h3>
        <p class="text-center text-sub">
          {{ failmessage }}
        </p>
      </notify>
    </teleport>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted, watch, reactive } from "vue";
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
    name: t("LABELS.requests-product"),
    path: "/requests-product",
    imgIcon: "",
  },
];

const items = ref([]);
const loading = ref(false);
const paginator = ref(null);
const openConfirmModel = ref("");
const btnConfirmLoading = ref(false);
const openCancelModel = ref("");
const btnCancelLoading = ref(false);
const failmodal = ref(false);
const failmessage = ref("");
const headers = [
  {
    title: t("LABELS.Name", { name: t("LABELS.request") }),
    align: "start",
    sortable: false,
    key: "name",
  },

  // {
  //   title: t("LABELS.activation"),
  //   align: "start",
  //   sortable: false,
  //   key: "is_admin_active_user",
  // },

  {
    title: t("LABELS.Actions"),
    align: "start",
    sortable: false,
    key: "action",
  },
];
const initialValues = reactive({
  file: "",
  nameAr: "",
  nameEn: "",
  reject_reason: "",
});
function fetchData() {
  loading.value = true;
  axios
    .get("product-requests", {
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
function resetCancelForm() {
  initialValues.nameAr = "";
  initialValues.nameEn = "";
  openCancelModel.value = "";
}
function resetConfirmForm() {
  initialValues.file = "";
  openConfirmModel.value = "";
}

function cancelOrder(values, id) {
  btnCancelLoading.value = true;
  const frmData = new FormData();

  frmData.append("nameAr", values.nameAr);
  frmData.append("nameEn", values.nameEn);

  axios
    .patch(
      `product-requests/${id}/reject?reason=${values.reject_reason}`,
      frmData
    )
    .then((res) => {
      btnCancelLoading.value = false;
      resetCancelForm();
      items.value.map((el) => {
        if (el.id == id) {
          el.status = res.data.data.status;

          return el;
        }
      });
    })
    .catch((err) => {
      btnCancelLoading.value = false;
      toast.error(err.response.data.message);
    });
}

function acceptOrder(values, id) {
  btnConfirmLoading.value = true;
  const frmData = new FormData();

  frmData.append("_method", "put");
  frmData.append("file", values.file);
  axios
    .post(`product-requests/${id}/accept-and-store`, frmData)
    .then((res) => {
      btnConfirmLoading.value = false;
      openConfirmModel.value = "";
      items.value.map((el) => {
        if (el.id == id) {
          el.status = res.data.data.status;

          return el;
        }
      });
    })
    .catch((err) => {
      openConfirmModel.value = "";
      btnConfirmLoading.value = false;

      failmodal.value = true;
      failmessage.value = err.response.data.message;
      setTimeout(() => {
        failmodal.value = false;
        failmessage.value = "";
      }, 3000);
    });
}

watch(
  () => route.query,
  () => fetchData()
);

onMounted(() => {
  fetchData();
});
</script>
