<template>
  <div>
    <breadcrumbs
      back="/users"
      :title="$t('LABELS.users')"
      :items="breads"
    />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.User') })"
          >
            <VeeForm
              :validation-schema="schema"
              @submit="handleSubmit"
              class="profile_page"
            >
              <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
                <base-input
                  id="storeName"
                  name="storeName"
                  :placeholder="$t('LABELS.storeName')"
                  :label="$t('LABELS.storeName')"
                  type="text"
                />
                <base-input
                  id="storeRegion"
                  name="storeRegion"
                  :placeholder="$t('LABELS.storeRegion')"
                  :label="$t('LABELS.storeRegion')"
                  type="text"
                />
                <base-select
                  id="storeCity"
                  name="storeCity"
                  :placeholder="$t('LABELS.storeCity')"
                  :label="$t('LABELS.storeCity')"
                  :options="cities"
                  v-model:itemValue="initialValues.storeCity"
                />
                <base-select
                  id="storeActivity"
                  name="storeActivity"
                  :placeholder="$t('LABELS.storeActivity')"
                  :label="$t('LABELS.storeActivity')"
                  :options="categories"
                  v-model:itemValue="initialValues.storeActivity"
                />

                <base-input
                  id="storeLocation"
                  name="storeLocation"
                  :placeholder="$t('LABELS.storeLocation')"
                  :label="$t('LABELS.storeLocation')"
                  type="text"
                />
                
              </div>
              <div
                  class="grid grid-cols-1 md:grid-cols-3 gap-2 mb-7 relative"
                >
                  <div class="image1 relative w-52">
                    <base-file
                      @uploading="btnLoading = $event"
                      modalName="users"
                      modalType="image"
                      id="storeImage"
                      name="storeImage"
                      :placeholder="$t('LABELS.image')"
                      label=""
                      v-model:itemValue="initialValues.storeImage"
                      v-model:image="initialValues.storeImage"
                      accept="image/png, image/webp, image/jpeg"
                      :no_preview="true"
                    />

                    <label
                      for="image"
                      class="w-10 h-10 end-9 bg-primary rounded-full flex justify-center items-center absolute -bottom-1"
                    >
                      <img
                        class="!object-contain"
                        src="@/assets/images/icons/solar_upload-linear.png"
                        alt="solar icon"
                      />
                    </label>
                  </div>
                  <div class="image1 relative w-52">
                    <base-file
                      @uploading="btnLoading = $event"
                      modalName="users"
                      modalType="image"
                      id="storeCommericalRegisterImage"
                      name="storeCommericalRegisterImage"
                      :placeholder="$t('LABELS.image')"
                      label=""
                      v-model:itemValue="
                        initialValues.storeCommericalRegisterImage
                      "
                      v-model:image="initialValues.storeCommericalRegisterImage"
                      accept="image/png, image/webp, image/jpeg"
                      :no_preview="true"
                    />

                    <label
                      for="image"
                      class="w-10 h-10 end-9 bg-primary rounded-full flex justify-center items-center absolute -bottom-1"
                    >
                      <img
                        class="!object-contain"
                        src="@/assets/images/icons/solar_upload-linear.png"
                        alt="solar icon"
                      />
                    </label>
                  </div>
                </div>

              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                <router-link
                  to="/users"
                  class="capitalize font-semibold text-sub"
                >
                  {{ $t("BUTTONS.cancel") }}
                </router-link>
                <button class="base-btn" :disabled="btnLoading" type="submit">
                  {{ $t("BUTTONS.save") }}
                </button>
              </div>
            </VeeForm>
          </base-card1>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import * as yup from "yup";
import { toast } from "vue3-toastify";
import axios from "axios";
import { reactive, ref, onBeforeMount } from "vue";
import { useI18n } from "vue-i18n";

import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();

const { t } = useI18n();

const initialValues = reactive({
  // name: "",
  id: "",
  // user_type: "",
  // email: "",
  // phoneNumber: "",
  // phoneCode: "",
  // image: "",
  // preview: "",
  // password: "",
  // cPassword: "",
  storeName: "",
  storeRegion: "",
  storeCity: "",
  storeActivity: "",
  storeLocation: "",
  storeImage: "",
  storeCommericalRegisterImage: "",
  // subCategoryId: "",
  // subCategoryId2: "",
});

const schema = yup.object().shape({
  // name: yup.string().required(t("ERRORS.name")),
  // email: yup.string().required(t("ERRORS.emailAddress")),
  // phoneCode: yup.mixed().required(t("ERRORS.phoneCode")),
  // phoneNumber: yup
  //   .string()
  //   .required(t("ERRORS.isRequired", { name: t("LABELS.phone") }))
  //   .test((value, context) => {
  //     let parent = context.parent.phoneCode?.phone_number_limit;
  //     if (value.length > parent || value.length < parent) {
  //       return context.createError({
  //         message: t("ERRORS.phoneLength", { value: parent }),
  //         path: "phoneNumber",
  //       });
  //     } else {
  //       return true;
  //     }
  //   }),
  // role: yup
  //   .string()
  //   .required(t("ERRORS.isRequired", { name: t("LABELS.Role") })),
  // image: yup
  //   .mixed()
  //   .test(
  //     "image",
  //     t("ERRORS.isRequired", { name: t("LABELS.image") }),
  //     (value) => {
  //       if (value || initialValues.preview) {
  //         return true;
  //       }
  //     }
  //   ),
  // password: yup.string().test("password", t("ERRORS.password"), (value) => {
  //   if (route.params.id) {
  //     return true;
  //   } else if (value) {
  //     return true;
  //   } else return false;
  // }),
  // cPassword: yup.string().when("password", (password, field) =>
  //   password
  //     ? field
  //         .test("cPassword", t("ERRORS.confirmPassword"), (value) => {
  //           if (route.params.id) {
  //             return true;
  //           } else if (value) {
  //             return true;
  //           } else return false;
  //         })
  //         .oneOf([yup.ref("password")], t("ERRORS.notEqualPasswords"))
  //     : field
  // ),
});
const btnLoading = ref(false);

function handleSubmit(values, actions) {
  btnLoading.value = true;
  const frmData = new FormData();

  let url = "users";
  console.log(route.params);
  console.log(values);
  if (route.params.id) {
    frmData.append("_method", "PUT");
    url = `users/${values.id}/upgrade`;
  }

  // frmData.append("card_front[media]", initialValues.image);

  // frmData.append("card_back[media]", initialValues.image2);
  // if (initialValues.image) {
  //   frmData.append("image", initialValues.image);
  // }
  // frmData.append("full_name", values.name);
  // frmData.append("email", values.email);
  frmData.append("full_name", values.storeName);
  frmData.append("region", values.storeRegion);
  frmData.append("city_id", values.storeCity);
  frmData.append("store_activity_id", values.storeActivity);
  frmData.append("location_in_map", values.storeLocation);
  frmData.append("store_image", values.storeImage);
  frmData.append(
    "commercial_register_image",
    values.storeCommericalRegisterImage
  );
  // frmData.append("phone", values.phoneNumber);
  // frmData.append("phone_code", values.phoneCode);
  // if (values.password) frmData.append("password", values.password);
  // if (values.password)
  //   frmData.append("password_confirmation", values.cPassword);
  // console.log(route.params);

  axios
    .post(url, frmData)
    .then((res) => {
      setTimeout(() => toast.success(res.data.message), 300);
      router.push("/users");
      btnLoading.value = false;
      actions.resetForm();
    })
    .catch((e) => {
      toast.error(e.response.data.message);
    })
    .finally(() => (btnLoading.value = false));
}

const loading = ref(false);

const breads = [
  {
    path: "/",
    imgIcon: "menu-setup.svg",
    name: t("TITLES.home"),
  },
  {
    name: t("LABELS.users"),
    path: "/users",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.User"),
    }),
    path: `/users/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

//   function getData() {
//     console.log(route.params);
//     axios.get(`users/${route.params.id}`).then((res) => {
//       const result = res.data.data;

//       initialValues.name = result.full_name;
//       initialValues.email = result.email;
//       initialValues.storeName = result.store.full_name;
//       initialValues.storeRegion = result.store.region;
//       initialValues.storeCity = result.store.id;
//       initialValues.image = result.image;
//       initialValues.preview = result.image;
//       initialValues.storeActivity = result.store.store_activity;
//       initialValues.storeLocation = result.store.location_in_map;
//       initialValues.storeImage = result.store.store_image;
//       initialValues.storeCommericalRegisterImage =
//         result.store.commercial_register_image;

//       initialValues.phoneNumber = result.phone;
//       initialValues.phoneCode = result.phone_code;

//       initialValues.id = result.id;

//       loading.value = false;
//     });
//   }
const categories = ref([]);
const cities = ref([]);
function getCategories() {
  axios.get("store-activities").then((res) => {
    categories.value = res.data.data.map((el) => {
      return {
        id: el.id,
        name: el.name,
      };
    });
  });
}
function getCities() {
  axios.get("cities_without_pagination").then((res) => {
    cities.value = res.data.data.map((el) => {
      return {
        id: el.id,
        name: el.name,
      };
    });
  });
}
getCities();
getCategories();

onBeforeMount(() => {
  if (route.params.id) {
    loading.value = true;
    getData();
  }
});
</script>

<style lang="scss">
.v-table {
  @apply flex h-full w-full max-w-[100%] flex-col rounded-lg leading-[1.5];
  --v-table-header-height: 45px;

  .table-wrapper {
    @apply flex-auto overflow-auto;
    table {
      @apply w-full border-spacing-0;
      thead {
        @apply rounded-2xl bg-[#F8FBFD];
      }

      th {
        @apply px-4 text-start;
        height: calc(var(--v-table-header-height) + 15px);
      }

      td {
        @apply p-4 text-start;
      }
      tr.error {
        label,
        input {
          @apply text-error;
        }
      }
    }
  }
}
.profile_page {
  .upload {
    padding: 0;
    width: 180px;
    height: 180px;
    border-radius: 50% !important;
    overflow: hidden;
    label {
      padding: 0;
      margin: 0;
      width: 180px;
      height: 180px;
    }
    img {
      // width: 100% !important;
      // height: 100% !important;
      object-fit: cover !important;
      border-radius: 50% !important;
    }
  }
}
</style>
