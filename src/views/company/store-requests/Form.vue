<template>
  <div>
    <breadcrumbs back="/store-requests" :title="$t('LABELS.store-requests')" :items="breads" />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.store-requests') })"
          >
            <VeeForm
              :validation-schema="schema"
              @submit="handleSubmit"
              :initial-values="initialValues"
              class="profile_page"
            >
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
              <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
                <!-- <base-input
                  v-model="initialValues.website_name"
                  :placeholder="$t('LABELS.websiteName')"
                  :label="$t('LABELS.websiteName')"
                  name="website_name"
                  type="text"
                />
                <base-input
                  v-model="initialValues.email"
                  :placeholder="$t('LABELS.email')"
                  :label="$t('LABELS.email')"
                  name="email"
                  type="email"
                /> -->
                <base-input
                  v-model="initialValues.tax_rate"
                  :placeholder="$t('LABELS.tax_rate')"
                  :label="$t('LABELS.tax_rate')"
                  name="tax_rate"
                  type="text"
                />
                <base-input
                  v-model="initialValues.rate_for_application_from_merchants"
                  :placeholder="$t('LABELS.rate_for_application_from_merchants')"
                  :label="$t('LABELS.rate_for_application_from_merchants')"
                  name="rate_for_application_from_merchants"
                  type="text"
                />
                <base-input
                  v-model="initialValues.rate_for_workshop_customers"
                  :placeholder="$t('LABELS.rate_for_workshop_customers')"
                  :label="$t('LABELS.rate_for_workshop_customers')"
                  name="rate_for_workshop_customers"
                  type="text"
                />
                <base-input
                  v-model="initialValues.permitted_period_for_accepting_request"
                  :placeholder="$t('LABELS.permitted_period_for_accepting_request')"
                  :label="$t('LABELS.permitted_period_for_accepting_request')"
                  name="permitted_period_for_accepting_request"
                  type="text"
                />
                <base-input
                  v-model="initialValues.maximum_number_of_pending_requests"
                  :placeholder="$t('LABELS.maximum_number_of_pending_requests')"
                  :label="$t('LABELS.maximum_number_of_pending_requests')"
                  name="maximum_number_of_pending_requests"
                  type="text"
                />
                <!-- <base-input
                  v-model="initialValues.tiktok"
                  :placeholder="$t('LABELS.tiktok')"
                  :label="$t('LABELS.tiktok')"
                  name="tiktok"
                  type="text"
                />
                <base-input
                  v-model="initialValues.snapchat"
                  :placeholder="$t('LABELS.snapchat')"
                  :label="$t('LABELS.snapchat')"
                  name="snapchat"
                  type="text"
                />
                <base-input
                  v-model="initialValues.app_store"
                  :placeholder="$t('LABELS.appStore')"
                  :label="$t('LABELS.appStore')"
                  name="app_store"
                  type="text"
                />
                <base-input
                  v-model="initialValues.google_play"
                  :placeholder="$t('LABELS.googlePlay')"
                  :label="$t('LABELS.googlePlay')"
                  name="google_play"
                  type="text"
                /> -->
                <!-- <base-input
                  v-model="initialValues.whatsapp"
                  :placeholder="$t('LABELS.whatsapp')"
                  :label="$t('LABELS.whatsapp')"
                  name="whatsapp"
                  type="text"
                /> -->
                <!-- <base-input
                  v-model="initialValues.vat"
                  :placeholder="$t('LABELS.vat')"
                  :label="$t('LABELS.vat')"
                  name="vat"
                  type="text"
                />
                <base-input
                  v-model="initialValues.vendor_percentage"
                  :placeholder="$t('LABELS.vendorPercentage')"
                  :label="$t('LABELS.vendorPercentage')"
                  name="vendor_percentage"
                  type="text"
                /> -->
                
                <!-- Phones Inputs -->
                <!-- <div v-for="(phone, index) in initialValues.phones" :key="index" class="flex gap-2">
                  <base-input
                    v-model="phone.phone"
                    :placeholder="$t('LABELS.phone')"
                    :label="$t('LABELS.phone')"
                    :name="`phones[${index}][phone]`"
                    type="text"
                  />
                  <base-input
                    v-model="phone.phone_code"
                    :placeholder="$t('LABELS.phoneCode')"
                    :label="$t('LABELS.phoneCode')"
                    :name="`phones[${index}][phone_code]`"
                    type="text"
                  />
                </div> -->

                
                
              </div>


              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                <router-link
                  to="/store-requests"
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
  tax_rate: '',
  rate_for_application_from_merchants: '',
  rate_for_workshop_customers: '',
  permitted_period_for_accepting_request: '',
  maximum_number_of_pending_requests: '',
  permitted_period_for_completing_reques: '',


  
});

const schema = yup.object().shape({
  // questionAr: yup.string().required(t("ERRORS.name")),
  // questionEn: yup.string().required(t("ERRORS.name")),
  // answerAr: yup.string().required(t("ERRORS.name")),
  // answerEn: yup.string().required(t("ERRORS.name")),
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

  let url = "store-requests";

  if (route.params.id) {
    frmData.append("_method", "PUT");
    url = `store-requests/${route.params.id}`;
  }

  // if (initialValues.image) {
  //   frmData.append("image", initialValues.image);
  // }
  console.log(values);
  frmData.append("tax_rate", values.tax_rate);
  frmData.append("rate_for_application_from_merchants", values.rate_for_application_from_merchants);
  frmData.append("rate_for_workshop_customers", values.rate_for_workshop_customers);
  frmData.append("permitted_period_for_accepting_request", values.permitted_period_for_accepting_request);
  frmData.append("maximum_number_of_pending_requests", values.maximum_number_of_pending_requests);

  frmData.append("permitted_period_for_completing_reques", values.permitted_period_for_completing_reques);  

// });

  

  axios
    .post(url, frmData)
    .then((res) => {
      setTimeout(() => toast.success(res.data.message), 300);
      router.push("/store-requests");
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
    name: t("LABELS.store-requests"),
    path: "/store-requests",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.store-requests"),
    }),
    path: `/store-requests/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

function getData() {
  axios.get(`store-requests`).then((res) => {
  const result = res.data.data;
    console.log(result)
  // Iterate over the data and set the initialValues based on the key
  result.forEach(item => {
    switch(item.key) {
      // case 'number_of_cancel_orders':
      //   initialValues.number_of_cancel_orders = item.value;
      //   break;
      case 'tax_rate':
        initialValues.tax_rate = item.value;
        break;
      
      case 'rate_for_application_from_merchants':
        initialValues.rate_for_application_from_merchants = item.value;
        break;
      case 'rate_for_workshop_customers':
        initialValues.rate_for_workshop_customers = item.value;
        break;
      case 'permitted_period_for_accepting_request':
        initialValues.permitted_period_for_accepting_request= item.value;
        break;
      case 'maximum_number_of_pending_requests':
        initialValues.maximum_number_of_pending_requests= item.value;
        break;
      case 'permitted_period_for_completing_reques':
        initialValues.permitted_period_for_completing_reques= item.value;
        break;

      // case 'tiktok':
      //   initialValues.tiktok = item.value;
      //   break;
      // case 'snapchat':
      //   initialValues.snapchat = item.value;
      //   break;
      // case 'app_store':
      //   initialValues.app_store = item.value;
      //   break;
      // case 'google_play':
      //   initialValues.google_play = item.value;
      //   break;
   
      // case 'website_name':
      //   initialValues.website_name = item.value;
      //   break;
      // case 'vat':
      //   initialValues.vat = item.value;
      //   break;
      // case 'vendor_percentage':
      //   initialValues.vendor_percentage = item.value;
      //   break;
      // case 'phones':
      //   initialValues.phones = item.value;  // For array (phone data)
      //   break;
      default:
        break;
    }
  });
  console.log(initialValues)

  // After assigning all values, you can stop loading
  loading.value = false;
});
}

onBeforeMount(() => {
  
    loading.value = true;
    getData();
  
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
