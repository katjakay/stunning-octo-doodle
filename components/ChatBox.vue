<script lang="ts" setup>
import { nanoid } from 'nanoid';
import { Message, User } from '~~/types';

// Define props that will be used in the components
const props = defineProps<{
  messages: Message[];
  users: User[];
  me: User;
  usersTyping?: User[];
}>();

// Creating an event with a function so send a message
const emit = defineEmits<{
  (event: 'newMessage', newMessage: Message): void;
}>();

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

const isOpen = ref(true);

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

<template>
  <div class="fixed bottom-[10px] right-[10px]">
    <button
      v-show="!isOpen"
      class="bg-blue-800 p-3 rounded"
      @click="isOpen = true"
    >
      <IconChat class="w-8 h-8 text-white" />
    </button>

    <div
      v-if="isOpen"
      class="box bg-gray-200 dark:bg-gray-800 w-[450px] rounded p-1"
    >
      <header
        class="bg-gray-600 dark:bg-gray-900 px-4 flex justify-between items-center text-white"
      >
        Customer Support Chat

        <!-- Setting the different state of the button here -->
        <button class="p-4 pr-0" @click="isOpen = false">
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
          :user="getUser(message.userId)"
          :message="message"
          :is-mine="me.id === message.userId"
        />
      </div>

      <footer class="p-2">
        <input
          type="text"
          v-model="textMessage"
          @keypress.enter.exact.prevent="sendMessage"
          placeholder="Type your message"
          class="input w-full block"
        />
      </footer>
    </div>
  </div>
</template>
