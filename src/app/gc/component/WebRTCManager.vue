<template>
  <div class="webrtc-manager" style="display: none;">
    <!-- Этот компонент не отображает UI, только управляет WebRTC соединениями -->
  </div>
</template>

<script>
export default {
  name: 'WebRTCManager',

  props: {
    roomId: {
      type: String,
      required: true
    },
    localStream: {
      type: MediaStream,
      default: null
    },
    participants: {
      type: Array,
      default: () => []
    },
    iksarWebrtc: {
      type: Object,
      required: true
    },
    ws: {
      type: WebSocket,
      required: true
    },
    isExternalUser: {
      type: Boolean,
      default: false
    },
    audioInputValue: {
      type: String,
      default: ''
    },
    isSaveCallMy: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      // Карта участников и их соединений
      peerConnections: new Map(),
      peerInstances: new Map(),

      // Глобальные потоки
      STREAM_AUDIO: null,
      STREAM_AUDIO_SAVE: null,

      // Таймеры
      connectionTimeouts: new Map(),
      reconnectAttempts: new Map(),

      // Статистика
      connectionStats: new Map(),
      lastStatsUpdate: new Map(),
      audioContexts: new Map(),

      // Локальный поток
      currentLocalStream: null,

      // Оптимизация
      maxReconnectAttempts: 3,
      connectionTimeout: 10000,
      isUpgradingVideo: false
    };
  },

  watch: {
    participants: {
      deep: true,
      handler(newParticipants, oldParticipants) {
        this.syncParticipants(newParticipants, oldParticipants);
      }
    },

    localStream: {
      immediate: true,
      handler(newStream) {
        this.updateLocalStream(newStream);
      }
    }
  },

  computed: {
    totalParticipants() {
      return this.participants.length;
    }
  },

  methods: {
    // ОБНОВЛЕНИЕ ЛОКАЛЬНОГО ПОТОКА
    updateLocalStream(newStream) {
      console.log('updateLocalStream вызван', newStream);

      // Сохраняем локальный поток
      this.currentLocalStream = newStream;

      // Инициализируем глобальные аудио потоки
      if (newStream && !this.STREAM_AUDIO) {
        this.STREAM_AUDIO = newStream;
        this.STREAM_AUDIO_SAVE = newStream;
      }

      // Обновляем все существующие соединения
      this.updateAllPeerConnections(newStream);

      // Обновляем качество видео в зависимости от количества участников
      this.adjustVideoQuality(this.totalParticipants);

      this.$emit('local-stream-updated', newStream);
    },

    // ОБНОВЛЕНИЕ ВСЕХ PEER СОЕДИНЕНИЙ
    updateAllPeerConnections(newStream) {
      this.peerInstances.forEach((peer, participantId) => {
        if (peer.peerConnection && newStream) {
          // Обновляем локальные треки во всех соединениях
          this.updateLocalTracks(peer.peerConnection, newStream);
        }
      });
    },

    // ОБНОВЛЕНИЕ ЛОКАЛЬНЫХ ТРЕКОВ В СОЕДИНЕНИИ
    updateLocalTracks(peerConnection, newStream) {
      const senders = peerConnection.getSenders();

      // Обновляем аудио трек
      const audioSender = senders.find(s => s.track && s.track.kind === 'audio');
      if (audioSender && newStream.getAudioTracks().length > 0) {
        audioSender.replaceTrack(newStream.getAudioTracks()[0])
          .then(() => console.log('Аудио трек обновлен'))
          .catch(err => console.error('Ошибка обновления аудио трека:', err));
      }

      // Обновляем видео трек
      const videoSender = senders.find(s => s.track && s.track.kind === 'video');
      if (videoSender && newStream.getVideoTracks().length > 0) {
        videoSender.replaceTrack(newStream.getVideoTracks()[0])
          .then(() => console.log('Видео трек обновлен'))
          .catch(err => console.error('Ошибка обновления видео трека:', err));
      }
    },

    // Основной метод для создания WebRTC соединения
    async createPeerConnection(participant, hasCamera) {
      console.log('Создание WebRTC соединения для', participant.id);

      // Очищаем предыдущее соединение, если есть
      this.cleanupParticipant(participant.id);

      // Настройки constraints
      const constraints = {
        audio: {
          optional: [
            { sourceId: this.audioInputValue },
            { echoCancellation: true },
            { noiseSuppression: true },
            { autoGainControl: true },
            { volume: 1.5 }
          ]
        },
        video: {
          mandatory: {
            maxWidth: 320,
            maxFrameRate: 15,
            minFrameRate: 15
          }
        }
      };

      // Настройки WebRTC
      const options = {
        remoteVideo: hasCamera ?
          this.getVideoElement(participant.id) :
          this.getAudioElement(participant.id),
        name: participant.id,
        onicecandidate: this.onIceCandidate.bind(this, participant.id),
        onconnectionstatechange: this.onConnectionStateChange.bind(this, participant.id),
        ontrack: this.onTrack.bind(this, participant.id),
        onnegotiationneeded: this.onNegotiationNeeded.bind(this, participant.id),
        oniceconnectionstatechange: this.onIceConnectionStateChange.bind(this, participant.id),
        onicegatheringstatechange: this.onIceGatheringStateChange.bind(this, participant.id),
        onsignalingstatechange: this.onSignalingStateChange.bind(this, participant.id)
      };

      try {
        // Создаем экземпляр WebRtcPeerRecvonly
        const self = this;
        const participantId = participant.id;

        const peer = new this.iksarWebrtc.WebRtcPeer.WebRtcPeerRecvonly(
          options,
          function(error) {
            if (error) {
              console.error('Ошибка создания WebRtcPeer для', participantId, error);
              self.handleConnectionError(participantId, error);
              return;
            }

            console.log('Подключение к webrtc на получение видео от', participant.name);

            // Сохраняем ссылки на оригинальные методы
            participant.ofTEST = self.offerToReceiveVideo;
            participant.ofOPTION = options;
            participant.peer = this;

            // Сохраняем в карты
            self.peerInstances.set(participantId, this);

            // Добавляем локальный поток, если он есть
            if (self.currentLocalStream) {
              self.addLocalStreamToConnection(this.peerConnection, self.currentLocalStream);
            }

            // Генерируем предложение
            this.generateOffer(participant.ofTEST, participantId);
          }
        );

        // Сохраняем соединение
        this.peerConnections.set(participant.id, peer.peerConnection);

        // Настраиваем таймаут соединения
        this.setupConnectionTimeout(participant.id);

        return peer;

      } catch (error) {
        console.error('Ошибка при создании WebRTC соединения:', error);
        this.handleConnectionError(participant.id, error);
        return null;
      }
    },

    // ДОБАВЛЕНИЕ ЛОКАЛЬНОГО ПОТОКА В СОЕДИНЕНИЕ
    addLocalStreamToConnection(peerConnection, localStream) {
      if (!localStream) return;

      // Добавляем все треки из локального потока
      localStream.getTracks().forEach(track => {
        peerConnection.addTrack(track, localStream);
      });

      console.log('Локальный поток добавлен в соединение');
    },

    // КОРРЕКТИРОВКА КАЧЕСТВА ВИДЕО
    adjustVideoQuality(participantCount) {
      if (!this.currentLocalStream || this.isUpgradingVideo) return;

      const videoTrack = this.currentLocalStream.getVideoTracks()[0];
      if (!videoTrack) return;

      let constraints = {};

      if (participantCount > 8) {
        // Низкое качество при многих участниках
        constraints = {
          width: 320,
          height: 240,
          frameRate: 10
        };
        console.log('Качество видео: низкое (320x240, 10fps)');
      } else if (participantCount > 5) {
        // Среднее качество
        constraints = {
          width: 640,
          height: 360,
          frameRate: 15
        };
        console.log('Качество видео: среднее (640x360, 15fps)');
      } else {
        // Высокое качество
        constraints = {
          width: 1280,
          height: 720,
          frameRate: 24
        };
        console.log('Качество видео: высокое (1280x720, 24fps)');
      }

      // Применяем ограничения
      this.isUpgradingVideo = true;
      videoTrack.applyConstraints(constraints)
        .then(() => {
          console.log('Качество видео обновлено');
          this.isUpgradingVideo = false;
        })
        .catch(err => {
          console.error('Ошибка обновления качества видео:', err);
          this.isUpgradingVideo = false;
        });
    },

    // UPGRADE VIDEO метод из оригинала
    upgradeVideo() {
      console.log('upgradeVideo вызван, участников:', this.totalParticipants);

      const hasOperators = this.participants.some(p => p.isOperator);

      // Проверка на отключение видео при наличии операторов
      if (this.totalParticipants > 7 && hasOperators && this.currentLocalStream) {
        const videoTrack = this.currentLocalStream.getVideoTracks()[0];
        if (videoTrack && !this.isMaxOperVideo) {
          this.isMaxOperVideo = true;
          this.isStreamVideoOld = videoTrack.enabled;

          if (videoTrack.enabled) {
            videoTrack.enabled = false;
            console.log('Видео отключено (более 7 участников с оператором)');
          }
        }
      } else if (this.totalParticipants < 8 && hasOperators && this.currentLocalStream) {
        const videoTrack = this.currentLocalStream.getVideoTracks()[0];
        if (videoTrack && this.isMaxOperVideo) {
          this.isMaxOperVideo = false;
          if (this.isStreamVideoOld) {
            videoTrack.enabled = true;
            console.log('Видео включено обратно');
          }
        }
      }

      // Регулировка качества в зависимости от количества участников
      this.adjustVideoQuality(this.totalParticipants);

      this.$emit('video-quality-updated', {
        participantCount: this.totalParticipants,
        hasOperators
      });
    },

    // Генерация предложения (SDP offer)
    async generateOffer(callback, participantId) {
      try {
        const peer = this.peerInstances.get(participantId);
        if (!peer) {
          throw new Error('WebRTC peer не найден');
        }

        const offer = await peer.peerConnection.createOffer({
          offerToReceiveAudio: true,
          offerToReceiveVideo: true
        });

        await peer.peerConnection.setLocalDescription(offer);

        // Отправляем предложение через WebSocket
        this.sendOfferToServer(participantId, offer);

        // Вызываем оригинальный callback
        if (typeof callback === 'function') {
          callback(this, null, offer.sdp, participantId, false);
        }

      } catch (error) {
        console.error('Ошибка при генерации SDP offer:', error);
        if (typeof callback === 'function') {
          callback(this, error, null, participantId, true);
        }
      }
    },

    // Обработка предложения от сервера (ответ)
    async handleRemoteOffer(participantId, sdpOffer) {
      try {
        const peer = this.peerInstances.get(participantId);
        if (!peer) {
          throw new Error('WebRTC peer не найден');
        }

        const remoteDesc = new RTCSessionDescription({
          type: 'offer',
          sdp: sdpOffer
        });

        await peer.peerConnection.setRemoteDescription(remoteDesc);

        const answer = await peer.peerConnection.createAnswer();
        await peer.peerConnection.setLocalDescription(answer);

        // Отправляем ответ серверу
        this.sendAnswerToServer(participantId, answer);

        console.log('Ответ на offer отправлен для', participantId);

      } catch (error) {
        console.error('Ошибка при обработке remote offer:', error);
        this.handleConnectionError(participantId, error);
      }
    },

    // Обработка получения треков
    onTrack(participantId, event) {
      console.log('Получен трек от', participantId);

      const [stream] = event.streams;
      if (!stream) return;

      // Обновляем видео/аудио элементы
      this.updateMediaElements(participantId, stream);

      // Настраиваем анализ аудио
      this.setupAudioAnalysis(participantId, stream);

      // Настраиваем объединение аудио потоков для записи
      this.setupAudioMixing(participantId, stream);

      // Запускаем сбор статистики
      this.startStatsCollection(participantId);

      // Уведомляем родительский компонент
      this.$emit('track-received', {
        participantId,
        stream,
        track: event.track
      });
    },

    // НАСТРОЙКА СМЕШИВАНИЯ АУДИО ДЛЯ ЗАПИСИ
    setupAudioMixing(participantId, incomingStream) {
      if (!this.isSaveCallMy || !this.STREAM_AUDIO_SAVE) return;

      try {
        const incomingAudioTrack = incomingStream.getAudioTracks()[0];
        if (!incomingAudioTrack) return;

        // Создаем AudioContext для смешивания
        const audioContext = new (window.AudioContext || window.webkitAudioContext)();

        // Создаем потоки
        const outgoingAudioStream = new MediaStream();
        outgoingAudioStream.addTrack(this.STREAM_AUDIO_SAVE.getAudioTracks()[0]);

        const incomingAudioStream = new MediaStream();
        incomingAudioStream.addTrack(incomingAudioTrack);

        // Создаем источники
        const audioIn1 = audioContext.createMediaStreamSource(outgoingAudioStream);
        const audioIn2 = audioContext.createMediaStreamSource(incomingAudioStream);

        // Создаем назначение
        const dest = audioContext.createMediaStreamDestination();

        // Соединяем источники с назначением
        audioIn1.connect(dest);
        audioIn2.connect(dest);

        // Обновляем глобальный поток для записи
        this.STREAM_AUDIO_SAVE = dest.stream;

        // Сохраняем AudioContext для очистки
        this.audioContexts.set(participantId, audioContext);

        console.log('Аудио смешивание настроено для записи');

        // Уведомляем родителя о новом аудио треке
        this.$emit('new-audio-track', {
          participantId,
          track: incomingAudioTrack
        });

      } catch (error) {
        console.error('Ошибка при настройке смешивания аудио:', error);
      }
    },

    // Обработка ICE кандидатов
    onIceCandidate(participantId, event) {
      if (event.candidate) {
        this.sendICECandidate(participantId, event.candidate);
      }
    },

    // Отправка ICE кандидата на сервер
    sendICECandidate(participantId, candidate) {
      const message = {
        id: 'onIceCandidate',
        senderId: participantId,
        candidate: candidate.candidate,
        sdpMid: candidate.sdpMid,
        sdpMLineIndex: candidate.sdpMLineIndex
      };

      this.ws.send(JSON.stringify(message));
      this.$emit('ice-candidate-sent', { participantId, candidate });
    },

    // Обработка ошибок соединения
    handleConnectionError(participantId, error) {
      console.error(`Ошибка соединения с ${participantId}:`, error);

      const attempts = this.reconnectAttempts.get(participantId) || 0;

      if (attempts < this.maxReconnectAttempts) {
        // Пытаемся переподключиться
        this.reconnectAttempts.set(participantId, attempts + 1);

        setTimeout(() => {
          this.reconnectParticipant(participantId);
        }, 2000 * attempts); // Экспоненциальная задержка

        this.$emit('connection-error', {
          participantId,
          error,
          reconnectAttempt: attempts + 1
        });
      } else {
        // Превышено количество попыток
        this.$emit('connection-failed', {
          participantId,
          error,
          finalAttempt: true
        });

        this.cleanupParticipant(participantId);
      }
    },

    // Переподключение участника
    async reconnectParticipant(participantId) {
      console.log(`Переподключение к ${participantId}, попытка ${this.reconnectAttempts.get(participantId)}`);

      const participant = this.participants.find(p => p.id === participantId);
      if (!participant) return;

      // Очищаем старое соединение
      this.cleanupParticipant(participantId);

      // Создаем новое соединение
      await this.createPeerConnection(participant, participant.hasCamera);

      this.$emit('reconnect-attempt', {
        participantId,
        attempt: this.reconnectAttempts.get(participantId)
      });
    },

    // Настройка анализа аудио для индикации речи
    setupAudioAnalysis(participantId, stream) {
      const audioTracks = stream.getAudioTracks();
      if (audioTracks.length === 0) return;

      try {
        const audioContext = new (window.AudioContext || window.webkitAudioContext)();
        const analyser = audioContext.createAnalyser();
        analyser.fftSize = 256;

        const source = audioContext.createMediaStreamSource(stream);
        source.connect(analyser);

        // Сохраняем анализатор
        const participant = this.participants.find(p => p.id === participantId);
        if (participant) {
          participant.audioAnalyser = analyser;
          participant.audioContext = audioContext;
        }

        // Запускаем мониторинг уровня звука
        this.startAudioLevelMonitoring(participantId, analyser);

      } catch (error) {
        console.error('Ошибка при настройке анализа аудио:', error);
      }
    },

    // Мониторинг уровня звука
    startAudioLevelMonitoring(participantId, analyser) {
      const bufferLength = analyser.frequencyBinCount;
      const dataArray = new Uint8Array(bufferLength);

      const checkAudioLevel = () => {
        if (!this.peerInstances.has(participantId)) return;

        analyser.getByteFrequencyData(dataArray);

        // Вычисляем средний уровень
        let sum = 0;
        for (let i = 0; i < bufferLength; i++) {
          sum += dataArray[i];
        }
        const average = sum / bufferLength;

        // Определяем, говорит ли пользователь (порог можно настроить)
        const isSpeaking = average > 20; // Порог 20 из 255

        // Уведомляем родительский компонент
        this.$emit('audio-level', {
          participantId,
          level: average,
          isSpeaking,
          normalizedLevel: average / 255
        });

        // Продолжаем мониторинг
        if (this.peerInstances.has(participantId)) {
          requestAnimationFrame(checkAudioLevel);
        }
      };

      requestAnimationFrame(checkAudioLevel);
    },

    // Сбор статистики соединения
    async startStatsCollection(participantId) {
      const peer = this.peerInstances.get(participantId);
      if (!peer) return;

      const collectStats = async () => {
        if (!this.peerInstances.has(participantId)) return;

        try {
          const stats = await peer.peerConnection.getStats();

          // Парсим статистику
          const parsedStats = this.parseConnectionStats(stats);
          this.connectionStats.set(participantId, parsedStats);
          this.lastStatsUpdate.set(participantId, Date.now());

          // Уведомляем родительский компонент
          this.$emit('connection-stats', {
            participantId,
            stats: parsedStats,
            timestamp: Date.now()
          });

          // Проверяем качество соединения
          this.checkConnectionQuality(participantId, parsedStats);

          // Продолжаем сбор статистики
          if (this.peerInstances.has(participantId)) {
            setTimeout(() => collectStats(), 2000); // Каждые 2 секунды
          }

        } catch (error) {
          console.error('Ошибка при сборе статистики:', error);
        }
      };

      collectStats();
    },

    // Парсинг статистики WebRTC
    parseConnectionStats(stats) {
      const result = {
        audio: {},
        video: {},
        connection: {},
        candidate: {},
        tracks: []
      };

      stats.forEach(report => {
        if (report.type === 'inbound-rtp' && report.kind === 'audio') {
          result.audio = {
            bytesReceived: report.bytesReceived,
            packetsReceived: report.packetsReceived,
            packetsLost: report.packetsLost,
            jitter: report.jitter,
            jitterBufferDelay: report.jitterBufferDelay,
            jitterBufferEmittedCount: report.jitterBufferEmittedCount,
            timestamp: report.timestamp
          };
        } else if (report.type === 'inbound-rtp' && report.kind === 'video') {
          result.video = {
            bytesReceived: report.bytesReceived,
            framesReceived: report.framesReceived,
            framesDecoded: report.framesDecoded,
            framesDropped: report.framesDropped,
            frameWidth: report.frameWidth,
            frameHeight: report.frameHeight,
            timestamp: report.timestamp
          };
        } else if (report.type === 'candidate-pair' && report.state === 'succeeded') {
          result.connection = {
            rtt: report.currentRoundTripTime,
            availableOutgoingBitrate: report.availableOutgoingBitrate,
            bytesSent: report.bytesSent,
            bytesReceived: report.bytesReceived
          };
        } else if (report.type === 'remote-candidate') {
          result.candidate = {
            ip: report.ip,
            port: report.port,
            type: report.candidateType,
            protocol: report.protocol
          };
        } else if (report.type === 'track') {
          result.tracks.push({
            id: report.id,
            kind: report.kind,
            trackIdentifier: report.trackIdentifier,
            ended: report.ended
          });
        }
      });

      return result;
    },

    // Проверка качества соединения
    checkConnectionQuality(participantId, stats) {
      let quality = 5; // Отличное качество по умолчанию

      if (stats.audio && stats.audio.packetsLost > 10) {
        quality -= 1;
      }

      if (stats.video && stats.video.framesDropped > 5) {
        quality -= 1;
      }

      if (stats.connection && stats.connection.rtt > 300) {
        quality -= 1;
      }

      // Ограничиваем качество от 1 до 5
      quality = Math.max(1, Math.min(5, quality));

      this.$emit('connection-quality', {
        participantId,
        quality,
        stats
      });
    },

    // Обновление медиа элементов
    updateMediaElements(participantId, stream) {
      const videoElement = this.getVideoElement(participantId);
      const audioElement = this.getAudioElement(participant.id);

      if (videoElement) {
        videoElement.srcObject = stream;
        videoElement.play().catch(console.error);
      }

      if (audioElement) {
        // Создаем отдельный поток только с аудио
        const audioStream = new MediaStream(stream.getAudioTracks());
        audioElement.srcObject = audioStream;
        audioElement.volume = 1.0;
        audioElement.muted = false;

        // Скрываем аудио элемент (как в оригинальном коде)
        audioElement.setAttribute('hidden', 'hidden');
        audioElement.play().catch(console.error);
      }
    },

    // Вспомогательные методы для получения DOM элементов
    getVideoElement(participantId) {
      return document.getElementById(`video_${participantId}`);
    },

    getAudioElement(participantId) {
      return document.getElementById(`audio_${participantId}`);
    },

    // Настройка таймаута соединения
    setupConnectionTimeout(participantId) {
      // Очищаем предыдущий таймаут
      if (this.connectionTimeouts.has(participantId)) {
        clearTimeout(this.connectionTimeouts.get(participantId));
      }

      // Устанавливаем новый таймаут
      const timeout = setTimeout(() => {
        if (!this.isConnected(participantId)) {
          console.warn(`Таймаут соединения для ${participantId}`);
          this.handleConnectionError(participantId, new Error('Connection timeout'));
        }
        this.connectionTimeouts.delete(participantId);
      }, this.connectionTimeout);

      this.connectionTimeouts.set(participantId, timeout);
    },

    // Проверка соединения
    isConnected(participantId) {
      const peer = this.peerInstances.get(participantId);
      return peer &&
        peer.peerConnection &&
        peer.peerConnection.connectionState === 'connected';
    },

    // Очистка ресурсов участника
    cleanupParticipant(participantId) {
      console.log(`Очистка ресурсов участника ${participantId}`);

      // Очищаем таймауты
      if (this.connectionTimeouts.has(participantId)) {
        clearTimeout(this.connectionTimeouts.get(participantId));
        this.connectionTimeouts.delete(participantId);
      }

      // Очищаем попытки переподключения
      this.reconnectAttempts.delete(participantId);

      // Закрываем WebRTC соединение
      const peer = this.peerInstances.get(participantId);
      if (peer) {
        if (peer.peerConnection) {
          peer.peerConnection.close();
        }
        if (peer.dispose) {
          peer.dispose();
        }
        this.peerInstances.delete(participantId);
      }

      // Очищаем AudioContext
      if (this.audioContexts.has(participantId)) {
        const audioContext = this.audioContexts.get(participantId);
        if (audioContext && audioContext.close) {
          audioContext.close();
        }
        this.audioContexts.delete(participantId);
      }

      // Очищаем статистику
      this.connectionStats.delete(participantId);
      this.lastStatsUpdate.delete(participantId);

      console.log(`Ресурсы участника ${participantId} очищены`);

      this.$emit('participant-cleaned', participantId);
    },

    // Синхронизация участников
    syncParticipants(newParticipants, oldParticipants) {
      const newIds = new Set(newParticipants.map(p => p.id));
      const oldIds = new Set(oldParticipants.map(p => p.id));

      // Удаляем старых участников
      oldIds.forEach(id => {
        if (!newIds.has(id)) {
          this.cleanupParticipant(id);
        }
      });

      // Добавляем новых участников
      newParticipants.forEach(participant => {
        if (!oldIds.has(participant.id)) {
          this.createPeerConnection(participant, participant.hasCamera);
        }
      });

      // Обновляем качество видео
      this.upgradeVideo();
    },

    // Отправка сообщений через WebSocket
    sendOfferToServer(participantId, offer) {
      const message = {
        id: 'receiveVideoFrom',
        senderId: participantId,
        sdpOffer: offer.sdp
      };

      this.ws.send(JSON.stringify(message));
      this.$emit('offer-sent', { participantId, offer });
    },

    sendAnswerToServer(participantId, answer) {
      const message = {
        id: 'receiveVideoAnswer',
        senderId: participantId,
        sdpAnswer: answer.sdp
      };

      this.ws.send(JSON.stringify(message));
      this.$emit('answer-sent', { participantId, answer });
    },

    // Обработчики событий WebRTC
    onConnectionStateChange(participantId) {
      const peer = this.peerInstances.get(participantId);
      if (!peer) return;

      const state = peer.peerConnection.connectionState;
      console.log(`Состояние соединения ${participantId}: ${state}`);

      this.$emit('connection-state-change', {
        participantId,
        state
      });
    },

    onIceConnectionStateChange(participantId) {
      const peer = this.peerInstances.get(participantId);
      if (!peer) return;

      const state = peer.peerConnection.iceConnectionState;
      console.log(`ICE состояние ${participantId}: ${state}`);

      this.$emit('ice-state-change', {
        participantId,
        state
      });
    },

    onIceGatheringStateChange(participantId) {
      const peer = this.peerInstances.get(participantId);
      if (!peer) return;

      const state = peer.peerConnection.iceGatheringState;
      console.log(`ICE gathering ${participantId}: ${state}`);
    },

    onSignalingStateChange(participantId) {
      const peer = this.peerInstances.get(participantId);
      if (!peer) return;

      const state = peer.peerConnection.signalingState;
      console.log(`Signaling состояние ${participantId}: ${state}`);
    },

    onNegotiationNeeded(participantId) {
      console.log(`Требуется переговоры для ${participantId}`);
      this.generateOffer(null, participantId);
    },

    // Метод для ручного обновления соединения (из оригинала)
    updatePeerConnection(participant) {
      console.log('Обновление соединения для', participant.id);

      this.cleanupParticipant(participant.id);

      // Отправляем сообщение серверу о необходимости восстановления
      const message = {
        id: 'receiveVideoFromErrorJS',
        room: this.roomId,
        senderName: participant.name,
        senderId: participant.id
      };

      this.ws.send(JSON.stringify(message));

      // Создаем новое соединение через 1 секунду
      setTimeout(() => {
        this.createPeerConnection(participant, participant.hasCamera);
      }, 1000);

      this.$emit('peer-connection-updated', participant.id);
    },

    // ОБРАБОТКА ДАННЫХ И СОЗДАНИЕ PARTICIPANT (из оригинала)
    newCallData() {
      return {
        senderName: '',
        senderId: '',
        new: true,
        avatar: '',
        isMic: true,
        isFuncOper: false,
        update: 0,
        orient: 0,
        delta: 1.7,
        prId: null,
        resultId: null,
        stepId: null,
        fileId: null,
        isRealFuncOper: false,
        full: false,
        nameReplase: '',
        isMainWin: false,
        OperFunc: false,
        ws: this.ws,
        bodyPix: false,
        isPaint: false,
        SSFunc: false,
        scrs: false,
        peer: null,
        video: null,
        audio: null,
        ofTEST: null,
        ofOPTION: null,
        mediaStream: null,
        audioAnalyser: null,
        audioContext: null,
        idTrackAudio: null,
        idTrackVideo: null,
        dateConnect: null,
        connectionQuality: 5
      };
    },

    // Обработка offerToReceiveVideo (из оригинала)
    offerToReceiveVideo(context, error, offerSdp, senderId, isError) {
      const participant = this.participants.find(p => p.id === senderId);
      if (participant) {
        participant.new = false;
      }

      if (error) {
        return console.error("sdp offer error");
      }

      const msg = {
        id: "receiveVideoFrom",
        senderId: senderId,
        senderName: '',
        sdpOffer: offerSdp
      };

      this.sendMessage(msg);

      // Логика добавления аудио дорожек для сохранения
      if (this.STREAM_AUDIO_SAVE == null) {
        this.STREAM_AUDIO_SAVE = this.STREAM_AUDIO;
      }

      setTimeout(() => {
        const peer = this.peerInstances.get(senderId);
        if (!peer) return;

        const senders = peer.peerConnection.getReceivers();

        console.log('Подключение к webrtc на getReceivers ' + senderId);

        const d = new Date();
        if (participant) {
          participant.dateConnect = d.getTime();
        }

        // Запускаем сбор статистики
        this.startStatsCollection(senderId);

        // Смешивание аудио для записи
        senders.forEach((sender) => {
          try {
            if (sender.track && sender.track.kind == 'audio') {
              this.setupAudioMixing(senderId, new MediaStream([sender.track]));
            }
          } catch (e) {
            console.error('error all audio = ' + e);
          }
        });

        // Снижение качества совего видео от кол-ва участников
        this.upgradeVideo();

      }, 500);

      this.$emit('offer-to-receive-video', {
        senderId,
        offerSdp,
        error
      });
    },

    // Вспомогательный метод для отправки сообщений
    sendMessage(message) {
      if (this.ws && this.ws.readyState === WebSocket.OPEN) {
        this.ws.send(JSON.stringify(message));
      } else {
        console.error('WebSocket не подключен');
      }
    },

    // Закрытие всех соединений
    closeAllConnections() {
      console.log('Закрытие всех WebRTC соединений');

      // Очищаем всех участников
      Array.from(this.peerInstances.keys()).forEach(participantId => {
        this.cleanupParticipant(participantId);
      });

      // Очищаем все таймауты
      this.connectionTimeouts.forEach(timeout => clearTimeout(timeout));
      this.connectionTimeouts.clear();

      // Очищаем все AudioContext
      this.audioContexts.forEach(context => {
        if (context && context.close) {
          context.close();
        }
      });
      this.audioContexts.clear();

      // Очищаем все карты
      this.peerInstances.clear();
      this.reconnectAttempts.clear();
      this.connectionStats.clear();
      this.lastStatsUpdate.clear();

      // Очищаем глобальные потоки
      this.STREAM_AUDIO = null;
      this.STREAM_AUDIO_SAVE = null;
      this.currentLocalStream = null;

      console.log('Все WebRTC соединения закрыты');

      this.$emit('all-connections-closed');
    }
  },

  mounted() {
    console.log('WebRTCManager mounted');

    // Инициализируем глобальные переменные
    if (this.localStream) {
      this.STREAM_AUDIO = this.localStream;
      this.STREAM_AUDIO_SAVE = this.localStream;
      this.currentLocalStream = this.localStream;
    }
  },

  beforeDestroy() {
    this.closeAllConnections();
  }
};
</script>

<style scoped>
.webrtc-manager {
  /* Компонент не отображает UI */
}
</style>
