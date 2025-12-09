<template>
  <view>
    <view class="action-buttons">
      <view class="action-button" @click="handleCreateConversation">
        <text class="button-icon">+</text>
        <text class="button-text">发起会话</text>
      </view>
      <view class="action-button" @click="handleCreateGroupConversation">
        <text class="button-icon">+</text>
        <text class="button-text">发起群聊</text>
      </view>
    </view>
    <Conversation :onConversationSelect="handleConversationSelect"></Conversation>
    <view v-if="!conversationList?.length" class="empty-guide">
      <text class="guide-text">暂无会话，点击上方按钮开始聊天</text>
    </view>
  </view>
</template>

<script lang="ts" setup>
import { onShow } from '@dcloudio/uni-app'
import { watch, ref } from 'vue';
import Conversation from '../../TUIKit/components/ConversationList/ConversationList.vue'
import { useConversationListState } from '../../TUIKit';

const { conversationList, setActiveConversation, totalUnRead } = useConversationListState();

const updateTabBarBadge = () => {
  const tabBarList = ['pages/profile/profile', 'pages/conversation/conversation']
  const pages = getCurrentPages()
  const currentPage = pages[pages.length - 1]?.route
  const isTabBarPage = currentPage && tabBarList.includes(currentPage)

  if (!isTabBarPage) {
    return
  }

  if (totalUnRead.value > 0) {
    uni.setTabBarBadge({
      index: 0,
      text: totalUnRead.value > 99 ? '99+' : totalUnRead.value.toString()
    });
  }
};

watch(totalUnRead, updateTabBarBadge, { immediate: true });

const handleConversationSelect = (conversation) => {
  const { conversationID } = conversation
  setActiveConversation(conversationID);
  uni.navigateTo({
    url: '/pages/chat/chat',
  });
}

onShow(() => {
  if (totalUnRead.value > 0) {
    uni.setTabBarBadge({
      index: 0,
      text: totalUnRead.value > 99 ? '99+' : totalUnRead.value.toString()
    });
  } else {
    uni.removeTabBarBadge({ index: 0 });
  }
});

// 发起会话
const handleCreateConversation = () => {
  uni.navigateTo({
    url: '/pages/createConversation/createConversation?mode=createC2CConversation'
  })
};

// 发起群聊
const handleCreateGroupConversation = () => {
  uni.navigateTo({
    url: '/pages/createConversation/createConversation?mode=createGroupConversation'
  })
};
</script>

<style scoped>
.action-buttons {
  display: flex;
  justify-content: space-between;
  padding: 6px;
  background-color: #fff;
  border-bottom: 1px solid #f0f0f0;
}

.action-button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48%;
  height: 42px;
  background: linear-gradient(135deg, #4086FF 0%, #2E5CFF 100%);
  border-radius: 8px;
  box-shadow: 0 6px 20px rgba(64, 134, 255, 0.25), 0 2px 6px rgba(64, 134, 255, 0.15);
  transition: all 0.3s ease;
  cursor: pointer;
  gap: 6px;
  position: relative;
  overflow: hidden;
}

.action-button:active {
  transform: translateY(1px);
  box-shadow: 0 4px 15px rgba(64, 134, 255, 0.2), 0 1px 4px rgba(64, 134, 255, 0.1);
}

.action-button::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255, 255, 255, 0.15) 0%, transparent 100%);
    border-radius: 8px 8px 0 0;
}

.button-icon {
    font-size: 20px;
    font-weight: bold;
    color: #fff;
}

.button-text {
    font-size: 14px;
    font-weight: 500;
    color: #fff;
    text-align: center;
}

.empty-guide {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 20px;
}

.guide-text {
  font-size: 16px;
  color: #999;
  text-align: center;
}

</style>
