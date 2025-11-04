<template>
  <div class="TUIChat">
    <MessageList />
    <MessageInput />
  </div>
</template>

<script lang="ts" setup>
import MessageInput from '../../TUIKit/components/MessageInput/MessageInput.vue';
import MessageList from '../../TUIKit/components/MessageList/MessageList.vue';
</script>

<style>
.TUIChat {
  display: flex;
  flex-direction: column;
  width: 100vw;
  height: 100vh;
}
</style>