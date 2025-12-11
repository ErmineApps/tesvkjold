<template>
  <div class="test-component">
    <h2>Тест подключения WebSocket</h2>

    <div class="test-section">
      <button @click="testWebSocket">Тестировать WebSocket</button>
      <div class="result" :class="testResult.status">
        {{ testResult.message }}
      </div>
    </div>

    <div class="test-section">
      <h3>Подключение к конференции</h3>
      <input v-model="roomName" placeholder="Имя комнаты">
      <input v-model="displayName" placeholder="Ваше имя">
      <button @click="connect" :disabled="connecting">
        {{ connecting ? 'Подключение...' : 'Подключиться' }}
      </button>

      <div v-if="connectionStatus" class="status">
        {{ connectionStatus }}
      </div>
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>
  </div>
</template>

<script>
import JitsiService from '@/assets/ap_kurento/JitsiService';

export default {
  name: 'WebSocketTest',
  data() {
    return {
      roomName: 'test-room-' + Date.now(),
      displayName: 'User-' + Math.floor(Math.random() * 1000),
      connecting: false,
      connectionStatus: '',
      error: '',
      testResult: {
        status: 'idle',
        message: ''
      }
    };
  },
  methods: {
    async testWebSocket() {
      this.testResult = { status: 'testing', message: 'Тестирование WebSocket...' };

      // Пробуем разные URL WebSocket
      const urls = [
        'wss://test.iksar.io/telms/telms/xmpp-websocket',
        'wss://test.iksar.io/telms/telms/xmpp-websocket?room=123'
      ];

      for (const url of urls) {
        const result = await this.testSingleWebSocket(url);
        if (result.success) {
          this.testResult = {
            status: 'success',
            message: `✅ WebSocket работает: ${url}`
          };
          return;
        }
      }

      this.testResult = {
        status: 'error',
        message: '❌ Не удалось подключиться ни к одному WebSocket URL'
      };
    },

    testSingleWebSocket(url) {
      return new Promise((resolve) => {
        const ws = new WebSocket(url);

        ws.onopen = () => {
          ws.close();
          resolve({ success: true, url });
        };

        ws.onerror = () => {
          resolve({ success: false, url });
        };

        setTimeout(() => {
          if (ws.readyState === WebSocket.CONNECTING) {
            ws.close();
            resolve({ success: false, url });
          }
        }, 3000);
      });
    },

    async connect() {
      try {
        this.connecting = true;
        this.error = '';
        this.connectionStatus = 'Подготовка...';

        // Сначала тестируем WebSocket
        const wsAvailable = await JitsiService.checkWebSocket();
        if (!wsAvailable) {
          throw new Error('WebSocket сервер недоступен');
        }

        this.connectionStatus = 'Создание локальных треков...';

        // Создаем локальные треки
        const localTracks = await JitsiService.createLocalTracks();

        this.connectionStatus = 'Подключение к конференции...';

        // Пробуем подключиться
        await JitsiService.connect(this.roomName, this.displayName);

        this.connectionStatus = '✅ Успешно подключено!';

        // Настраиваем обработчики
        JitsiService.on('conference-joined', () => {
          this.connectionStatus = '🎉 В конференции!';
        });

        JitsiService.on('conference-error', (error) => {
          console.error('Ошибка конференции:', error);
          this.error = `Ошибка конференции: ${error.message || error}`;
        });

      } catch (error) {
        console.error('Ошибка подключения:', error);
        this.error = `Ошибка: ${error.message}`;
        this.connectionStatus = '❌ Ошибка подключения';
      } finally {
        this.connecting = false;
      }
    }
  }
};
</script>

<style scoped>
.test-component {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.test-section {
  margin: 20px 0;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

input {
  display: block;
  width: 100%;
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 10px 20px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin: 5px;
}

button:hover:not(:disabled) {
  background: #0056cc;
}

button:disabled {
  background: #6c757d;
  cursor: not-allowed;
}

.result {
  margin-top: 10px;
  padding: 10px;
  border-radius: 4px;
}

.result.idle {
  background: #f8f9fa;
  color: #6c757d;
}

.result.testing {
  background: #fff3cd;
  color: #856404;
}

.result.success {
  background: #d4edda;
  color: #155724;
}

.result.error {
  background: #f8d7da;
  color: #721c24;
}

.status {
  margin-top: 10px;
  padding: 10px;
  background: #e9ecef;
  border-radius: 4px;
  font-weight: bold;
}

.error {
  margin-top: 10px;
  padding: 10px;
  background: #f8d7da;
  color: #721c24;
  border-radius: 4px;
  border: 1px solid #f5c6cb;
}
</style>
