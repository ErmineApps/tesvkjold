<template>
  <div class="video-conference">
    <!-- Локальное видео -->
    <div class="video-grid">
      <div class="video-container local-video" :class="{ 'video-muted': !localVideoEnabled }">
        <video
          ref="localVideo"
          autoplay
          muted
          playsinline
          class="video-element"
        ></video>
        <div class="video-overlay">
          <span class="participant-name">{{ userName || 'Вы' }}</span>
          <div class="video-status">
            <span v-if="!localVideoEnabled" class="status-icon">📷</span>
            <span v-if="!localAudioEnabled" class="status-icon">🎤</span>
          </div>
        </div>
      </div>

      <!-- Удаленные видео -->
      <div
        v-for="participant in remoteParticipants"
        :key="participant.id"
        class="video-container remote-video"
        :class="{ 'video-muted': !participant.videoEnabled }"
      >
        <video
          :ref="`remoteVideo-${participant.id}`"
          autoplay
          playsinline
          class="video-element"
        ></video>
        <div class="video-overlay">
          <span class="participant-name">{{ participant.name || `Участник ${participant.id}` }}</span>
          <div class="video-status">
            <span v-if="!participant.videoEnabled" class="status-icon">📷</span>
            <span v-if="!participant.audioEnabled" class="status-icon">🎤</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Панель управления -->
    <div class="control-panel">
      <button
        @click="toggleAudio"
        class="control-btn"
        :class="{ 'btn-active': localAudioEnabled }"
      >
        {{ localAudioEnabled ? '🔊' : '🔇' }}
        <span class="btn-label">{{ localAudioEnabled ? 'Выкл. звук' : 'Вкл. звук' }}</span>
      </button>

      <button
        @click="toggleVideo"
        class="control-btn"
        :class="{ 'btn-active': localVideoEnabled }"
      >
        {{ localVideoEnabled ? '📹' : '📷' }}
        <span class="btn-label">{{ localVideoEnabled ? 'Выкл. видео' : 'Вкл. видео' }}</span>
      </button>

      <button
        @click="toggleScreenShare"
        class="control-btn"
        :class="{ 'btn-active': isScreenSharing }"
      >
        {{ isScreenSharing ? '🖥️' : '🖥️' }}
        <span class="btn-label">{{ isScreenSharing ? 'Остановить' : 'Экран' }}</span>
      </button>

      <button
        @click="toggleParticipants"
        class="control-btn"
      >
        👥
        <span class="btn-label">Участники ({{ remoteParticipants.length + 1 }})</span>
      </button>

      <button
        @click="hangUp"
        class="control-btn btn-hangup"
      >
        📞
        <span class="btn-label">Завершить</span>
      </button>
    </div>

    <!-- Список участников -->
    <div v-if="showParticipants" class="participants-list">
      <h3>Участники</h3>
      <ul>
        <li class="participant-item">
          <span class="participant-name">{{ userName || 'Вы' }}</span>
          <span class="participant-status">
            <span v-if="!localAudioEnabled" title="Микрофон выключен">🔇</span>
            <span v-if="!localVideoEnabled" title="Камера выключена">📷</span>
          </span>
        </li>
        <li
          v-for="participant in remoteParticipants"
          :key="participant.id"
          class="participant-item"
        >
          <span class="participant-name">{{ participant.name || `Участник ${participant.id}` }}</span>
          <span class="participant-status">
            <span v-if="!participant.audioEnabled" title="Микрофон выключен">🔇</span>
            <span v-if="!participant.videoEnabled" title="Камера выключена">📷</span>
          </span>
        </li>
      </ul>
    </div>

    <!-- Уведомления -->
    <div v-if="notification" class="notification">
      {{ notification }}
    </div>
  </div>
</template>

<script>
import JitsiService from '@/assets/ap_kurento/JitsiService';

export default {
  name: 'VideoConference',
  props: {
    roomName: {
      type: String,
      required: true
    },
    userName: {
      type: String,
      default: 'Участник'
    },
    autoJoin: {
      type: Boolean,
      default: true
    }
  },

  data() {
    return {
      localVideoEnabled: true,
      localAudioEnabled: true,
      isScreenSharing: false,
      showParticipants: false,
      remoteParticipants: [],
      notification: '',
      mediaDevices: [],
      localStream: null,
      notificationTimeout: null
    };
  },

  async mounted() {
    if (this.autoJoin) {
      await this.joinConference();
    }
  },

  beforeDestroy() {
    this.leaveConference();
  },

  methods: {
    async joinConference() {
      try {
        this.showNotification('Подключение к конференции...');

        // Создаем локальные треки (камера и микрофон)
        const localTracks = await JitsiService.createLocalTracks();

        // Подключаем локальное видео
        const videoTrack = localTracks.find(t => t.isVideoTrack());
        if (videoTrack && this.$refs.localVideo) {
          videoTrack.attach(this.$refs.localVideo);
        }

        // Подключаемся к комнате
        await JitsiService.connect(this.roomName);

        // Подписываемся на события
        this.setupEventListeners();

        // Устанавливаем имя пользователя
        JitsiService.setDisplayName(this.userName);

        this.showNotification('Подключено к конференции');

      } catch (error) {
        console.error('Ошибка подключения:', error);
        this.showNotification('Ошибка подключения: ' + error.message, true);
        this.$emit('error', error);
      }
    },

    setupEventListeners() {
      // Удаленный трек добавлен
      JitsiService.on('remote-track-added', ({ participantId, track }) => {
        this.handleRemoteTrack(participantId, track);
      });

      // Удаленный трек удален
      JitsiService.on('remote-track-removed', (participantId) => {
        this.removeRemoteParticipant(participantId);
      });

      // Участник присоединился
      JitsiService.on('participant-joined', ({ id, user }) => {
        this.addRemoteParticipant(id, user);
        this.showNotification(`Участник ${user?.displayName || id} присоединился`);
      });

      // Участник вышел
      JitsiService.on('participant-left', (id) => {
        this.removeRemoteParticipant(id);
        this.showNotification('Участник вышел');
      });

      // Изменение статуса треков
      JitsiService.on('track-mute-changed', (track) => {
        this.updateParticipantTrackStatus(track);
      });

      // Демонстрация экрана
      JitsiService.on('screen-sharing-started', () => {
        this.isScreenSharing = true;
      });

      JitsiService.on('screen-sharing-stopped', () => {
        this.isScreenSharing = false;
      });

      // События подключения
      JitsiService.on('connection-established', () => {
        this.showNotification('Соединение установлено');
      });

      JitsiService.on('connection-failed', (error) => {
        this.showNotification('Ошибка соединения', true);
      });
    },

    handleRemoteTrack(participantId, track) {
      const videoElement = this.$refs[`remoteVideo-${participantId}`];
      if (videoElement && videoElement[0]) {
        track.attach(videoElement[0]);
      }

      // Обновляем информацию об участнике
      const participantIndex = this.remoteParticipants.findIndex(p => p.id === participantId);
      if (participantIndex === -1) {
        this.remoteParticipants.push({
          id: participantId,
          videoEnabled: !track.isMuted() && track.isVideoTrack(),
          audioEnabled: !track.isMuted() && track.isAudioTrack(),
          name: track.getParticipantId()
        });
      } else {
        const participant = this.remoteParticipants[participantIndex];
        if (track.isVideoTrack()) {
          participant.videoEnabled = !track.isMuted();
        } else if (track.isAudioTrack()) {
          participant.audioEnabled = !track.isMuted();
        }
      }
    },

    addRemoteParticipant(id, user) {
      if (!this.remoteParticipants.some(p => p.id === id)) {
        this.remoteParticipants.push({
          id,
          name: user?.displayName || id,
          videoEnabled: true,
          audioEnabled: true
        });
      }
    },

    removeRemoteParticipant(id) {
      this.remoteParticipants = this.remoteParticipants.filter(p => p.id !== id);
    },

    updateParticipantTrackStatus(track) {
      if (!track.isLocal()) {
        const participantId = track.getParticipantId();
        const participant = this.remoteParticipants.find(p => p.id === participantId);
        if (participant) {
          if (track.isVideoTrack()) {
            participant.videoEnabled = !track.isMuted();
          } else if (track.isAudioTrack()) {
            participant.audioEnabled = !track.isMuted();
          }
        }
      }
    },

    // Управление медиа
    toggleAudio() {
      this.localAudioEnabled = !this.localAudioEnabled;
      JitsiService.muteAudio(!this.localAudioEnabled);
    },

    toggleVideo() {
      this.localVideoEnabled = !this.localVideoEnabled;
      JitsiService.muteVideo(!this.localVideoEnabled);
    },

    async toggleScreenShare() {
      try {
        if (this.isScreenSharing) {
          JitsiService.stopScreenSharing();
          this.isScreenSharing = false;
        } else {
          await JitsiService.shareScreen();
          this.isScreenSharing = true;
          this.showNotification('Демонстрация экрана начата');
        }
      } catch (error) {
        console.error('Ошибка демонстрации экрана:', error);
        this.showNotification('Ошибка демонстрации экрана', true);
      }
    },

    toggleParticipants() {
      this.showParticipants = !this.showParticipants;
    },

    hangUp() {
      this.leaveConference();
      this.$emit('hangup');
    },

    leaveConference() {
      JitsiService.leaveConference();
      this.remoteParticipants = [];
      this.isScreenSharing = false;
    },

    showNotification(message, isError = false) {
      this.notification = message;

      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout);
      }

      this.notificationTimeout = setTimeout(() => {
        this.notification = '';
      }, 3000);

      if (isError) {
        console.error(message);
      } else {
        console.log(message);
      }
    },

    // Дополнительные методы управления
    async switchCamera(deviceId) {
      await JitsiService.switchCamera(deviceId);
    },

    async switchAudioInput(deviceId) {
      await JitsiService.switchAudioInput(deviceId);
    },

    async switchAudioOutput(deviceId) {
      await JitsiService.switchAudioOutput(deviceId);
    },

    async getMediaDevices() {
      try {
        const devices = await navigator.mediaDevices.enumerateDevices();
        this.mediaDevices = devices;
        return devices;
      } catch (error) {
        console.error('Ошибка получения устройств:', error);
        return [];
      }
    }
  }
};
</script>

<style scoped>
.video-conference {
  position: relative;
  width: 100%;
  height: 100vh;
  background: #1a1a1a;
  overflow: hidden;
}

.video-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 10px;
  padding: 10px;
  height: calc(100vh - 80px);
  overflow-y: auto;
}

.video-container {
  position: relative;
  background: #000;
  border-radius: 8px;
  overflow: hidden;
  aspect-ratio: 16/9;
}

.video-element {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.video-container.video-muted::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: #333;
  z-index: 1;
}

.video-overlay {
  position: absolute;
  bottom: 10px;
  left: 10px;
  right: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
  z-index: 2;
}

.participant-name {
  background: rgba(0, 0, 0, 0.7);
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 14px;
}

.video-status {
  display: flex;
  gap: 5px;
}

.status-icon {
  background: rgba(0, 0, 0, 0.7);
  padding: 4px;
  border-radius: 4px;
  font-size: 12px;
}

.control-panel {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 10px;
  background: rgba(0, 0, 0, 0.8);
  padding: 10px 20px;
  border-radius: 50px;
  z-index: 1000;
}

.control-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  background: #333;
  border: none;
  color: white;
  padding: 10px 15px;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.3s;
  min-width: 70px;
}

.control-btn:hover {
  background: #444;
  transform: translateY(-2px);
}

.control-btn.btn-active {
  background: #0056cc;
}

.control-btn.btn-hangup {
  background: #ff4444;
}

.control-btn.btn-hangup:hover {
  background: #ff6666;
}

.btn-label {
  font-size: 11px;
  opacity: 0.9;
}

.participants-list {
  position: fixed;
  top: 20px;
  right: 20px;
  background: rgba(0, 0, 0, 0.9);
  color: white;
  padding: 15px;
  border-radius: 8px;
  min-width: 200px;
  max-height: 300px;
  overflow-y: auto;
  z-index: 1001;
}

.participants-list h3 {
  margin: 0 0 10px 0;
  font-size: 16px;
}

.participant-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid #333;
}

.participant-item:last-child {
  border-bottom: none;
}

.participant-status {
  display: flex;
  gap: 5px;
}

.notification {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.9);
  color: white;
  padding: 10px 20px;
  border-radius: 20px;
  z-index: 1002;
  animation: slideDown 0.3s ease;
}

@keyframes slideDown {
  from {
    transform: translate(-50%, -100%);
    opacity: 0;
  }
  to {
    transform: translate(-50%, 0);
    opacity: 1;
  }
}

@media (max-width: 768px) {
  .video-grid {
    grid-template-columns: 1fr;
  }

  .control-panel {
    flex-wrap: wrap;
    justify-content: center;
    width: 90%;
    border-radius: 20px;
  }

  .control-btn {
    min-width: 60px;
    padding: 8px 12px;
  }
}
</style>
