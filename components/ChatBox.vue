<script lang="ts" setup>
import { nanoid } from 'nanoid';
import { Message, User } from '~~/types';

// Define props that will be used in the components
const props = withDefaults(
  defineProps<{
    messages: Message[];
    users: User[];
    me: User;
    usersTyping?: User[];
  }>(),
  {
    usersTyping: () => [],
  },
);

// Creating an event with a function to send a message
const emit = defineEmits<{
  (event: 'newMessage', newMessage: Message): void;
}>();

const open = ref(false);

function getUser(id: string) {
  return props.users.find((user) => user.id === id);
}

const messageBox = ref<HTMLElement>();
watch(
  () => props.messages.length,
  async () => {
    await nextTick();
    if (messageBox.value) {
      messageBox.value.scrollTop = messageBox.value?.scrollHeight;
    }
  },
);

const textMessage = ref('');

function sendMessage() {
  emit('newMessage', {
    id: nanoid(),
    userId: props.me.id,
    createdAt: new Date(),
    text: textMessage.value,
  });

  textMessage.value = '';
}
</script>

<!-- Presentaton in frontend -->
<template>
  <div class="fixed bottom-[10px] right-[10px]">
    <button v-show="!open" class="bg-blue-800 p-3 rounded" @click="open = true">
      <IconChat class="w-8 h-8 text-white" />
    </button>

    <div
      v-if="open"
      class="box bg-gray-200 dark:bg-gray-800 w-[450px] rounded p-2"
    >
      <header
        class="bg-gray-600 dark:bg-gray-900 px-4 flex justify-between items-center text-white"
      >
        Customer Support Chat

        <!-- Setting the different state of the button here -->
        <button class="p-4 pr-0" @click="open = false">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="white"
            class="w-6 h-6"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M19.5 8.25l-7.5 7.5-7.5-7.5"
            />
          </svg>
        </button>
      </header>

      <div ref="messageBox" class="messages p-4 overflow-y-scroll max-h-[80vh]">
        <!-- The content of the chat goes here -->
        <ChatBubble
          v-for="message in messages"
          :key="message.id"
          :message="message"
          :user="getUser(message.userId)"
          :my-message="message.userId === me.id"
        />

        <ChatBubble
          v-for="message in messages"
          :key="message.id"
          :message="message"
          :user="getUser(message.userId)"
          :my-message="message.userId === me.id"
        />

        <ChatBubble v-for="user in usersTyping" :key="user.id" :user="user">
          <AppLoading />
        </ChatBubble>
      </div>
      <!-- Footer -->
      <footer class="p-2">
        <input
          ref="input"
          class="input w-full px-2 block"
          type="text"
          placeholder="Type your message"
          @keypress.enter="
            $emit('newMessage', {
              id: nanoid(),
              userId: me.id,
              createdAt: new Date(),
              text: ($event.target as HTMLInputElement).value,
            });
            ($event.target as HTMLInputElement).value = '';
          "
        />

        <div class="h-6 py-1 px-2 text-sm italic">
          <span v-if="usersTyping.length">
            {{ usersTyping.map((user) => user.name).join(' and ') }}
            {{ usersTyping.length === 1 ? 'is' : 'are' }} typing
          </span>
        </div>
      </footer>
    </div>
  </div>
</template>
