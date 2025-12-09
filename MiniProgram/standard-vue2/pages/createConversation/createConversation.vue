<template>
  <view class="create-conversation-page">
    <UserPicker :maxCount="maxCount" @confirm="handleConfirm" />
    <CreateGroup v-if="showGroupForm" @close="handleCloseGroupForm" @submit="handleGroupSubmit" />
  </view>
</template>

<script>
import { useConversationListState } from '../../TUIKit';
import UserPicker from '../../TUIKit/components/UserPicker/UserPicker.vue';
import CreateGroup from '../../TUIKit/components/Group/CreateGroup.vue';

const { createGroupConversation, setActiveConversation } = useConversationListState();

export default {
  components: {
    UserPicker,
    CreateGroup
  },
  data() {
    return {
      maxCount: 100,
      isGroupMode: false,
      showGroupForm: false,
      selectedUserIDs: [],
      isLoading: false
    }
  },
  onLoad(options) {
    const { mode } = options;
    this.isGroupMode = mode === 'createGroupConversation';
    if (!this.isGroupMode) {
      this.maxCount = 1;
    }
    const title = this.isGroupMode ? '选择群成员' : '选择用户';
    uni.setNavigationBarTitle({
      title
    });
  },
  methods: {
    async handleConfirm(userIDs) {
      if (userIDs.length === 0) return;

      // 保存选中的用户ID
      this.selectedUserIDs = userIDs;

      if (this.isGroupMode) {
        this.showGroupForm = true;
      } else {
        await this.handleCreateC2CConversation(userIDs[0]);
      }
    },

    async handleGroupSubmit(groupInfo) {
      this.isLoading = true;

      try {
        // 显示创建中提示
        uni.showLoading({
          title: '创建群聊中...',
          mask: true
        });

        const conversation = await createGroupConversation({
          ...groupInfo,
          type: 'Public', // 默认创建公开群组，支持邀请用户和解散群组
          inviteOption: 'FreeAccess', // 自由加入
          memberList: this.selectedUserIDs.map(userID => ({ userID }))
        });

        // 隐藏创建中提示，显示成功提示
        uni.hideLoading();
        uni.showToast({
          title: '群聊创建成功',
          icon: 'success',
          duration: 1500
        });

        // 设置活跃会话
        await setActiveConversation(conversation.conversationID);

        uni.redirectTo({
          url: '/pages/chat/chat'
        });

      } catch (error) {
        uni.hideLoading();
        console.error('创建群聊失败:', error);
        uni.showToast({
          title: '创建群聊失败',
          icon: 'none',
          duration: 2000
        });
      } finally {
        this.isLoading = false;
      }
    },

    async handleCreateC2CConversation(userID) {
      try {
        await setActiveConversation(`C2C${userID}`);
        uni.redirectTo({
          url: '/pages/chat/chat'
        });
      } catch (error) {
        console.error('打开会话失败:', error);
        uni.showToast({
          title: '打开会话失败',
          icon: 'none'
        });
      }
    },

    handleCloseGroupForm() {
      this.showGroupForm = false;
    }
  }
}
</script>

<style lang="scss" scoped>
.create-conversation-page {
  height: 100vh;
  background-color: #f8f9fa;
}
</style>