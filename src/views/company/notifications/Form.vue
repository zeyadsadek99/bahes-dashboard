<template>
  <div>
    <breadcrumbs back="/notifications" :title="$t('LABELS.notifications')" :items="breads" />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.notifications') })"
          >
            <VeeForm
              :validation-schema="schema"
              @submit="handleSubmit"
              :initial-values="initialValues"
              class="profile_page"
            >
             
              <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
                <!-- <base-input
                  id="name"
                  name="name"
                  :placeholder="$t('LABELS.name')"
                  :label="$t('LABELS.name')"
                  type="text"
                /> -->
                <base-input
                  id="title"
                  name="title"
                  :placeholder="$t('LABELS.title')"
                  :label="$t('LABELS.title')"
                  type="text"
                />
                <base-input
                  id="body"
                  name="body"
                  :placeholder="$t('LABELS.body')"
                  :label="$t('LABELS.body')"
                  type="text"
                />
                <base-select
                  id="type"
                  name="type"
                  :placeholder="$t('LABELS.type')"
                  :label="$t('LABELS.type')"
                  :options="types"
                  v-model:itemValue="initialValues.type"

                />

                
              
              </div>

              

              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                <router-link
                  to="/notifications"
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
  title: "",
  body: "",
  type: "",
  
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
  // // role: yup
  // //   .string()
  // //   .required(t("ERRORS.isRequired", { name: t("LABELS.Role") })),
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

  let url = "notifications";

  if (route.params.id) {
    frmData.append("_method", "PUT");
    url = `notifications/${values.id}`;
  }

  // if (initialValues.image) {
  //   frmData.append("image", initialValues.image);
  // }

  frmData.append("title", values.title);
  frmData.append("body", values.body);
  frmData.append("type", values.type);
  // frmData.append("phone", values.phoneNumber);
  // frmData.append("phone_code", values.phoneCode);
  // frmData.append("email", values.email);
  // if (values.password) frmData.append("password", values.password);

  axios
    .post(url, frmData)
    .then((res) => {
      setTimeout(() => toast.success(res.data.message), 300);
      router.push("/notifications");
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
    name: t("LABELS.notifications"),
    path: "/notifications",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.notifications"),
    }),
    path: `/notifications/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

function getData() {
  axios.get(`notifications/${route.params.id}`).then((res) => {
    const result = res.data.data;

    // initialValues.nameAr = result.name;
  //  initialValues.nameEn = result.name;

    initialValues.id = result.id;

    loading.value = false;
  });
}
const types = ref([
{ id: 'all', name: 'All' },
  { id: 'admin', name: 'Admin' },
  { id: 'supper_admin', name: 'Super Admin' },
  { id: 'specific', name: 'Specific' }
]);

function getCategories() {
  axios.get("product-types").then((res) => {
    categories.value = res.data.data.map((el) => {
      return {
        id: el.id,
        name: el.name,
      };
    });
  });
}
// getCategories();
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
