<template>
  <div class="TUIChat">
    <MessageList class="message-list" />
    <MessageInput />
  </div>
</template>

<script lang="ts">
import MessageInput from '../../TUIKit/components/MessageInput/MessageInput.vue';
import MessageList from '../../TUIKit/components/MessageList/MessageList.vue';

export default {
  components: {
    MessageInput,
    MessageList
  }
}
</script>

<style lang="scss">
.TUIChat {
  display: flex;
  flex-direction: column;
  width: 100vw;
  height: 100vh;

  .message-list {
    display: flex;
    flex: 1;
    min-height: 0;
    width: 100%;
    height: 100%;
  }
}
</style>