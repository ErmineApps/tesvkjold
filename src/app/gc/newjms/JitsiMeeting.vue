<template>
  <div class="jitsi-container">
    <div ref="jitsiContainer" class="jitsi-meet-container"></div>

    <!-- Контролы управления -->
    <div v-if="showControls" class="meeting-controls">
      <button @click="toggleAudio" class="control-btn">
        {{ isAudioMuted ? 'Включить звук' : 'Выключить звук' }}
      </button>
      <button @click="toggleVideo" class="control-btn">
        {{ isVideoMuted ? 'Включить видео' : 'Выключить видео' }}
      </button>
      <button @click="toggleScreenShare" class="control-btn">
        {{ isSharingScreen ? 'Остановить демонстрацию' : 'Демонстрация экрана' }}
      </button>
      <button @click="hangUp" class="control-btn hangup">
        Завершить
      </button>
    </div>

    <!-- Состояние подключения -->
    <div v-if="connectionStatus" class="connection-status">
      {{ connectionStatus }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'JitsiMeeting',
  props: {
    roomName: {
      type: String,
      required: true
    },
    user: {
      type: Object,
      default: () => ({
        name: 'Участник',
        email: ''
      })
    },
    config: {
      type: Object,
      default: () => ({})
    },
    interfaceConfig: {
      type: Object,
      default: () => ({})
    },
    showControls: {
      type: Boolean,
      default: true
    }
  },

  data() {
    return {
      api: null,
      isAudioMuted: false,
      isVideoMuted: false,
      isSharingScreen: false,
      connectionStatus: '',
      participants: []
    };
  },

  mounted() {
    this.initializeJitsi();
  },

  beforeDestroy() {
    this.disposeJitsi();
  },

  methods: {
    async initializeJitsi() {
      try {
        // Ожидаем загрузку библиотеки
        if (typeof JitsiMeetExternalAPI === 'undefined') {
          await this.loadJitsiScript();
        }

        this.connectionStatus = 'Подключение...';

        // Базовая конфигурация
        const defaultConfig = {
          hosts: {
            domain: 'localhost:8443',
            muc: 'conference.localhost:8443'
          },
          serviceUrl: `wss://localhost:8443/xmpp-websocket`,
          bosh: `https://localhost:8443/http-bind`,
          websocket: 'wss://localhost:8443/xmpp-websocket',
          clientNode: 'http://jitsi.org/jitsimeet'
        };

        const options = {
          roomName: this.roomName,
          parentNode: this.$refs.jitsiContainer,
          configOverwrite: {
            ...defaultConfig,
            ...this.config,
            startWithAudioMuted: false,
            startWithVideoMuted: false,
            disableSimulcast: false,
            enableLayerSuspension: true,
            p2p: {
              enabled: true,
              stunServers: [
                { urls: 'stun:stun.l.google.com:19302' }
              ]
            }
          },
          interfaceConfigOverwrite: {
            SHOW_JITSI_WATERMARK: false,
            SHOW_WATERMARK_FOR_GUESTS: false,
            DEFAULT_BACKGROUND: '#474747',
            DEFAULT_REMOTE_DISPLAY_NAME: 'Участник',
            DEFAULT_LOCAL_DISPLAY_NAME: 'Вы',
            SHOW_BRAND_WATERMARK: false,
            SHOW_POWERED_BY: false,
            ...this.interfaceConfig
          },
          userInfo: {
            displayName: this.user.name,
            email: this.user.email
          },
          onload: this.onJitsiLoad
        };

        // Инициализация API
        this.api = new JitsiMeetExternalAPI('localhost:8443', options);

        // Регистрация обработчиков событий
        this.registerEventHandlers();

      } catch (error) {
        console.error('Ошибка инициализации Jitsi:', error);
        this.connectionStatus = 'Ошибка подключения';
        this.$emit('error', error);
      }
    },

    async loadJitsiScript() {
      return new Promise((resolve, reject) => {
        if (typeof JitsiMeetExternalAPI !== 'undefined') {
          resolve();
          return;
        }

        const script = document.createElement('script');
        script.src = 'https://localhost:8443/external_api.js';
        script.async = true;
        script.defer = true;

        script.onload = () => {
          if (typeof JitsiMeetExternalAPI !== 'undefined') {
            resolve();
          } else {
            reject(new Error('Jitsi API не загрузился'));
          }
        };

        script.onerror = () => reject(new Error('Не удалось загрузить Jitsi API'));
        document.head.appendChild(script);
      });
    },

    registerEventHandlers() {
      if (!this.api) return;

      // События конференции
      this.api.on('videoConferenceJoined', () => {
        this.connectionStatus = 'Подключен';
        this.$emit('conference-joined');
      });

      this.api.on('readyToClose', () => {
        this.$emit('conference-ended');
      });

      this.api.on('participantJoined', (participant) => {
        this.participants.push(participant);
        this.$emit('participant-joined', participant);
      });

      this.api.on('participantLeft', (participant) => {
        this.participants = this.participants.filter(p => p.id !== participant.id);
        this.$emit('participant-left', participant);
      });

      // События мультимедиа
      this.api.on('audioMuteStatusChanged', ({ muted }) => {
        this.isAudioMuted = muted;
        this.$emit('audio-toggle', muted);
      });

      this.api.on('videoMuteStatusChanged', ({ muted }) => {
        this.isVideoMuted = muted;
        this.$emit('video-toggle', muted);
      });

      this.api.on('screenSharingStatusChanged', ({ on }) => {
        this.isSharingScreen = on;
        this.$emit('screen-share-toggle', on);
      });

      // Ошибки
      this.api.on('connectionFailed', (error) => {
        this.connectionStatus = 'Ошибка подключения';
        this.$emit('error', error);
      });

      this.api.on('passwordRequired', () => {
        this.$emit('password-required');
      });
    },

    // Методы управления
    toggleAudio() {
      if (this.api) {
        this.api.executeCommand('toggleAudio');
      }
    },

    toggleVideo() {
      if (this.api) {
        this.api.executeCommand('toggleVideo');
      }
    },

    async toggleScreenShare() {
      if (this.api) {
        try {
          if (!this.isSharingScreen) {
            await this.api.executeCommand('toggleShareScreen');
          } else {
            this.api.executeCommand('toggleShareScreen');
          }
        } catch (error) {
          console.error('Ошибка демонстрации экрана:', error);
        }
      }
    },

    hangUp() {
      if (this.api) {
        this.api.executeCommand('hangup');
        this.$emit('hangup');
      }
    },

    sendMessage(message) {
      if (this.api) {
        this.api.executeCommand('sendChatMessage', message);
        this.$emit('message-sent', message);
      }
    },

    disposeJitsi() {
      if (this.api) {
        this.api.dispose();
        this.api = null;
      }
    }
  }
};
</script>

<style scoped>
.jitsi-container {
  position: relative;
  width: 100%;
  height: 100vh;
  background: #000;
}

.jitsi-meet-container {
  width: 100%;
  height: calc(100vh - 60px);
}

.meeting-controls {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 10px;
  padding: 10px;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 20px;
  z-index: 1000;
}

.control-btn {
  padding: 10px 20px;
  background: #0056cc;
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  transition: background 0.3s;
}

.control-btn:hover {
  background: #007bff;
}

.control-btn.hangup {
  background: #ff0000;
}

.control-btn.hangup:hover {
  background: #cc0000;
}

.connection-status {
  position: absolute;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 10px 20px;
  border-radius: 20px;
  z-index: 1000;
}
</style>
