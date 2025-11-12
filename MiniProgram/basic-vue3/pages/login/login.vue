<template>
  <view class="container">
    <view class="login-section" v-if="!isLoggedIn">
      <text class="guide-text">请输入 userID 用于登录</text>
      <input 
        class="input-box" 
        v-model="userID" 
        placeholder="请输入您的用户ID" 
        placeholder-style="color:#BBBBBB;"
      />
      <button class="login-btn" @click="handleLogin">登录</button>
    </view>

    <view class="chat-section" v-else>
      <text class="guide-text">请输入对方用户ID开始聊天</text>
      <input 
        class="target-input" 
        v-model="targetUserID" 
        :style="{ borderColor: targetUserID ? '#07C160' : '#DDDDDD' }"
        placeholder="输入对方用户ID"
        @input="onTargetInput"
      />
      <button class="start-chat-btn" style="background-color: #07C160;"  @click="startChat">开始聊天</button>
    </view>
  </view>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { useLoginState } from '../../TUIKit/states/LoginState';
import { useConversationListState } from '../../TUIKit/states/ConversationListState/ConversationListState';
import { genTestUserSig, SDKAPPID as SDKAppID } from '../../debug/GenerateTestUserSig-es.js';

const userID = ref('');
const targetUserID = ref('');
const isLoggedIn = ref(false);

const { login } = useLoginState();
const { setActiveConversation } = useConversationListState();

const handleLogin = async () => {
  const { userSig } = genTestUserSig({ userID: userID.value });
  
   await login({
    userId: userID.value,
    userSig,
    sdkAppId: SDKAppID,
  });
  wx.$globalCallPagePath = 'TUIKit/components/CallView/CallView';
  isLoggedIn.value = true;
};


const startChat = () => {
  if (!targetUserID.value) return;
  
  const conversationID = `C2C${targetUserID.value}`;
  setActiveConversation(conversationID);
  
  uni.navigateTo({
    url: '/pages/chat/chat',
  });
};
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

.input-box, .target-input {
  width: 80%;
  height: 50px;
  border: 1px solid #DDDDDD;
  border-radius: 8px;
  padding: 0 15px;
  margin-bottom: 20px;
  font-size: 16px;
}

.login-btn, .start-chat-btn {
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