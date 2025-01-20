<template>
  <div>
    <breadcrumbs back="/contacts" :title="$t('LABELS.contacts')" :items="breads" />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.contacts') })"
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
                <base-input
                  v-model="initialValues.name"
                  :disabled="true"

                  :placeholder="$t('LABELS.name')"
                  :label="$t('LABELS.name')"
                  name="name"
                  type="text"
                />
                <base-input
                  v-model="initialValues.content"
                  :disabled="true"
                  :placeholder="$t('LABELS.content')"
                  :label="$t('LABELS.content')"
                  name="content"
                  type="text"
                />
                <!-- <base-input
                  v-model="initialValues.reply"
                  :placeholder="$t('LABELS.reply')"
                  :label="$t('LABELS.reply')"
                  name="reply"
                  type="text"
                /> -->
                
                
                

                
                
              </div>
              <!-- Replies Section -->
              <div class="mt-6">
                <h3 class="font-semibold text-lg">
                  {{ $t('LABELS.replies') }}
                </h3>
                <div
                  v-if="initialValues.replies.length"
                  class="mt-4 space-y-4 bg-gray-50 p-4 rounded-md"
                >
                  <div
                    v-for="(reply, index) in initialValues.replies"
                    :key="index"
                    class="p-4 bg-white shadow-sm rounded-md"
                  >
                    <p class="text-gray-700 font-medium">
                      {{ reply.author || $t('LABELS.anonymous') }}
                    </p>
                    <p class="text-gray-600 mt-1">
                      {{ reply.content }}
                    </p>
                  </div>
                </div>
                <p v-else class="text-gray-500 mt-4">
                  {{ $t('TITLES.noRepliesYet') }}
                </p>
              </div>

              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                
                <router-link
                  to="/contacts"
                  class="capitalize base-btn font-semibold "
                >
                  {{ $t("BUTTONS.Ok") }}
                </router-link>
                <!-- <button class=" disabled" :disabled="btnLoading"  type="submit">
                  {{ $t("BUTTONS.Ok") }}
                </button> -->
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
  content: '',
  name: '',
  replies:[],
  
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
// const btnLoading = ref(false);

// function handleSubmit(values, actions) {
//   btnLoading.value = true;
//   const frmData = new FormData();

//   let url = "contacts";

//   // if (route.params.id) {
//   //   // frmData.append("_method", "PUT");
//   //   url = `contacts`;
//   // }

  
//   // frmData.append("reply", values.reply);
//   // frmData.append("email", values.email);
//   // frmData.append("facebook", values.facebook);
//   // frmData.append("twitter", values.twitter);
//   // frmData.append("instagram", values.instagram);
//   // frmData.append("tiktok", values.tiktok);
//   // frmData.append("snapchat", values.snapchat);
//   // frmData.append("app_store", values.app_store);
//   // frmData.append("google_play", values.google_play);
//   // frmData.append("whatsapp", values.whatsapp);
// //   frmData.append("website_name", values.website_name);
// //   frmData.append("vat", values.vat);
// //   frmData.append("vendor_percentage", values.vendor_percentage);
// //   values.phones.forEach((phone, index) => {
// //   frmData.append(`phones[${index}][phone]`, phone.phone);
// //   frmData.append(`phones[${index}][phone_code]`, phone.phone_code);
// // });

  

//   axios
//     .post(url, frmData)
//     .then((res) => {
//       setTimeout(() => toast.success(res.data.message), 300);
//       router.push("/contacts");
//       btnLoading.value = false;
//       actions.resetForm();
//     })
//     .catch((e) => {
//       toast.error(e.response.data.message);
//     })
//     .finally(() => (btnLoading.value = false));
// }

const loading = ref(false);

const breads = [
  {
    path: "/",
    imgIcon: "menu-setup.svg",
    name: t("TITLES.home"),
  },
  {
    name: t("LABELS.contacts"),
    path: "/contacts",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.contacts"),
    }),
    path: `/contacts/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

function getData() {
  console.log(route.params);
  axios.get(`contacts/${route.params.id}`).then((res) => {
    const result = res.data.data;

    initialValues.name = result.full_name;
    initialValues.content = result.content;
    initialValues.replies = result.replies;
    initialValues.id = result.id;

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
.replies-section {
  .reply {
    background-color: #ffffff;
    padding: 1rem;
    border-radius: 0.5rem;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  }

  .reply-author {
    font-weight: 600;
    color: #333;
  }

  .reply-content {
    margin-top: 0.5rem;
    color: #555;
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
