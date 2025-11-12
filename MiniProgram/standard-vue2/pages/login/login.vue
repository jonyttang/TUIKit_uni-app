<template>
  <view class="container">
    <view class="login-section">
      <text class="guide-text">请输入 userID 用于登录</text>
      <input class="input-box" v-model="userID" placeholder="请输入您的用户ID" placeholder-style="color:#BBBBBB;" />
      <button class="login-btn" @click="handleLogin">登录</button>
    </view>
  </view>
</template>

<script>
import { genTestUserSig, SDKAPPID as SDKAppID } from '../../debug/GenerateTestUserSig-es.js';
import { useLoginState } from '../../TUIKit';
const { login } = useLoginState();

export default {
  data() {
    return {
      userID: ''
    }
  },
  methods: {
    async handleLogin() {
      const { userSig } = genTestUserSig({ userID: this.userID });

      await login({
        userId: this.userID,
        userSig,
        sdkAppId: SDKAppID,
      });

      wx.$globalCallPagePath = 'TUIKit/components/CallView/CallView';

      uni.switchTab({
        url: '/pages/conversation/conversation',
      });
    }
  }
}
</script>

<style scoped>
.container {
  padding: 40px;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.login-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 100px;
}

.title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 40px;
  color: #333;
}

.input-box,
.target-input {
  width: 80%;
  height: 50px;
  border: 1px solid #DDDDDD;
  border-radius: 8px;
  padding: 0 15px;
  margin-bottom: 20px;
  font-size: 16px;
}

.login-btn,
.start-chat-btn {
  width: 80%;
  height: 50px;
  background-color: #006EFF;
  color: white;
  border-radius: 8px;
  font-size: 16px;
  line-height: 50px;
}

.chat-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 100px;
}

.guide-text {
  font-weight: 600;
  font-size: 20px;
  color: rgb(47, 46, 46);
  margin-bottom: 40px;
}

.target-input {
  transition: border-color 0.3s;
}
</style>