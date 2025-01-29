<template>
  <div class="h-full flex flex-col">
    <breadcrumbs :items="breadItems" :title="$t('LABELS.users')" />
    <div
      class="bg-white rounded-3xl h-full shadow-[0_7px_6px_0px,rgba(#B1B1B11A)] md:p-7 flex-1 flex flex-col"
    >
      <base-filter
        name="users"
        :inputs="inputs"
        :btn-name="t(`BUTTONS.add`, { name: t('LABELS.users') })"
        icon="fas fa-plus"
        :keyword="true"
        :hide-label="true"
        @action="$router.push('/users/form')"
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
                  name: $t("LABELS.users"),
                })
              }}
            </h3>
            <div class="flex items-center justify-center mt-7 gap-2 flex-wrap">
              <router-link
                to="/users/form"
                class="base-btn rounded-xl self-end"
              >
                <i class="fas fa-plus"></i>
                {{ $t(`BUTTONS.add`, { name: $t("LABELS.users") }) }}
              </router-link>
            </div>
          </div>
          <h3 v-else class="mt-4 font-semibold text-text text-center">
            {{ $t("TITLES.No Search result") }}
          </h3>
        </template>

        <template v-slot:[`item.name`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card :title="`${item.full_name}`" />
          </div>
        </template>

        <template v-slot:[`item.phone`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card :title="`${item.phone}`" />
          </div>
        </template>
        <template v-slot:[`item.phone_code`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card :title="`${item.phone_code}`" />
          </div>
        </template>

        <template v-slot:[`item.is_admin_active_user`]="{ item }">
          <global-switcher
            :id="item.id"
            :url="`users/${item.id}/toggle-active-user`"
            v-model:modalValue="item.is_admin_active_user"
            method='PATCH'

          />
        </template>
        <!-- <template v-slot:[`item.is_ban`]="{ item }">
          <global-switcher
            :id="item.id"
            :url="`users/block/${item.id}`"
            v-model:modalValue="item.is_ban"
            method='POST'

          />
        </template> -->
        <template v-slot:[`item.actions`]="{ item, index }">
          <div class="flex items-center gap-4">
            <router-link :to="`/users/form/${item.id}`">
              <svg-icon class="text-primary" name="edit" filled />
            </router-link>
            <router-link :to="`/users/form/${item.id}`">
              <svg-icon class="text-primary" name="trending-up" filled />
            </router-link>
            <Deleter
              :url="`users/${item.id}`"
              :id="item.id"
              method="DELETE"
              @remove="items.splice(index, 1)"
            />
          </div>
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
const inputs = [
  // {
  //   name: "strat_date",
  //   placeholder: "from_date",
  //   type: "date",
  //   icon: "calendar",
  //   filter: null,
  //   multiple: false,
  //   minDate: "",
  // },
  // {
  //   name: "end_date",
  //   placeholder: "to_date",
  //   type: "date",
  //   icon: "calendar",
  //   filter: null,
  //   multiple: false,
  //   minDate: "",
  // },
  {
    name: "user_type",
    placeholder: "user_type",
    type: "select",
    // icon: "calendar",
    filter: null,
    options : [
    { id: "", name: t("STATUS.all")},
    { id: 'client', name: 'Client' },
    { id: 'vendor', name: 'Vendor' },
  ],

    multiple: false,
  },
  {
    name: "acceptance",
    placeholder: "acceptance",
    type: "select",
    // icon: "calendar",
    filter: null,
    options : [
    { id: "", name: t("STATUS.all")},
    { id: 'without', name: 'Without' },
    { id: 'with', name: 'With' },
  ],

    multiple: false,
  },
];
const breadItems = [
  {
    name: t("TITLES.home"),
    path: "/",
    imgIcon: "settings.svg",
  },
  {
    name: t("LABELS.users"),
    path: "/users",
    imgIcon: "",
  },
];

const items = ref([]);
const loading = ref(false);
const paginator = ref(null);

const headers = [
  {
    title: t("LABELS.Name", { name: t("LABELS.users") }),
    align: "start",
    sortable: false,
    key: "name",
  },
  {
    title: t("LABELS.phone"),
    align: "start",
    sortable: false,
    key: "phone",
  },
  {
    title: t("LABELS.phone_code"),
    align: "start",
    sortable: false,
    key: "phone_code",
  },
  {
    title: t("LABELS.activation"),
    align: "start",
    sortable: false,
    key: "is_admin_active_user",
  },
  // {
  //   title: t("LABELS.block"),
  //   align: "start",
  //   sortable: false,
  //   key: "is_ban",
  // },

  {
    title: t("LABELS.Actions"),
    align: "start",
    sortable: false,
    key: "actions",
  },
];

function fetchData() {
  const params = new URLSearchParams();
  // params.append("from", route.query.strat_date || "");
  // params.append("to", route.query.end_date || "");
  // params.append("filter", route.query.filter || "");
  params.append("acceptance", route.query.acceptance || "");
  
  params.append("user_type", route.query.user_type || "");
  params.append("keyword", route.query.keyword || "");
  params.append("page", +route.query.page || 1);
  loading.value = true;
  axios
    .get("users", {
      params,
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
