<template>
  <view>
    <Conversation :onConversationSelect="handleConversationSelect"></Conversation>
    <view v-if="!conversationList || !conversationList.length" class="empty-guide">
      <text class="guide-text">暂无会话，请输入用户ID创建聊天</text>
      <view class="input-container">
        <input v-model="inputUserID" class="userid-input" placeholder="请输入对方用户ID"
          placeholder-class="placeholder-style" />
        <button class="guide-btn" @click="handleCreateConversation">创建会话</button>
      </view>
    </view>
  </view>
</template>

<script>
import Conversation from '../../TUIKit/components/ConversationList/ConversationList.vue'
import { useConversationListState } from '../../TUIKit';

const conversationState = useConversationListState();

export default {
  components: {
    Conversation
  },
  data() {
    return {
      inputUserID: '',
      conversationList: conversationState.conversationList || [],
      totalUnRead: conversationState.totalUnRead || 0
    }
  },
  watch: {
    totalUnRead(newVal) {
      this.updateTabBarBadge();
    }
  },
  mounted() {
    this.$watch(
      () => conversationState.conversationList,
      (newVal) => {
        this.conversationList = newVal || [];
      },
      { immediate: true, deep: true }
    );
    
    this.$watch(
      () => conversationState.totalUnRead,
      (newVal) => {
        this.totalUnRead = newVal || 0;
      },
      { immediate: true }
    );
  },
  onShow() {
    this.updateTabBarBadge();
  },
  methods: {
    async handleCreateConversation() {
      if (!this.inputUserID.trim()) {
        uni.showToast({
          title: '请输入用户ID',
          icon: 'none'
        });
        return;
      }

      try {
        await conversationState.createC2CConversation(this.inputUserID);
        this.inputUserID = '';
      } catch (error) {
        uni.showToast({
          title: '创建会话失败，请检查用户ID是否注册',
          icon: 'none'
        });
      }
    },
    
    updateTabBarBadge() {
      const tabBarList = ['pages/profile/profile', 'pages/conversation/conversation'];
      const pages = getCurrentPages();
      const currentPage = pages[pages.length - 1] ? pages[pages.length - 1].route : '';
      const isTabBarPage = currentPage && tabBarList.includes(currentPage);

      if (!isTabBarPage) {
        return;
      }

      if (this.totalUnRead > 0) {
        uni.setTabBarBadge({
          index: 0,
          text: this.totalUnRead > 99 ? '99+' : this.totalUnRead.toString()
        });
      } else {
        uni.removeTabBarBadge({ index: 0 });
      }
    },
    
    handleConversationSelect(conversation) {
      const { conversationID } = conversation;
      conversationState.setActiveConversation(conversationID);
      uni.navigateTo({
        url: '/pages/chat/chat',
      });
    }
  }
}
</script>

<style scoped>
.empty-guide {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 20px;
}

.guide-text {
  font-size: 16px;
  color: red;
  margin-bottom: 20px;
}

.input-container {
  width: 80%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userid-input {
  width: 100%;
  height: 40px;
  padding: 0 10px;
  margin-bottom: 15px;
  border: 1px solid #eee;
  border-radius: 6px;
  font-size: 14px;
}

.placeholder-style {
  color: #ccc;
}

.guide-btn {
  width: 50%;
  line-height: 40px;
  height: 40px;
  background-color: #07c160;
  color: white;
  border-radius: 6px;
  font-size: 16px;
}
</style>