<template>
    <div class="chat-room">
      <div class="chat-header">
        <img class="profile-picture" :src="profilePicture" alt="Profile Picture">
        <h2 class="user-name">{{ userName }}</h2>
      </div>
      <MessageContainer :messages="messages"/>
      <div class="input-container">
        <button @click="showFileInput" class="attach-button">사진 첨부</button>
        <input type="file" accept="image/*" ref="fileInput" @change="handleImageSelect" capture="environment" class="file-input">
        <div class="message-input-container">
          <img v-if="previewImage" :src="previewImage" alt="Selected Image" class="inside-preview-image" @click="clearPreviewImage">
          <input v-model="newMessage" @keyup.enter="sendMessage" placeholder="메시지를 입력하세요" class="message-input">
        </div>
        <button @click="sendMessage" class="send-button">전송</button>
      </div>
    </div>
  </template>
  
  <script>
  import MessageContainer from './MessageContainer.vue';
  
  export default {
    name: 'Chat',
    components: {
      MessageContainer
    },
    props: {
      profilePicture: {
        type: String,
        required: true
      },
      userName: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        messages: [],
        newMessage: '',
        previewImage: null
      };
    },
    methods: {
      sendMessage() {
        if (this.previewImage) {
          this.messages.push({ image: this.previewImage, fromMe: true, type: 'image' });
          this.previewImage = null;
        } else if (this.newMessage.trim() !== '') {
          this.messages.push({ text: this.newMessage, fromMe: true, type: 'text' });
          this.newMessage = '';
        }
      },
      handleImageSelect(event) {
        const file = event.target.files[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = () => {
            this.previewImage = reader.result;
          };
          reader.readAsDataURL(file);
        }
      },
      showFileInput() {
        this.$refs.fileInput.click();
      },
      clearPreviewImage() {
        this.previewImage = null;
      }
    }
  };
  </script>

<style scoped>
/* 채팅방 스타일 */
.chat-room {
    max-width: 500px;
    height: 700px;
    /* 채팅방 높이 고정 */
    display: flex;
    flex-direction: column;
    /* 내부 요소를 세로로 배열 */
    margin: auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
}

/* 채팅 헤더 스타일 */
.chat-header {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
}

.profile-picture {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    margin-right: 10px;
}

.user-name {
    font-size: 16px;
    margin: 0;
}

/* 메시지 컨테이너 스타일 */
.message-container {
    flex-grow: 1;
    /* 사용 가능한 모든 공간을 채움 */
    overflow-y: auto;
    padding: 0 10px;
    /* 내부 여백 조정 */
}

/* 메시지 스타일 */
.message {
    margin-bottom: 10px;
}

.my-message {
    background-color: #dcf8c6;
    padding: 10px;
    border-radius: 10px;
    max-width: 70%;
    word-wrap: break-word;
    align-self: flex-end;
    margin-left: auto;
    margin-top: 10px
}

.other-message {
    background-color: #e6e6e6;
    padding: 10px;
    border-radius: 10px;
    max-width: 70%;
    word-wrap: break-word;
}

/* 입력 컨테이너 스타일 */
.input-container {
    display: flex;
    align-items: center;
    margin-top: auto;
    /* 하단에 고정 */
    margin-top: 15px;
}

.attach-button {
    padding: 10px 15px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin-right: 10px;
}

.file-input {
    display: none;
    /* 파일 입력 창 숨기기 */
}

.message-input {
    flex: 1;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-right: 10px;
}

.send-button {
    padding: 10px 20px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

.send-button:hover {
    background-color: #0056b3;
}

/* 이미지 프리뷰 스타일 */
.image-preview {
    margin-top: 10px;
    text-align: center;
}

.selected-image {
    max-width: 100px;
    /* 이미지 미리보기 최대 너비 설정 */
    cursor: pointer;
}

.sent-image {
    max-width: 100%;
    /* 이미지가 채팅방 너비를 넘지 않도록 함 */
    height: auto;
    /* 이미지 높이 자동 조정 */
    display: block;
    /* 이미지를 블록 요소로 처리 */
    margin: 0 auto;
}

.selected-image-preview {
    max-width: 50px;
    /* 미리보기 이미지 크기 조정 */
    max-height: 50px;
    /* 높이 제한 추가 */
    margin-right: 10px;
    /* 메시지 입력창과의 간격 */
    cursor: pointer;
    /* 클릭 가능 표시 */
}

.message-input-container {
  flex: 1; /* 컨테이너가 가능한 모든 공간을 차지하도록 함 */
  display: flex; /* 인풋 박스와 이미지 미리보기를 옆으로 배열 */
  align-items: center; /* 중앙 정렬 */
  position: relative; /* 내부의 이미지 위치 조정을 위함 */
}

.inside-preview-image {
  position: absolute; /* 컨테이너 내에서 절대 위치 사용 */
  left: 10px; /* 왼쪽에서부터의 간격 */
  max-width: 30px; /* 이미지 크기 조정 */
  max-height: 30px; /* 이미지 높이 조정 */
}

.message-input {
  flex: 1; /* 가능한 모든 공간을 차지 */
  padding-left: 45px; /* 이미지 미리보기와 텍스트 사이의 공간 확보 */
  /* 기존 스타일 유지 */
}
</style>