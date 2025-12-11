<template>
  <div class="conference-page">
    <!-- Форма входа в конференцию -->
    <div v-if="!isInConference" class="join-form">
      <h2>Присоединиться к конференции</h2>
      <div class="form-group">
        <label>Имя комнаты:</label>
        <input v-model="roomName" type="text" placeholder="my-conference-room" />
      </div>
      <div class="form-group">
        <label>Ваше имя:</label>
        <input v-model="userName" type="text" placeholder="Имя участника" />
      </div>
      <button @click="joinConference" class="join-btn">
        Присоединиться
      </button>
    </div>

    <!-- Компонент конференции -->
    <JitsiMeeting
      v-else
      ref="jitsiMeeting"
      :room-name="roomName"
      :user="user"
      :config="jitsiConfig"
      @conference-joined="onConferenceJoined"
      @conference-ended="onConferenceEnded"
      @error="onError"
    />

    <!-- Ошибка -->
    <div v-if="error" class="error-message">
      {{ error }}
      <button @click="error = ''">OK</button>
    </div>
  </div>
</template>

<script>
import JitsiMeeting from '@/app/gc/newjms/JitsiMeeting.vue';

export default {
  name: 'ConferencePage',
  components: {
    JitsiMeeting
  },

  data() {
    return {
      isInConference: false,
      roomName: '',
      userName: '',
      error: '',
      user: {
        name: '',
        email: ''
      },
      jitsiConfig: {
        // Дополнительная конфигурация
        constraints: {
          video: {
            height: {
              ideal: 720,
              max: 1080,
              min: 240
            },
            width: {
              ideal: 1280,
              max: 1920,
              min: 320
            },
            frameRate: {
              max: 30,
              min: 10
            }
          }
        },
        resolution: 720,
        disableAudioLevels: false,
        enableNoisyMicDetection: true,
        enableTalkWhileMuted: false,
        useTurnUdp: false
      }
    };
  },

  methods: {
    joinConference() {
      if (!this.roomName.trim()) {
        this.error = 'Введите название комнаты';
        return;
      }

      if (!this.userName.trim()) {
        this.error = 'Введите ваше имя';
        return;
      }

      this.user.name = this.userName;
      this.isInConference = true;
    },

    onConferenceJoined() {
      console.log('Успешно присоединились к конференции');
    },

    onConferenceEnded() {
      this.isInConference = false;
      this.roomName = '';
    },

    onError(error) {
      console.error('Ошибка конференции:', error);
      this.error = 'Произошла ошибка подключения к конференции';
      this.isInConference = false;
    }
  }
};
</script>

<style scoped>
.conference-page {
  width: 100%;
  height: 100vh;
}

.join-form {
  max-width: 400px;
  margin: 100px auto;
  padding: 30px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-group input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 16px;
}

.join-btn {
  width: 100%;
  padding: 12px;
  background: #0056cc;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background 0.3s;
}

.join-btn:hover {
  background: #007bff;
}

.error-message {
  position: fixed;
  top: 20px;
  right: 20px;
  background: #ff4444;
  color: white;
  padding: 15px;
  border-radius: 5px;
  z-index: 9999;
}
</style>
