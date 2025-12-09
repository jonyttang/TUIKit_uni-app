<template>
  <view class="create-conversation-page">
    <UserPicker :maxCount="maxCount" @confirm="handleConfirm" />
    <CreateGroup v-if="showGroupForm" @close="handleCloseGroupForm" @submit="handleGroupSubmit" />
  </view>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { onLoad } from '@dcloudio/uni-app';
import { useConversationListState } from '../../TUIKit/index';
import UserPicker from '../../TUIKit/components/UserPicker/UserPicker.vue';
import CreateGroup from '../../TUIKit/components/Group/CreateGroup.vue';

const { setActiveConversation, createGroupConversation } = useConversationListState();

const maxCount = ref(Number.POSITIVE_INFINITY);
const isGroupMode = ref(false);
const showGroupForm = ref(false);
const selectedUserIDs = ref<string[]>([]);
const isLoading = ref(false);

onLoad((options) => {
  const { mode } = options
  isGroupMode.value = mode === 'createGroupConversation';
  if (!isGroupMode.value) {
    maxCount.value = 1;
  }
  const title = isGroupMode.value ? '选择群成员' : '选择用户';
  uni.setNavigationBarTitle({
    title
  });
});

const handleConfirm = async (userIDs: string[]) => {
  if (userIDs.length === 0) return;

  // 保存选中的用户ID
  selectedUserIDs.value = userIDs;

  if (isGroupMode.value) {
    showGroupForm.value = true;
  } else {
    await handleCreateC2CConversation(userIDs[0]);
  }
};

const handleGroupSubmit = async (groupInfo: any) => {
  isLoading.value = true;

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
      memberList: selectedUserIDs.value.map(userID => ({ userID }))
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
    isLoading.value = false;
  }
};

const handleCreateC2CConversation = async (userID: string) => {
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
};

const handleCloseGroupForm = () => {
  showGroupForm.value = false;
}

</script>

<style lang="scss" scoped>
.create-conversation-page {
  height: 100vh;
  background-color: #f8f9fa;
}
</style>