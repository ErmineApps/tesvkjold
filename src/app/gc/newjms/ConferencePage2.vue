<template>
  <div class="conference-page">
    <!-- Форма входа -->
    <div v-if="!isInConference" class="join-form">
      <div class="form-card">
        <h2>Создать/Присоединиться к встрече</h2>

        <div class="form-group">
          <label>Имя комнаты:</label>
          <input
            v-model="roomName"
            type="text"
            placeholder="my-meeting-room"
            @keyup.enter="joinConference"
          />
        </div>

        <div class="form-group">
          <label>Ваше имя:</label>
          <input
            v-model="userName"
            type="text"
            placeholder="Ваше имя"
            @keyup.enter="joinConference"
          />
        </div>

        <!-- Настройки устройств -->
        <div class="device-settings">
          <div class="form-group">
            <label>Камера:</label>
            <select v-model="selectedCamera">
              <option value="">По умолчанию</option>
              <option
                v-for="device in videoDevices"
                :key="device.deviceId"
                :value="device.deviceId"
              >
                {{ device.label || `Камера ${device.deviceId.slice(0, 8)}` }}
              </option>
            </select>
          </div>

          <div class="form-group">
            <label>Микрофон:</label>
            <select v-model="selectedMicrophone">
              <option value="">По умолчанию</option>
              <option
                v-for="device in audioInputDevices"
                :key="device.deviceId"
                :value="device.deviceId"
              >
                {{ device.label || `Микрофон ${device.deviceId.slice(0, 8)}` }}
              </option>
            </select>
          </div>
        </div>

        <!-- Быстрый доступ к комнатам -->
        <div class="quick-rooms" v-if="recentRooms.length">
          <h3>Недавние комнаты:</h3>
          <div class="room-list">
            <button
              v-for="room in recentRooms"
              :key="room"
              @click="selectRoom(room)"
              class="room-btn"
            >
              {{ room }}
            </button>
          </div>
        </div>

        <div class="form-actions">
          <button @click="joinConference" class="btn-primary">
            Присоединиться
          </button>
          <button @click="createNewRoom" class="btn-secondary">
            Создать новую комнату
          </button>
        </div>
      </div>
    </div>

    <!-- Видеоконференция -->
    <VideoConference
      v-else
      ref="conference"
      :room-name="roomName"
      :user-name="userName"
      @hangup="leaveConference"
      @error="handleError"
    />

    <!-- Уведомление об ошибке -->
    <div v-if="error" class="error-modal">
      <div class="error-content">
        <h3>Ошибка</h3>
        <p>{{ error }}</p>
        <button @click="error = ''" class="btn-primary">OK</button>
      </div>
    </div>
  </div>
</template>

<script>
import VideoConference from '@/app/gc/newjms/VideoConference.vue';

export default {
  name: 'ConferencePage',
  components: {
    VideoConference
  },

  data() {
    return {
      isInConference: false,
      roomName: '',
      userName: '',
      error: '',

      // Устройства
      videoDevices: [],
      audioInputDevices: [],
      selectedCamera: '',
      selectedMicrophone: '',

      // Недавние комнаты
      recentRooms: []
    };
  },

  async mounted() {
    await this.loadMediaDevices();
    this.loadRecentRooms();

    // Проверяем параметры URL
    const urlParams = new URLSearchParams(window.location.search);
    const room = urlParams.get('room');
    const name = urlParams.get('name');

    if (room) {
      this.roomName = room;
    }
    if (name) {
      this.userName = name;
    }

    if (room && name) {
      this.joinConference();
    }
  },

  methods: {
    async loadMediaDevices() {
      try {
        const devices = await navigator.mediaDevices.enumerateDevices();

        this.videoDevices = devices.filter(device =>
          device.kind === 'videoinput'
        );

        this.audioInputDevices = devices.filter(device =>
          device.kind === 'audioinput'
        );

      } catch (error) {
        console.error('Ошибка загрузки устройств:', error);
      }
    },

    loadRecentRooms() {
      const rooms = localStorage.getItem('recentRooms');
      if (rooms) {
        this.recentRooms = JSON.parse(rooms);
      }
    },

    saveRecentRoom(room) {
      if (!this.recentRooms.includes(room)) {
        this.recentRooms.unshift(room);
        if (this.recentRooms.length > 5) {
          this.recentRooms.pop();
        }
        localStorage.setItem('recentRooms', JSON.stringify(this.recentRooms));
      }
    },

    selectRoom(room) {
      this.roomName = room;
    },

    createNewRoom() {
      // Генерируем уникальное имя комнаты
      const timestamp = Date.now().toString(36);
      const random = Math.random().toString(36).substring(2, 8);
      this.roomName = `room-${timestamp}-${random}`;

      if (!this.userName) {
        this.userName = 'Участник ' + Math.floor(Math.random() * 1000);
      }
    },

    async joinConference() {
      if (!this.roomName.trim()) {
        this.error = 'Введите название комнаты';
        return;
      }

      if (!this.userName.trim()) {
        this.error = 'Введите ваше имя';
        return;
      }

      // Сохраняем комнату в историю
      this.saveRecentRoom(this.roomName);

      this.isInConference = true;
      this.error = '';

      // Обновляем URL
      const url = new URL(window.location);
      url.searchParams.set('room', this.roomName);
      url.searchParams.set('name', this.userName);
      window.history.pushState({}, '', url);
    },

    leaveConference() {
      this.isInConference = false;

      // Очищаем URL параметры
      const url = new URL(window.location);
      url.searchParams.delete('room');
      url.searchParams.delete('name');
      window.history.pushState({}, '', url);
    },

    handleError(error) {
      console.error('Ошибка конференции:', error);
      this.error = error.message || 'Произошла ошибка во время конференции';
      this.isInConference = false;
    }
  }
};
</script>

<style scoped>
.conference-page {
  width: 100%;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.join-form {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
}

.form-card {
  background: white;
  border-radius: 15px;
  padding: 40px;
  width: 100%;
  max-width: 500px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.form-card h2 {
  margin: 0 0 30px 0;
  color: #333;
  text-align: center;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #666;
  font-weight: 500;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.3s;
}

.form-group input:focus,
.form-group select:focus {
  outline: none;
  border-color: #667eea;
}

.device-settings {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin: 20px 0;
}

.quick-rooms {
  margin: 25px 0;
  padding-top: 25px;
  border-top: 1px solid #eee;
}

.quick-rooms h3 {
  margin: 0 0 15px 0;
  font-size: 16px;
  color: #666;
}

.room-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.room-btn {
  padding: 8px 15px;
  background: #f0f5ff;
  border: 2px solid #e1e5e9;
  border-radius: 6px;
  color: #667eea;
  cursor: pointer;
  transition: all 0.3s;
  font-size: 14px;
}

.room-btn:hover {
  background: #667eea;
  color: white;
  border-color: #667eea;
}

.form-actions {
  display: flex;
  gap: 15px;
  margin-top: 30px;
}

.btn-primary {
  flex: 1;
  padding: 15px;
  background: #667eea;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.3s;
}

.btn-primary:hover {
  background: #5a67d8;
}

.btn-secondary {
  flex: 1;
  padding: 15px;
  background: white;
  color: #667eea;
  border: 2px solid #667eea;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
}

.btn-secondary:hover {
  background: #f0f5ff;
}

.error-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2000;
}

.error-content {
  background: white;
  padding: 30px;
  border-radius: 10px;
  max-width: 400px;
  width: 90%;
  text-align: center;
}

.error-content h3 {
  color: #ff4444;
  margin: 0 0 15px 0;
}

.error-content p {
  color: #666;
  margin-bottom: 20px;
}

@media (max-width: 768px) {
  .form-card {
    padding: 20px;
  }

  .device-settings {
    grid-template-columns: 1fr;
  }

  .form-actions {
    flex-direction: column;
  }
}
</style>
