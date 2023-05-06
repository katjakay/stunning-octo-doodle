<script lang="ts" setup>
// @ts-expect-error:no types
import Markdown from 'vue3-markdown-it';
import { Message, User } from '~~/types';

const props = defineProps<{
  user?: User;
  message: Message;
  isMine?: boolean;
}>();

// (Vueuse) userTimeAgo = automatically updates the time ago string when the time changes -> to display in the header
const relativeTime = useTimeAgo(() => props.message.createdAt);
</script>

<template>
  <div class="chat" :class="{ 'chat-end': isMine, 'chat-start': !isMine }">
    <div class="chat-image avatar">
      <div class="w-10 rounded-full">
        <img :src="user?.avatar" class="block w-10 rounded-full" />
      </div>
    </div>
    <div class="chat-header mb-1">
      <strong>{{ user?.name }}</strong>
      &nbsp;
      <time>{{ relativeTime }}</time>
    </div>

    <div
      class="chat-bubble bg-gray-600 max-w-max w-full prose-slates prose-sm py-0"
    >
      <Markdown :source="message.text" class="w-full" />
    </div>
  </div>
</template>
