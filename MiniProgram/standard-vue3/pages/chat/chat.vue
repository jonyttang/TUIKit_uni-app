<template>
  <div class="TUIChat">
    <!-- 更多浮窗 -->
    <div v-if="isInGroup" class="more-float" @click="goToGroupSetting">
      <div class="more-text">更多</div>
    </div>
    <MessageList />
    <MessageInput />
  </div>
</template>

<script lang="ts" setup>
import MessageInput from '../../TUIKit/components/MessageInput/MessageInput.vue';
import MessageList from '../../TUIKit/components/MessageList/MessageList.vue';
import { useGroupSettingState } from '../../TUIKit';

const { isInGroup } = useGroupSettingState()

const goToGroupSetting = () => {
  uni.navigateTo({
    url: '/TUIKit/components/Group/GroupSettings'
  });
};
</script>

<style lang="scss" scoped>
.TUIChat {
  display: flex;
  flex-direction: column;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  position: relative;
}

.more-float {
  position: absolute;
  top: 20px;
  right: 8px;
  z-index: 20;
  background: linear-gradient(135deg, #4086FF 0%, #2E5CFF 100%);
  backdrop-filter: blur(10px);
  border-radius: 12rpx;
  padding: 6rpx 12rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4rpx;
  box-shadow:
    0 2rpx 8rpx rgba(64, 134, 255, 0.3),
    0 4rpx 16rpx rgba(64, 134, 255, 0.2),
    0 8rpx 24rpx rgba(64, 134, 255, 0.15);
  cursor: pointer;
  transition: all 0.2s ease;
  border: 1rpx solid rgba(255, 255, 255, 0.2);
  min-width: 80rpx;
  height: 36rpx;
}

.more-float:active {
  transform: scale(0.95);
  box-shadow:
    0 1rpx 4rpx rgba(64, 134, 255, 0.4),
    0 2rpx 8rpx rgba(64, 134, 255, 0.3),
    0 4rpx 12rpx rgba(64, 134, 255, 0.2);
}

.more-icon {
  color: white;
  font-size: 20px;
  font-weight: bold;
  line-height: 1;
}

.more-text {
  color: white;
  font-size: 12px;
  font-weight: 500;
  letter-spacing: 0.5rpx;
  text-shadow: 0 1rpx 2rpx rgba(0, 0, 0, 0.1);
}
</style>