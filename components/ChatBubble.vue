<script lang="ts" setup>
// @ts-expect-error:no types
import Markdown from 'vue3-markdown-it';
import { Message, User } from '~~/types';

const props = defineProps<{
  user?: User;
  message: Message;
  myMessage?: boolean;
}>();

// (Vueuse) userTimeAgo = automatically updates the time ago string when the time changes -> to display in the header
const relativeTime = useTimeAgo(() => props.message?.createdAt ?? new Date());
</script>

<template>
  <div
    class="chat"
    :class="{ 'chat-end': myMessage, 'chat-start': !myMessage }"
  >
    <div class="chat-image avatar">
      <div class="w-10 rounded-full">
        <img :src="user?.avatar" class="block w-10 rounded-full" />
      </div>
    </div>
    <div class="chat-header text-xs mb-2">
      <strong>{{ user?.name }}</strong>
      &nbsp;
      <time v-if="message" class="text-xs">{{
        useTimeAgo(message?.createdAt).value
      }}</time>
    </div>

    <div
      class="chat-bubble bg-gray-600 max-w-max w-full prose-slates prose-sm py-0"
    >
      <slot>
        <Markdown :source="message?.text" class="w-full" />
      </slot>
    </div>
  </div>
</template>
