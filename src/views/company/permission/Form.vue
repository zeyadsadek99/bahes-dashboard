<template>
  <div>
    <breadcrumbs back="/permission" :title="$t('LABELS.permission')" :items="breads" />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.permission') })"
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
                  id="titleAr"
                  name="titleAr"
                  :placeholder="$t('LABELS.titleAr')"
                  :label="$t('LABELS.titleAr')"
                  type="text"
                />
                <base-input
                  id="titleEn"
                  name="titleEn"
                  :placeholder="$t('LABELS.titleEn')"
                  :label="$t('LABELS.titleEn')"
                  type="text"
                />

                <base-input
                  id="front_route_name"
                  name="front_route_name"
                  :placeholder="$t('LABELS.front_route_name')"
                  :label="$t('LABELS.front_route_name')"
                  type="text"
                />
                <base-input
                  id="back_route_name"
                  name="back_route_name"
                  :placeholder="$t('LABELS.back_route_name')"
                  :label="$t('LABELS.back_route_name')"
                  type="text"
                />
                <base-input
                  id="icon"
                  name="icon"
                  :placeholder="$t('LABELS.icon')"
                  :label="$t('LABELS.icon')"
                  type="text"
                />
                <!-- <base-input
                  id="body"
                  name="body"
                  :placeholder="$t('LABELS.body')"
                  :label="$t('LABELS.body')"
                  type="text"
                /> -->
                <!-- <base-select
                  id="type"
                  name="type"
                  :placeholder="$t('LABELS.type')"
                  :label="$t('LABELS.type')"
                  :options="types"
                  v-model:itemValue="initialValues.type"

                /> -->

                
              
              </div>

              

              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                <router-link
                  to="/permission"
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
  titleAr: "",
  titleEn: "",
  front_route_name: "",
  back_route_name: "",

  icon: "",
  
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

  let url = "update-all-permissions";

  if (route.params.id) {
    frmData.append("_method", "PUT");
    url = `permission/${values.id}`;
  }

  // if (initialValues.image) {
  //   frmData.append("image", initialValues.image);
  // }


  frmData.append("ar[title]", values.titleAr);
  frmData.append("en[title]", values.titleEn);
  frmData.append("front_route_name", values.front_route_name);
  frmData.append("back_route_name", values.back_route_name);
  frmData.append("icon", values.icon);

 
  // frmData.append("phone", values.phoneNumber);
  // frmData.append("phone_code", values.phoneCode);
  // frmData.append("email", values.email);
  // if (values.password) frmData.append("password", values.password);

  axios
    .post(url, frmData)
    .then((res) => {
      setTimeout(() => toast.success(res.data.message), 300);
      router.push("/permission");
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
    name: t("LABELS.permission"),
    path: "/permission",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.permission"),
    }),
    path: `/permission/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

function getData() {
  axios.get(`permission/${route.params.id}`).then((res) => {
    const result = res.data.data;

    // initialValues.nameAr = result.name;
  //  initialValues.nameEn = result.name;

    initialValues.id = result.id;
    initialValues.back_route_name = result.back_route_name;
    initialValues.front_route_name = result.front_route_name;
    initialValues.icon = result.icon;
    initialValues.titleAr = result.ar.title;
    initialValues.titleEn = result.en.title;
    loading.value = false;
  });
}
// const types = ref([
// { id: 'all', name: 'All' },
//   { id: 'admin', name: 'Admin' },
//   { id: 'supper_admin', name: 'Super Admin' },
//   { id: 'specific', name: 'Specific' }
// ]);

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
