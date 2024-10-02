<template>
  <div class="chat-wrapper">
    <div class="chat-container">
      <div class="chat-message" v-for="(message, index) in messages" :key="index">
        <div :class="{'user-message': message.sender === 'user', 'bot-message': message.sender === 'bot'}">
          <span class="message-content">{{ message.text }}</span>
          <!-- 링크 카드가 있을 경우 출력 -->
          <div v-if="message.links" class="links-container">
            <div v-for="link in message.links" :key="link.url" class="link-card">
              <h5>{{ link.title }}</h5> <!-- Title을 출력 -->
              <p>{{ link.description }}</p> <!-- Description을 출력 -->
              <a :href="link.url" target="_blank" class="view-more-btn">View More</a>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="chat-input-container">
      <div class="chat-input-wrapper">
        <button class="icon-btn" @click="triggerFileUpload">
          <i class="fas fa-plus"></i>
        </button>
        <input type="file" @change="handleFileUpload" style="display: none;" ref="fileInput" />
        <textarea
          v-model="userInput"
          placeholder="Type your message here..."
          @input="adjustTextareaHeight"
          @keyup.enter="sendMessage"
          ref="textarea"
        ></textarea>
        <button class="icon-btn" @click="triggerVoiceSearch">
          <i class="fas fa-microphone"></i>
        </button>
        <button class="send-btn" @click="sendMessage">
          <i class="fas fa-paper-plane"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import exampleData from '../data/data.js';

export default {
  data() {
    return {
      userInput: '',
      messages: []
    };
  },
  methods: {
    sendMessage() {
      if (this.userInput.trim()) {
        this.messages.push({ sender: 'user', text: this.userInput });
        this.getBotResponse(this.userInput);
        this.userInput = '';
        this.resetTextareaHeight(); // 텍스트 입력창 높이 초기화
      }
    },
    getBotResponse(query) {
      const response = exampleData.find(item => query.toLowerCase().includes(item.keyword.toLowerCase()));
      if (response) {
        this.messages.push({ sender: 'bot', text: response.response, links: response.links });
      } else {
        this.messages.push({ sender: 'bot', text: 'No relevant information found.' });
      }
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        this.messages.push({ sender: 'user', text: `File uploaded: ${file.name}` });
        this.getBotResponse(`file:${file.name}`);
      }
    },
    triggerFileUpload() {
      this.$refs.fileInput.click();
    },
    triggerVoiceSearch() {
      this.messages.push({ sender: 'bot', text: 'Voice search triggered (not implemented).' });
    },
    adjustTextareaHeight() {
      const textarea = this.$refs.textarea;
      textarea.style.height = '40px'; // 입력창의 기본 높이를 유지
      if (textarea.scrollHeight > 40) { // 높이가 기본값보다 커질 경우에만 늘림
        textarea.style.height = `${textarea.scrollHeight}px`; // 입력 내용에 따라 높이 조정
      }
    },
    resetTextareaHeight() {
      const textarea = this.$refs.textarea;
      textarea.style.height = '40px'; // 메시지 전송 후 높이를 다시 기본값으로 리셋
    }
  }
};
</script>

<style>
/* 기본적인 스타일 설정 */
body, html {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}

.chat-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100vw;
  background-color: #f0f0f0;
}

.chat-container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  height: calc(100vh - 100px);
  width: 100%;
  max-width: 800px;
  overflow-y: auto;
  background-color: #ffffff;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 20px;
}

.chat-message {
  margin-bottom: 10px;
  display: flex;
  align-items: flex-start;
}

.user-message {
  text-align: right;
  background-color: #d6cabc;
  padding: 12px;
  border-radius: 15px;
  display: inline-block;
  max-width: 70%;
  word-wrap: break-word;
  margin-left: auto;
  align-self: flex-end; /* 사용자 메시지 오른쪽 정렬 */
}

.bot-message {
  text-align: left;
  background-color: #8b0029;
  color: white;
  padding: 12px;
  border-radius: 15px;
  display: inline-block;
  max-width: 70%;
  word-wrap: break-word;
  margin-right: auto;
  align-self: flex-start; /* 챗봇 메시지 왼쪽 정렬 */
}

.message-content {
  display: block;
  margin-bottom: 5px;
}

.chat-input-container {
  background-color: #f0f0f0;
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 10px 20px;
}

.chat-input-wrapper {
  display: flex;
  align-items: center;
  width: 100%;
  max-width: 800px;
  background-color: #ffffff;
  border: 1px solid #ddd;
  border-radius: 30px;
  padding: 5px;
}

.chat-input-wrapper textarea {
  flex: 1;
  border: none;
  padding: 10px;
  resize: none;
  outline: none;
  border-radius: 30px;
  height: 40px; /* 기본 높이 설정 */
  max-height: 150px; /* 최대 높이 설정 */
  overflow-y: auto; /* 내용이 많아지면 스크롤 가능 */
  transition: height 0.2s ease-in-out; /* 높이 변화에 애니메이션 추가 */
}

.icon-btn {
  background-color: transparent;
  border: none;
  color: #8b0029;
  font-size: 20px;
  margin-right: 5px;
}

.send-btn {
  background-color: #8b0029;
  border: none;
  color: white;
  padding: 10px 15px;
  border-radius: 50%;
  margin-left: 5px;
  cursor: pointer;
}

.send-btn:hover {
  background-color: #a30032;
}
</style>
