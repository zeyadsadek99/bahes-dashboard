<template>
  <div>
    <breadcrumbs back="/product" :title="$t('LABELS.product')" :items="breads" />
    <div class="flex gap-4 flex-wrap">
      <div class="flex-1 w-full min-w-[250px]">
        <FormSkelton v-if="loading" />
        <template v-else>
          <base-card1
            :title="$t('TITLES.Details', { name: $t('LABELS.product') })"
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
                  id="desAr"
                  name="desAr"
                  :placeholder="$t('LABELS.desAr')"
                  :label="$t('LABELS.desAr')"
                  type="text" 
                />
                <base-input
                  id="desEn"
                  name="desEn"
                  :placeholder="$t('LABELS.desEn')"
                  :label="$t('LABELS.desEn')"
                  type="text"
                />
                <base-input
                  id="code"
                  name="code"
                  :placeholder="$t('LABELS.code')"
                  :label="$t('LABELS.code')"
                  type="text"
                />
                <base-select
                  id="product_id"
                  name="product_id"
                  :placeholder="$t('LABELS.product_id')"
                  :label="$t('LABELS.product_id')"
                  :options="categories"
                  v-model:itemValue="initialValues.product_id"
                />
                
              <!-- {{ categories }} -->
              </div>

              

              <div
                class="flex items-center justify-end mt-7 gap-4 md:col-span-2 xl:col-span-3"
              >
                <router-link
                  to="/product"
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
  nameAr: "",
  nameEn: "",
  desAr: "",
  desEn: "",
  code: "",
  product_id: "",
  
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

  let url = "products";

  if (route.params.id) {
    frmData.append("_method", "PUT");
    url = `products/${values.id}`;
  }

  // if (initialValues.image) {
  //   frmData.append("image", initialValues.image);
  // }

  frmData.append("en[name]", values.nameEn);
  frmData.append("ar[name]", values.nameAr);
  frmData.append("en[desc]", values.desEn);
  frmData.append("ar[desc]", values.desAr);
  frmData.append("code", values.code);
  frmData.append("product_type_id", values.product_id);
  // frmData.append("phone", values.phoneNumber);
  // frmData.append("phone_code", values.phoneCode);
  // frmData.append("email", values.email);
  // if (values.password) frmData.append("password", values.password);

  axios
    .post(url, frmData)
    .then((res) => {
      setTimeout(() => toast.success(res.data.message), 300);
      router.push("/product");
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
    name: t("LABELS.product"),
    path: "/product",
    imgIcon: "",
  },
  {
    name: t(`BUTTONS.${route.params.id ? "Edit" : "add"}`, {
      name: t("LABELS.product"),
    }),
    path: `/product/form${route.params.id ? "/" + route.params.id : ""}`,
  },
];

function getData() {
  axios.get(`products/${route.params.id}`).then((res) => {
    const result = res.data.data;
    initialValues.code = result.code;
    initialValues.product_id = result.product_type;
    // initialValues.nameAr = result.name;
  //  initialValues.nameEn = result.name;

    initialValues.id = result.id;

    loading.value = false;
  });
}
const categories = ref([]);

function getCategories() {
  axios.get("product-types").then((res) => {
    categories.value = res.data.data.map((el) => {
      return {
        id: el.name,
        name: el.name,
      };
    });
  });
}
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
