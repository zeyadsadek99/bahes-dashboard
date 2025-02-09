<template>
  <v-navigation-drawer
    v-model="appStore.sidebar"
    :rail="rail"
    permanent
    width="256"
    density="compact"
  >
    <!-- this is comment -->
    <button
      v-if="appStore.sidebar && smallScreen"
      type="button"
      @click="appStore.sidebar = !appStore.sidebar"
      class="me-auto px-2 text-2xl text-primary"
    >
      <i class="fa-solid fa-times"></i>
    </button>
    <v-list-item
      class="top_header hover:bg-transparent !justify-evenly"
      as="div"
      nav
    >
      <router-link to="/">
        <svg-icon path="logo" name="logo" />
      </router-link>
    </v-list-item>

    <v-list density="compact" nav :class="{ railed: rail }">
      <template v-for="(main, mainIndex) in sidebar" :key="mainIndex">
        <template v-if="main">
          <p class="text-sm my-2 text-sub px-4" v-if="main.label">
            <span>
              {{ $t(main.label) }}
            </span>
          </p>
          <v-list-item
            :class="{
              active:
                $route.path.includes(main.active) ||
                ($route.path == '/' && main.active == 'home-page'),
            }"
            v-if="main.type == 'general'"
            :to="`/${main.path}`"
            exact
            class="flex items-center"
          >
            <template v-slot:prepend>
              <svg-icon
                @click="rail = false"
                :class="!rail ? 'me-4' : ''"
                path="sidebar"
                class="text-xl"
                :name="main.icon"
              />
            </template>
            {{ $t(main.title) }}

            <p
              v-if="main.path === 'support-tickets' && appStore.unreadMessages"
              class="bg-error size-4 rounded-full text-[10px] text-white flex items-center justify-center"
            >
              {{ appStore.unreadMessages }}
            </p>
          </v-list-item>
        </template>
      </template>
    </v-list>
  </v-navigation-drawer>
</template>

<script setup>
import axios from "axios";
import {
  checkRootPermission,
  settings,
  checkPermission,
} from "@/utils/permissions.ts";
import { useAppStore } from "@/store/app";
import { onMounted, ref, watch } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const appStore = useAppStore();

const unreadMessages = ref(0);
const sidebar = ref([
  // Dashboard
  {
    label: "",
    title: "TITLES.home",
    path: "",
    icon: "dashboard",
    type: "general",
    active: "home-page",
  },

  // Content Management
  {
    label: "",
    title: "LABELS.Sliders",
    path: "sliders",
    icon: "sliders",
    type: "general",
    active: "sliders",
    permission: checkRootPermission(["sliders"]),
  },
  {
    label: "",
    title: "LABELS.page",
    path: "page",
    icon: "pages",
    type: "general",
    active: "page",
    permission: checkRootPermission(["page"]),
  },

  // User & Role Management
  {
    label: "",
    title: "LABELS.Admins",
    path: "admins",
    icon: "user-shield",
    type: "general",
    active: "admins",
    permission: checkRootPermission(["admins"]),
  },
  {
    label: "",
    title: "LABELS.Brands",
    path: "brands",
    icon: "brand-settings",
    type: "general",
    active: "brands",
    permission: checkRootPermission(["brands"]),
  },
  {
    label: "",
    title: "LABELS.models",
    path: "models",
    icon: "person-circle",
    type: "general",
    active: "models",
    permission: checkRootPermission(["models"]),
  },
  // {
  //   label: "",
  //   title: "LABELS.store-requests",
  //   path: "store-requests",
  //   icon: "person-circle",
  //   type: "general",
  //   active: "store-requests",
  //   permission: checkRootPermission(["store-requests"]),
  // },
  {
    label: "",
    title: "LABELS.Product",
    path: "product",
    icon: "box",
    type: "general",
    active: "/product",
    permission: checkRootPermission(["product"]),
  },
  {
    label: "",
    title: "LABELS.role",
    path: "role",
    icon: "settings",
    type: "general",
    active: "/role",
    permission: checkRootPermission(["role"]),
  },
  {
    label: "",
    title: "LABELS.settlement-requests",
    path: "settlement-requests",
    icon: "bank",
    type: "general",
    active: "/settlement-requests",
    permission: checkRootPermission(["settlement-requests"]),
  },
  {
    label: "",
    title: "LABELS.permission",
    path: "permission",
    icon: "lock",
    type: "general",
    active: "/permission",
    permission: checkRootPermission(["permission"]),
  },
  {
    label: "",
    title: "LABELS.store-activities",
    path: "store-activities",
    icon: "bar-chart",
    type: "general",
    active: "store-activities",
    permission: checkRootPermission(["store-activities"]),
  },
  {
    label: "",
    title: "LABELS.type-product",
    path: "type-product",
    icon: "price-tag",
    type: "general",
    active: "/type-product",
    permission: checkRootPermission(["product-types"]),
  },
  {
    label: "",
    title: "LABELS.notifications",
    path: "notifications",
    icon: "notification-2",
    type: "general",
    active: "/notifications", 
    permission: checkRootPermission(["notifications"]),
  },
  {
    label: "",
    title: "LABELS.review",
    path: "review",
    icon: "star",
    type: "general",
    active: "/review", 
    permission: checkRootPermission(["review"]),
  },
  {
    label: "",
    title: "LABELS.profile",
    path: "profile",
    icon: "person-circle",
    type: "general",
    active: "profile",
    permission: checkRootPermission(["profile"]),
  },
  
  // {
  //   label: "",
  //   title: "LABELS.clients",
  //   path: "clients",
  //   icon: "users",
  //   type: "general",
  //   active: "clients",
  //   permission: checkRootPermission(["clients"]),
  // },
  {
    label: "",
    title: "LABELS.users",
    path: "users",
    icon: "user",
    type: "general",
    active: "users",
    permission: checkRootPermission(["users"]),
  },
  {
    label: "",
    title: "LABELS.bank-accounts",
    path: "bank-accounts",
    icon: "bank-card",
    type: "general",
    active: "bank-accounts",
    permission: checkRootPermission(["bank-accounts"]),
  },
  // {
  //   label: "",
  //   title: "LABELS.vendors",
  //   path: "vendors",
  //   icon: "users",
  //   type: "general",
  //   active: "vendors",
  //   permission: checkRootPermission(["vendors"]),
  // },

  // Orders & Transactions
  {
    label: "",
    title: "LABELS.orders",
    path: "orders",
    icon: "box",
    type: "general",
    active: "orders",
    permission: checkRootPermission(["orders"]),
  },
  
  // {
  //   label: "",
  //   title: "LABELS.cancel-order",
  //   path: "cancel-order",
  //   icon: "close-circle",
  //   type: "general",
  //   active: "cancel-order",
  //   permission: checkRootPermission(["cancel-order"]),
  // },

  // Settings & Configuration
  {
    label: "",
    title: "LABELS.settings",
    path: "settings",
    icon: "settings",
    type: "general",
    active: "settings",
    permission: checkRootPermission(["settings"]),
  },
  // {
  //   label: "",
  //   title: "LABELS.app-ratio",
  //   path: "app-ratio",
  //   icon: "settings",
  //   type: "general",
  //   active: "app-ratio",
  //   permission: checkRootPermission(["app-ratio"]),
  // },
  {
    label: "",
    title: "LABELS.contacts",
    path: "contacts",
    icon: "phone",
    type: "general",
    active: "contacts",
    permission: checkRootPermission(["contacts"]),
  },
  {
    label: "",
    title: "LABELS.chat",
    path: "chat",
    icon: "phone",
    type: "general",
    active: "chat",
    // permission: checkRootPermission(["chat"]),
  },
  {
    label: "",
    title: "LABELS.Countries",
    path: "countries",
    icon: "globe",
    type: "general",
    active: "countries",
    permission: checkRootPermission(["countries"]),
  },
  {
    label: "",
    title: "LABELS.cities",
    path: "cities",
    icon: "city",
    type: "general",
    active: "cities",
    permission: checkRootPermission(["cities"]),
  },

  // Reports & Analytics
  {
    label: "",
    title: "LABELS.statistics",
    path: "statistics",
    icon: "line-chart",
    type: "general",
    active: "statistics",
    permission: checkRootPermission(["statistics"]),
  },
  {
    label: "",
    title: "LABELS.rate",
    path: "rate",
    icon: "feedback",
    type: "general",
    active: "rate",
    permission: checkRootPermission(["rate"]),
  },
]);


const rail = ref(false);
const smallScreen = ref(false);

onMounted(() => {
  if (window.innerWidth < 992) {
    appStore.sidebar = false;
    smallScreen.value = true;
  } else {
    appStore.sidebar = true;
    smallScreen.value = false;
  }

  window.addEventListener("resize", () => {
    if (window.innerWidth < 992) {
      appStore.sidebar = false;
      smallScreen.value = true;
    } else {
      appStore.sidebar = true;
      smallScreen.value = false;
    }
  });
  // getUnreadMessages();
});

watch(
  () => route.path,
  () => {
    if (smallScreen.value) {
      appStore.sidebar = false;
    }
  }
);

function getUnreadMessages() {
  axios
    .get("un-read-messages-count")
    .then((res) => (appStore.unreadMessages = res.data.data.un_read_replies));
}
</script>
<style lang="scss">
.v-navigation-drawer {
  // height: calc(100vh - 55px) !important;
  @apply border-none;
  .v-navigation-drawer__content {
    .top_header {
      height: 130px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
    }
    .v-list {
      height: calc(100vh - 130px);
      @apply overflow-y-auto px-0;
      .v-list-item {
        @apply mb-0 text-sub  p-4 rounded-none relative font-semibold;
        .v-list-item__content {
          @apply flex items-center gap-1 justify-between;
        }
        &::before {
          content: "";
          @apply w-1 h-full absolute start-0 bg-white;
        }
        .v-list-item__overlay {
          @apply hidden;
        }
        &.active {
          @apply from-primary/30 to-white text-primary;
          &::before {
            @apply bg-primary;
          }
        }
      }
      &.railed {
        .v-list-item {
          @apply rounded-md p-2;
        }
        .v-list-item__prepend {
          > svg {
            margin-inline-end: 10px;
          }
        }
      }
    }
  }
}


</style>
