<template>
  <view class="toc-profile-container">
    <view class="profile-card">
      <view class="avatar-section">
        <image class="avatar" v-if="loginUserInfo.avatarUrl" :src="loginUserInfo.avatarUrl" mode="aspectFill" />
        <view class="avatar-placeholder" v-else>
          <text class="placeholder-text">未设置头像</text>
        </view>
        <view class="user-info">
          <text class="username">{{ loginUserInfo.userName || '未设置昵称' }}</text>
          <text class="user-id">userID: {{ loginUserInfo.userId }}</text>
        </view>
      </view>
    </view>

    <view class="edit-area">
      <view class="input-group">
        <text class="input-label">修改昵称</text>
        <input class="input-field" v-model="editNick" placeholder="请输入新昵称" />
      </view>
      <view class="input-group">
        <text class="input-label">修改头像</text>
        <input class="input-field" v-model="editAvatar" placeholder="请输入头像URL" />
      </view>
    </view>

    <view class="action-area">
      <button class="save-btn" @click="handleSave">保存设置</button>
      <button class="logout-btn" @click="handleLogout">退出登录</button>
    </view>
  </view>
</template>

<script>
import { useLoginState } from '../../TUIKit';

export default {
  data() {
    return {
      editNick: '',
      editAvatar: '',
      loginUserInfo: {
        avatarUrl: '',
        userName: '',
        userId: ''
      }
    }
  },
  mounted() {
    this.initLoginState();
  },
  methods: {
    initLoginState() {
      const state = useLoginState();
      this.$loginState = state;
      this.loginUserInfo = state.loginUserInfo;
    
      if (state.loginUserInfo && typeof state.loginUserInfo === 'object') {
        this.$watch(() => state.loginUserInfo, (newVal) => {
          this.loginUserInfo = newVal;
        }, { deep: true });
      }
    },
    
    async handleSave() {
      try {
        const updateData = {};
        
        if (this.editNick) {
          updateData.userName = this.editNick;
        }
        if (this.editAvatar) {
          updateData.avatarUrl = this.editAvatar;
        }

        if (Object.keys(updateData).length === 0) {
          return uni.showToast({
            title: '请至少修改一项信息',
            icon: 'none'
          });
        }

        await this.$loginState.setSelfInfo(updateData);
        uni.showToast({
          title: '修改成功',
          icon: 'none'
        });
        
        this.editNick = '';
        this.editAvatar = '';
      } catch (error) {
        uni.showToast({
          title: '修改失败',
          icon: 'none'
        });
      }
    },
    
    async handleLogout() {
      try {
        await this.$loginState.logout();
        uni.showToast({
          title: '退出成功',
          icon: 'none'
        });
        setTimeout(() => {
          uni.reLaunch({ url: '/pages/login/login' });
        }, 1500);
      } catch (error) {
        uni.showToast({
          title: '退出失败',
          icon: 'none'
        });
      }
    }
  }
}
</script>

<style scoped>
.toc-profile-container {
  background-color: #f8f9fa;
  max-width: 100vw;
  max-height: 100vh;
  padding: 16px;
}

.profile-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.avatar-section {
  display: flex;
  align-items: center;
}

.avatar {
  width: 72px;
  height: 72px;
  border-radius: 50%;
  background-color: #f0f2f5;
}

.avatar-placeholder {
  width: 72px;
  height: 72px;
  border-radius: 50%;
  background-color: #f0f2f5;
  display: flex;
  align-items: center;
  justify-content: center;
}

.placeholder-text {
  color: #8c8c8c;
  font-size: 14px;
}

.user-info {
  margin-left: 16px;
  flex: 1;
}

.username {
  font-size: 18px;
  font-weight: 500;
  color: #1a1a1a;
  display: block;
  margin-bottom: 4px;
}

.user-id {
  font-size: 14px;
  color: #8c8c8c;
}

.edit-area {
  background: white;
  border-radius: 12px;
  padding: 16px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.input-group {
  margin-bottom: 16px;
}

.input-group:last-child {
  margin-bottom: 0;
}

.input-label {
  font-size: 14px;
  color: #8c8c8c;
  display: block;
  margin-bottom: 8px;
}

.input-field {
  width: 100%;
  height: 48px;
  padding: 0 16px;
  border: 1px solid #e8e8e8;
  border-radius: 8px;
  font-size: 16px;
  background-color: #fafafa;
}

.action-area {
  display: flex;
  gap: 12px;
}

.save-btn {
  flex: 1;
  background-color: #07C160;
  color: white;
  height: 48px;
  line-height: 48px;
  border-radius: 8px;
  font-size: 16px;
  border: none;
}

.logout-btn {
  flex: 1;
  background-color: white;
  color: #ff4d4f;
  height: 48px;
  line-height: 48px;
  border-radius: 8px;
  font-size: 16px;
  border: 1px solid #ff4d4f;
}
</style>
