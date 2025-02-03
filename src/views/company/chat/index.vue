<template>
  <div class="h-full flex flex-col">
    <breadcrumbs :items="breadItems" :title="$t('LABELS.contacts')" />
    <div
      class="bg-white rounded-3xl h-full shadow-[0_7px_6px_0px,rgba(#B1B1B11A)] md:p-7 flex-1 flex flex-col"
    >
      <!-- <base-filter
        name="contacts"
        :inputs="[]"
        :btn-name="t(`BUTTONS.add`, { name: t('LABELS.contacts') })"
        icon="fas fa-plus"
        @action="$router.push('/contacts/form')"
      /> -->
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
          <!-- <div
            class="text-center"
            v-if="!$route.query.keyword && !$route.query.status"
          >
            <h3 class="mt-4 font-semibold text-text text-center">
              {{
                $t("TITLES.No have been added yet", {
                  name: $t("LABELS.contacts"),
                })
              }}
            </h3>
            <div class="flex items-center justify-center mt-7 gap-2 flex-wrap">
              <router-link
                to="/contacts/form"
                class="base-btn rounded-xl self-end"
              >
                <i class="fas fa-plus"></i>
                {{ $t(`BUTTONS.add`, { name: $t("LABELS.contacts") }) }}
              </router-link>
            </div>
          </div>
          <h3 v-else class="mt-4 font-semibold text-text text-center">
            {{ $t("TITLES.No Search result") }}
          </h3> -->
          <!-- component -->
          <div class="flex h-screen antialiased text-[#111827] bg-[#F9FAFB]">
            <div class="flex flex-row h-full w-full overflow-hidden">
              <!-- Sidebar -->
              <aside
                class="flex flex-col py-8 pl-6 pr-2 w-64 bg-white flex-shrink-0 shadow-lg"
              >
                <!-- Brand Header -->
                <div
                  class="flex flex-row items-center justify-center h-12 w-full"
                >
                  <div
                    class="flex items-center justify-center rounded-full text-[#4338CA] bg-[#E0E7FF] h-10 w-10"
                  >
                    <svg
                      class="w-6 h-6"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"
                      ></path>
                    </svg>
                  </div>
                  <div
                    class="ml-2 font-bold text-2xl text-[#4F46E5] tracking-wide"
                  >
                    QuickChat
                  </div>
                </div>

                <!-- User Profile Card -->
                <div
                  class="flex flex-col items-center bg-[#EEF2FF] border border-[#E5E7EB] mt-6 w-full py-6 px-4 rounded-lg shadow-sm"
                >
                  <div
                    class="h-20 w-20 rounded-full overflow-hidden border-2 border-[#C7D2FE]"
                  >
                    <!-- User image -->
                    <img
                      :src="userData?.image"
                      alt="Avatar"
                      class="h-full w-full object-cover"
                    />
                  </div>
                  <div class="mt-3 text-sm font-semibold text-[#111827]">
                    {{ userData?.full_name }}
                  </div>
                  <div class="text-xs text-[#6B7280]">
                    {{ userData?.email }}
                  </div>
                </div>

                <!-- Conversations List -->
                <div class="flex flex-col mt-8">
                  <!-- Active Conversations -->
                  <div
                    class="flex items-center justify-between text-sm font-semibold text-[#374151]"
                  >
                    <span>Active Conversations</span>
                    <span
                      class="flex items-center justify-center bg-[#E5E7EB] text-[#374151] h-5 w-5 rounded-full"
                    >
                      {{ activeChats.length }}
                    </span>
                  </div>
                  <div
                    class="flex flex-col space-y-2 mt-4 overflow-y-auto max-h-48"
                  >
                    <button
                      v-for="chat in activeChats"
                      :key="chat.id"
                      @click="selectChat(chat)"
                      class="flex items-center w-full px-3 py-2 rounded-lg transition-colors duration-200 hover:bg-[#EEF2FF]"
                      :class="{ 'bg-[#EEF2FF]': selectedChat?.id === chat.id }"
                    >
                      <div
                        class="flex items-center justify-center h-9 w-9 bg-[#C7D2FE] rounded-full font-medium text-[#4338CA]"
                      >
                        {{ chat.name.charAt(0).toUpperCase() }}
                      </div>
                      <div
                        class="ml-3 text-sm font-semibold text-[#111827] flex-grow text-left"
                      >
                        {{ chat.name }}
                      </div>
                      <div
                        v-if="chat.unread_count > 0"
                        class="flex items-center justify-center ml-auto text-xs text-white bg-[#EF4444] h-5 w-5 rounded-full"
                      >
                        {{ chat.unread_count }}
                      </div>
                    </button>
                  </div>

                  <!-- Archived Conversations -->
                  <!-- <div class="mt-6">
                    <div
                      class="flex items-center justify-between text-sm font-semibold text-[#374151]"
                    >
                      <span>Archived</span>
                      <span
                        class="flex items-center justify-center bg-[#E5E7EB] text-[#374151] h-5 w-5 rounded-full"
                      >
                        {{ archivedChats.length }}
                      </span>
                    </div>
                    <div class="flex flex-col space-y-2 mt-4">
                      <button
                        v-for="chat in archivedChats"
                        :key="chat.id"
                        @click="selectChat(chat)"
                        class="flex items-center w-full px-3 py-2 rounded-lg transition-colors duration-200 hover:bg-[#EEF2FF]"
                        :class="{
                          'bg-[#EEF2FF]': selectedChat?.id === chat.id,
                        }"
                      >
                        <div
                          class="flex items-center justify-center h-9 w-9 bg-[#A5B4FC] rounded-full font-medium text-white"
                        >
                          {{ chat.name.charAt(0).toUpperCase() }}
                        </div>
                        <div class="ml-3 text-sm font-semibold text-[#111827]">
                          {{ chat.name }}
                        </div>
                      </button>
                    </div>
                  </div> -->
                </div>
              </aside>

              <!-- Chat Conversation Area -->
              <main class="flex flex-col flex-auto h-full p-6">
                <div
                  class="flex flex-col flex-auto flex-shrink-0 rounded-2xl bg-[#F3F4F6] shadow-lg h-full p-4"
                >
                  <!-- Chat Header -->
                  <div
                    v-if="selectedChat"
                    class="flex items-center pb-4 border-b border-[#E5E7EB]"
                  >
                    <div class="flex items-center">
                      <div
                        class="flex items-center justify-center h-10 w-10 rounded-full bg-[#6366F1] text-white font-medium"
                      >
                        {{ selectedChat.name.charAt(0).toUpperCase() }}
                      </div>
                      <div class="ml-3">
                        <div class="text-sm font-semibold text-[#111827]">
                          {{ selectedChat.name }}
                        </div>
                      </div>
                    </div>
                  </div>

                  <!-- Chat Messages -->
                  <div
                    v-if="selectedChat"
                    class="flex flex-col flex-auto overflow-y-auto mt-4"
                  >
                    <div class="flex flex-col gap-y-2">
                      <template
                        v-for="message in selectedChat.messages"
                        :key="message.id"
                      >
                        <!-- Received Message -->
                        <div
                          v-if="message.isReceiver"
                          class="  p-2"
                        >
                          <div class="flex items-start">
                            <div
                              class="flex items-center justify-center h-9 w-9 rounded-full bg-[#6366F1] text-white font-medium flex-shrink-0"
                            >
                              {{ selectedChat.name.charAt(0).toUpperCase() }}
                            </div>
                            <div
                              class="ml-3 relative text-sm bg-white py-2 px-4 shadow rounded-lg"
                            >
                              <div>{{ message.text }}</div>
                              <div
                                v-if="message.time"
                                class="absolute text-xs bottom-0 right-0 -mb-5 mr-2 text-[#6B7280]"
                              >
                                {{ message.time }}
                              </div>
                            </div>
                          </div>
                        </div>
                        <!-- Sent Message -->
                        <div v-else class="flex flex-row-reverse p-2">
                          <div
                            class="flex items-end justify-end flex-row-reverse"
                          >
                            <div
                              class="flex items-center justify-center h-9 w-9 rounded-full bg-[#6366F1] text-white font-medium flex-shrink-0"
                            >
                              {{ userData?.full_name?.charAt(0).toUpperCase() }}
                            </div>
                            <div
                              class="mr-3 relative text-sm bg-[#E0E7FF] py-2 px-4 shadow rounded-lg"
                            >
                              <div>{{ message.text }}</div>
                              <div
                                v-if="message.time"
                                class="absolute text-xs bottom-0 right-0 -mb-5 mr-2 text-[#6B7280]"
                              >
                                {{ message.time }}
                              </div>
                            </div>
                          </div>
                        </div>
                      </template>
                    </div>
                  </div>

                  <!-- Placeholder for No Selected Chat -->
                  <div v-else class="flex-1 flex items-center justify-center">
                    <div class="text-[#6B7280] text-lg">
                      Select a chat to start messaging
                    </div>
                  </div>

                  <!-- Message Input -->
                  <div
                    v-if="selectedChat"
                    class="flex flex-row items-center h-16 rounded-xl bg-white w-full px-4 mt-4 shadow-inner"
                  >
                    <button
                      class="flex items-center justify-center text-[#6B7280] hover:text-[#374151]"
                    >
                      <svg
                        class="w-5 h-5"
                        fill="none"
                        stroke="currentColor"
                        viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg"
                      >
                        <path
                          stroke-linecap="round"
                          stroke-linejoin="round"
                          stroke-width="2"
                          d="M15.172 7l-6.586 6.586a2 2 0 102.828 2.828l6.414-6.586a4 4 0 00-5.656-5.656l-6.415 6.585a6 6 0 108.486 8.486L20.5 13"
                        ></path>
                      </svg>
                    </button>
                    <div class="flex-grow ml-4">
                      <input
                        v-model="newMessage"
                        type="text"
                        class="w-full border border-[#D1D5DB] rounded-full focus:outline-none focus:border-[#818CF8] pl-4 h-10"
                        placeholder="Type your message..."
                        @keyup.enter="sendMessage"
                      />
                    </div>
                    <button
                      @click="sendMessage"
                      class="ml-4 flex items-center justify-center bg-[#6366F1] hover:bg-[#4F46E5] text-white font-medium rounded-full px-4 py-2 transition-colors duration-200"
                      :disabled="!newMessage?.trim()"
                    >
                      Send
                      <svg
                        class="w-4 h-4 ml-2 transform rotate-45 -mt-px"
                        fill="none"
                        stroke="currentColor"
                        viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg"
                      >
                        <path
                          stroke-linecap="round"
                          stroke-linejoin="round"
                          stroke-width="2"
                          d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"
                        ></path>
                      </svg>
                    </button>
                  </div>
                </div>
              </main>
            </div>
          </div>
        </template>

        <!-- <template v-slot:[`item.name`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card
              :title="`${item.full_name}`"
              :image="item.image"
            />
          </div>
        </template> -->
        <!-- <template v-slot:[`item.country.name`]="{ item }">
          <div class="flex gap-2 items-center flex-wrap">
            <small-details-card
              :title="`${item.country.name}`"
              :image="item.image"
            />
          </div>
        </template> -->

        <!-- <template v-slot:[`item.action`]="{ item }">
          <div class="flex items-center gap-4">
            <router-link :to="`contacts/form/${item.id}`">
              <button class="icon-button w-5">
                <svg-icon name="eye2" />
              </button>
            </router-link>

            <router-link :to="`/contacts/reply/${item.id}`">
              <svg-icon class="text-primary rotate-180" name="reply" filled />
            </router-link>
          </div>
        </template> -->
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
// import { useAppAuth } from "@/store/auth";

const route = useRoute();
const { t } = useI18n();
// const appAuth = useAppAuth();
// const userData = appAuth.getProfile();
// console.log(userData);

const breadItems = [
  {
    name: t("TITLES.home"),
    path: "/",
    imgIcon: "settings.svg",
  },
  {
    name: t("LABELS.contacts"),
    path: "/contacts",
    imgIcon: "",
  },
];

const items = ref([]);
const loading = ref(false);
const paginator = ref(null);

const headers = [
  {
    title: t("LABELS.Name", { name: t("LABELS.city") }),
    align: "start",
    sortable: false,
    key: "name",
  },

  // {
  //   title: t("LABELS.Name", { name: t("LABELS.country") }),
  //   align: "start",
  //   sortable: false,
  //   key: "country.name",
  // },
  // {
  //   title: t("LABELS.Country key"),
  //   align: "start",
  //   sortable: false,
  //   key: "country.phone_code",
  // },
  {
    title: t("LABELS.Actions"),
    align: "start",
    sortable: false,
    key: "action",
  },
  // {
  //   title: t("LABELS.email"),
  //   align: "start",
  //   sortable: false,
  //   key: "email",
  // },
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

const activeChats = ref([
  {
    id: 1,
    name: "John Doe",
    unread_count: 3,
    lastMessage: "Hey, how are you doing?",
    time: "2:30 PM",
    messages: [
      {
        id: 1,
        text: "Hey, how are you doing?",
        time: "2:30 PM",
        sender: "John Doe",
        senderId: 1,
        isReceiver: true,
        isRead: false,
      },
      {
        id: 2,
        text: "I'm doing well, thanks for asking!",
        time: "2:31 PM",
        sender: "Me",
        senderId: null,
        isReceiver: false,
        isRead: true,
      },
      {
        id: 3,
        text: "That's great to hear. I wanted to discuss the new project requirements",
        time: "2:32 PM",
        sender: "John Doe",
        senderId: 1,
        isReceiver: true,
        isRead: false,
      },
      {
        id: 4,
        text: "Sure, what would you like to know?",
        time: "2:33 PM",
        sender: "Me",
        senderId: null,
        isReceiver: false,
        isRead: true,
      },
      {
        id: 5,
        text: "Could you explain the timeline for deliverables?",
        time: "2:34 PM",
        sender: "John Doe",
        senderId: 1,
        isReceiver: true,
        isRead: false,
      },
      {
        id: 6,
        text: "We're looking at a 6-week timeline. Phase 1 should be completed by next Friday",
        time: "2:35 PM",
        sender: "Me",
        senderId: null,
        isReceiver: false,
        isRead: true,
      },
      {
        id: 7,
        text: "Perfect, that works for our schedule",
        time: "2:36 PM",
        sender: "John Doe",
        senderId: 1,
        isReceiver: true,
        isRead: false,
      },
      {
        id: 8,
        text: "I'll send you the detailed breakdown tomorrow",
        time: "2:37 PM",
        sender: "Me",
        senderId: null,
        isReceiver: false,
        isRead: true,
      },
    ],
  },
  {
    id: 2,
    name: "Jane Smith",
    unread_count: 0,
    lastMessage: "The project is ready for review",
    time: "11:45 AM",
    messages: [
      {
        id: 1,
        text: "Hi, I've completed the initial review",
        time: "11:30 AM",
        sender: "Jane Smith",
        senderId: 2,
        isReceiver: true,
        isRead: true,
      },
      {
        id: 2,
        text: "Great! How are the results looking?",
        time: "11:32 AM",
        sender: "Me",
        senderId: null,
        isReceiver: false,
        isRead: true,
      },
      {
        id: 3,
        text: "Very promising. All test cases passed",
        time: "11:35 AM",
        sender: "Jane Smith",
        senderId: 2,
        isReceiver: true,
        isRead: true,
      },
      {
        id: 4,
        text: "The project is ready for review",
        time: "11:45 AM",
        sender: "Jane Smith",
        senderId: 2,
        isReceiver: true,
        isRead: true,
      },
    ],
  },
  {
    id: 3,
    name: "Mike Johnson",
    unread_count: 1,
    lastMessage: "When is the next meeting?",
    time: "Yesterday",
    messages: [
      {
        id: 1,
        text: "Hello, just checking about the meeting",
        time: "Yesterday",
        sender: "Mike Johnson",
        senderId: 3,
        isReceiver: true,
        isRead: true,
      },
      {
        id: 2,
        text: "When is the next meeting?",
        time: "Yesterday",
        sender: "Mike Johnson",
        senderId: 3,
        isReceiver: true,
        isRead: false,
      },
    ],
  },
]);

// const archivedChats = ref([
//   {
//     id: 4,
//     name: "Sarah Wilson",
//     unread_count: 0,
//     lastMessage: "Thanks for your help!",
//     time: "2 days ago"
//   },
//   {
//     id: 5,
//     name: "Tom Brown",
//     unread_count: 0,
//     lastMessage: "Project completed successfully",
//     time: "1 week ago"
//   }
// ]);
const selectedChat = ref(null);

function selectChat(chat) {
  selectedChat.value = chat;

  // Mark messages as read when chat is selected
  if (chat.messages) {
    chat.messages.forEach((message) => {
      message.isRead = true;
    });
  }

  // Reset unread count
  chat.unread_count = 0;
}
const newMessage = ref("");

function sendMessage() {
  if (!selectedChat.value || !newMessage.value.trim()) return;

  // Create new message object
  const message = {
    id: Date.now(), // Temporary ID
    text: newMessage.value,
    time: new Date().toLocaleTimeString([], {
      hour: "2-digit",
      minute: "2-digit",
    }),
    sender: userData.value?.full_name,
    senderId: userData.value?.id,
    isReceiver: false,
    isRead: true,
  };

  // Add message to selected chat
  console.log(selectedChat.value);
  if (!selectedChat.value.messages) {
    selectedChat.value.messages = [];
  }
  selectedChat.value.messages.push(message);
  // // Save messages to localStorage to persist across page reloads
  // const chatKey = `chat_${selectedChat.value.id}`;
  // const savedMessages = JSON.parse(localStorage.getItem(chatKey) || '[]');
  // savedMessages.push(message);
  // localStorage.setItem(chatKey, JSON.stringify(savedMessages));

  // Clear input
  newMessage.value = "";

  // TODO: Send message to backend
  // axios.post('messages', {
  //   chat_id: selectedChat.value.id,
  //   text: message.text
  // });
}

// console.log(userData);
// const userData = appAuth.userData;
// const appAuth = useAppAuth();
const userData = ref(null);

function fetchProfile() {
  axios.get("profile").then((res) => {
    userData.value = res.data.data;
  });
}

function fetchData() {
  loading.value = true;
  axios
    .get("support_chat", {
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
  fetchProfile();
});
</script>
