<template>
  <div
    :id="`devVideo_${participant.id}`"
    class="video-participant winVideoRoom1"
    :class="participantClasses"
  >
    <!-- Видео контейнер -->
    <div
      :id="`devDevBlockVideo_${participant.id}`"
      class="video-container divDevBlockOper"
      @click="onVideoClick"
    >
      <!-- Canvas для BodyPix -->
      <canvas
        v-if="showCanvas"
        :id="`canvas_${participant.id}`"
        ref="canvas"
        :class="canvasClasses"
        :style="canvasStyle"
      ></canvas>

      <!-- Видео элемент -->
      <video
        v-if="hasVideo"
        :id="`video_${participant.id}`"
        ref="video"
        :class="videoClasses"
        autoplay
        playsinline
        :style="videoStyle"
        @loadedmetadata="onVideoLoaded"
      ></video>

      <!-- Аудио элемент (скрытый) -->
      <audio
        v-if="hasAudio"
        :id="`audio_${participant.id}`"
        ref="audio"
        autoplay
        :volume="audioVolume"
        :muted="audioMuted"
        hidden
      ></audio>

      <!-- Заглушка, если нет видео -->
      <div
        v-if="!hasVideo || !isVideoActive"
        :id="`devDevIconNoVideo_${participant.id}`"
        class="video-placeholder devDevIconNoVideo"
        :style="{ display: placeholderDisplay }"
      >
        <div
          :id="`imgDevIconNoVideo_${participant.id}`"
          class="avatar imgDevIconNoVideoTextDev"
          :style="avatarStyle"
        >
          {{ nameInitials }}
        </div>
        <div
          :id="`noImgDevIconNoVideo_${participant.id}`"
          class="avatar-text devDevIconNoVideoTextDev"
        >
          {{ nameInitials }}
        </div>

        <div v-if="!isConnected" class="connecting-overlay">
          {{ $t('grp-call.connecnt.user') }}
        </div>
      </div>

      <!-- Индикатор аудио трансляции -->
      <div
        v-if="isTranslating"
        :id="`devAudioTranslate_${participant.id}`"
        class="translate-indicator divAudioTranslate"
        @click.stop="stopTranslation"
      >
        <svg class="translate-icon" viewBox="0 0 512 512">
          <!-- SVG path (сохранен из оригинала) -->
          <g>
            <path d="M113,170H51.803C43.074,170,36,177.236,36,185.965v137.962C36,332.645,43.074,340,51.803,340H113" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="119" x2="119" y1="186" y2="323"/>
            <line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="172.543" y2="49.319"/>
            <line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="338.936" y2="462.16"/>
            <path d="M265.58,446.629" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <path d="M265.58,65.382" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <path d="M253.715,49.311c21.258,0,38.285,17.24,38.285,38.511v336.346c0,21.28-17.146,38.521-38.403,38.521" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="252" x2="252" y1="71" y2="442"/>
            <path d="M323.125,183.086c39.704,0,71.861,32.167,71.861,71.859s-32.157,71.87-71.861,71.87" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <path d="M359.066,138.013c64.574,0,116.934,52.346,116.934,116.933c0,64.585-52.359,116.944-116.934,116.944" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
            <ellipse ry="250" rx="250" cy="250" cx="250" stroke="#000" fill="#c07819" class="blinkAudoiAT"/>
          </g>
        </svg>
      </div>

      <!-- Статус трансляции -->
      <div
        v-if="isTranslating"
        :id="`spanStatusTranslate_${participant.id}`"
        class="translate-status spanSTra"
      >
        <span :id="`spanStatusTranslateText_${participant.id}`" class="spanSTraText"></span>
      </div>

      <!-- Блок функций оператора -->
      <div
        v-if="isOperator && !isExternalUser"
        :id="`devOperFunc_${participant.id}`"
        class="operator-functions devVideoBlockFunc"
      >
        <button
          :id="`devBtnOperFunc_${participant.id}`"
          class="control-btn operator-btn v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type5"
          :title="$t('gc.butWin4')"
          @click.stop="showOperatorMenu"
        >
          <i :id="`iconOperFunc_${participant.id}`" class="fas fa-ellipsis-v"></i>
          <i :id="`iconOperFunc1_${participant.id}`" class="fas fa-slash slash-icon-1"></i>
          <i :id="`iconOperFunc2_${participant.id}`" class="fas fa-slash slash-icon-2"></i>
        </button>
      </div>

      <!-- Индикаторы состояния -->
      <div class="status-indicators">
        <!-- Индикатор микрофона -->
        <div
          :id="`micVoiseActive_${participant.id}`"
          class="mic-indicator cssSoundBars"
          :class="{'mic-muted': !isMicActive, 'mic-speaking': isSpeaking}"
          @click.stop="toggleMic"
        >
          <!-- Аудио полосы -->
          <div class="audio-bars">
            <div
              :id="`micVoise1_${participant.id}`"
              class="bar barCssSound"
              :style="barStyle(0)"
            ></div>
            <div
              :id="`micVoise2_${participant.id}`"
              class="bar barCssSound"
              :style="barStyle(1)"
            ></div>
            <div
              :id="`micVoise3_${participant.id}`"
              class="bar barCssSound"
              :style="barStyle(2)"
            ></div>
          </div>

          <!-- Иконка микрофона -->
          <i
            :id="`iconUserMicVideo_${participant.id}`"
            :class="micIconClass"
          ></i>
        </div>

        <!-- Индикатор качества соединения -->
        <div
          v-if="connectionQuality < 3"
          :id="`minBitrateVideo_${participant.id}`"
          class="quality-indicator minBitrateVideo"
          :class="`quality-${connectionQuality}`"
        >
          <i class="mdi mdi-signal-cellular-1"></i>
        </div>

        <!-- Иконка выключенного микрофона -->
        <span
          v-if="!isMicActive"
          :id="`micVideo_${participant.id}`"
          class="mic-off-icon fa fas fa-microphone-alt-slash v-btn_ot_stats_sp ma-2 infoSpanWin1"
          style="color: red"
          aria-hidden="true"
        ></span>

        <!-- Индикатор записи аудио -->
        <div
          v-if="isRecordingAudio"
          :id="`micVoiseSaveAT_${participant.id}`"
          class="record-indicator multi-ripple"
        >
          <div class="cAT"></div>
          <div class="cAT"></div>
        </div>

        <!-- Флаг записи аудио -->
        <div
          v-if="isRecordingAudio"
          :id="`micVoiseSaveATflag_${participant.id}`"
          class="record-flag multi-rippleDiv"
        ></div>
      </div>

      <!-- Панель управления -->
      <transition name="fade">
        <div
          v-if="showControls"
          :id="`devBlockVideo_${participant.id}`"
          class="control-panel devVideoBlock1"
          @mouseleave="hideControls"
        >
          <!-- Кнопка микрофона -->
          <button
            v-if="canControlMic"
            :id="`devBlockVideoBtn1_${participant.id}`"
            class="control-btn mic-btn v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type1"
            :title="$t('gc.butWin1')"
            @click.stop="toggleMic"
          >
            <i :class="micControlIcon"></i>
          </button>

          <!-- Кнопка закрепления -->
          <button
            v-if="!isScreenShare && canPin"
            :id="`devBlockVideoBtn3_${participant.id}`"
            class="control-btn pin-btn v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type3"
            :title="$t('gc.butWin3')"
            @click.stop="togglePin"
          >
            <i :id="`iconMainVideo_${participant.id}`" :class="pinIconClass"></i>
            <i :id="`iconMainVideo1_${participant.id}`" class="fas fa-slash slash-icon-1"></i>
            <i :id="`iconMainVideo2_${participant.id}`" class="fas fa-slash slash-icon-2"></i>
          </button>

          <!-- Кнопка полноэкранного режима -->
          <button
            v-if="isScreenShare"
            :id="`devBlockVideoBtn4_${participant.id}`"
            class="control-btn fullscreen-btn v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type3"
            :title="$t('gc.butWin5')"
            @click.stop="toggleFullscreen"
          >
            <i :id="`iconMainVideo4_${participant.id}`" class="fa fa-expand-arrows-alt"></i>
          </button>

          <!-- Кнопка закрытия -->
          <button
            v-if="canClose"
            :id="`devBlockVideoBtn2_${participant.id}`"
            class="control-btn close-btn v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type2"
            :title="$t('gc.butWin2')"
            @click.stop="closeParticipant"
          >
            <i class="fa fa-times"></i>
          </button>
        </div>
      </transition>
    </div>

    <!-- Информация о участнике -->
    <div :id="`devDevBlockVideoInfo_${participant.id}`" class="participant-info-wrapper">
      <!-- Блок подключения -->
      <div
        v-if="!isConnected"
        :id="`devDevBlockConnect_${participant.id}`"
        class="connecting-block devDevBlockConnectcss"
      >
        <p class="pdivVideoConndectcss">{{ $t('grp-call.connecnt.user') }}</p>
      </div>

      <!-- Основная информация -->
      <div class="participant-info winVideoRoomInfo row">
        <!-- Все индикаторы из status-indicators -->
        <div class="info-content">
          <span class="participant-name pdivVideoRow">{{ displayName }}</span>
          <span v-if="isScreenShare" class="screen-share-badge">
            {{ $t('gc.scrS') }}
          </span>
        </div>
      </div>
    </div>

    <!-- Попап меню оператора -->
    <operator-menu
      v-if="showOperatorPopup"
      :participant="participantData"
      @close="showOperatorPopup = false"
      @action="handleOperatorAction"
    />
  </div>
</template>

<script>
export default {
  name: 'VideoParticipant',

  props: {
    // Основные данные участника
    participant: {
      type: Object,
      required: true,
      validator: (value) => {
        return value.id && value.name;
      }
    },

    // Состояние участника
    isMicActive: {
      type: Boolean,
      default: true
    },
    isVideoActive: {
      type: Boolean,
      default: true
    },
    isConnected: {
      type: Boolean,
      default: false
    },
    isSpeaking: {
      type: Boolean,
      default: false
    },
    isTranslating: {
      type: Boolean,
      default: false
    },
    isRecordingAudio: {
      type: Boolean,
      default: false
    },
    isPinned: {
      type: Boolean,
      default: false
    },
    isFullscreen: {
      type: Boolean,
      default: false
    },
    connectionQuality: {
      type: Number,
      default: 5,
      validator: value => value >= 1 && value <= 5
    },

    // Флаги
    isExternalUser: Boolean,
    isScreenShare: Boolean,
    isOperator: Boolean,
    isFunctionOperator: Boolean,
    hasBodyPix: Boolean,

    // Разрешения
    canControlMic: {
      type: Boolean,
      default: true
    },
    canPin: {
      type: Boolean,
      default: true
    },
    canClose: {
      type: Boolean,
      default: true
    },

    // Настройки
    audioVolume: {
      type: Number,
      default: 1.0,
      validator: value => value >= 0 && value <= 1
    },
    showAudioBars: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      showControls: false,
      controlsTimeout: null,
      showOperatorPopup: false,
      isMouseOver: false,
      audioLevels: [0, 0, 0],
      audioAnalyser: null,
      animationFrame: null,
      mediaStream: null,

      // Для совместимости с оригинальным кодом
      originalClasses: {
        winVideoRoom1: true,
        divDevBlockOper: true,
        devDevIconNoVideo: true,
        devVideoBlockFunc: true,
        devVideoBlock1: true
      }
    };
  },

  computed: {
    // Классы компонента с оригинальными именами
    participantClasses() {
      return {
        ...this.originalClasses,
        'pinned': this.isPinned,
        'fullscreen': this.isFullscreen,
        'screen-share': this.isScreenShare,
        'operator': this.isOperator,
        'speaking': this.isSpeaking,
        'disconnected': !this.isConnected,
        'external-user': this.isExternalUser,
        'winVideoRoomDevMy': this.participant.isLocal,
        'screenVideoN': this.isScreenShare || this.isOperator
      };
    },

    canvasClasses() {
      return {
        'video-canvas': true,
        'winVideovideoCanvas': true,
        'p1video': true,
        'bodypix-overlay': this.hasBodyPix,
        'visible': this.showCanvas
      };
    },

    videoClasses() {
      return {
        'video-element': true,
        'winVideovideo': true,
        'mirrored': this.participant.isMirror,
        'contain-mode': this.isScreenShare,
        'clVideoMirror': this.participant.isMirror,
        'screenSharingCss': this.isScreenShare,
        'screenVideoN': this.isScreenShare || this.isOperator
      };
    },

    // Стили
    canvasStyle() {
      if (this.isOperator && !this.isExternalUser) {
        return {
          zIndex: 15,
          position: 'absolute',
          background: 'transparent'
        };
      }
      return {};
    },

    videoStyle() {
      if (this.isScreenShare) {
        return { objectFit: 'contain' };
      }
      return {};
    },

    placeholderDisplay() {
      return this.hasVideo && this.isVideoActive ? 'none' : 'flex';
    },

    avatarStyle() {
      if (this.participant.avatarColor) {
        return {
          backgroundColor: this.participant.avatarColor,
          backgroundImage: this.participant.avatarUrl ?
            `url(${this.participant.avatarUrl})` : 'none'
        };
      }
      return {};
    },

    // Данные для отображения
    displayName() {
      if (this.isScreenShare) {
        return this.participant.name.replace('scrsXR_', '');
      }
      return this.participant.name;
    },

    nameInitials() {
      const name = this.participant.name || '';
      return name
        .split(' ')
        .map(word => word[0])
        .join('')
        .toUpperCase()
        .substring(0, 2);
    },

    // Иконки
    micIconClass() {
      const baseClass = 'fa v-btn_ot_ex_sp v-span-video';
      return {
        [baseClass]: true,
        'fa-microphone': this.isMicActive && !this.isSpeaking,
        'fa-microphone-alt': this.isMicActive && this.isSpeaking,
        'fa-microphone-slash': !this.isMicActive
      };
    },

    micControlIcon() {
      return {
        'fa': true,
        'fa-microphone': !this.isMicActive,
        'fa-microphone-slash': this.isMicActive
      };
    },

    pinIconClass() {
      return {
        'fa': true,
        'fa-thumbtack': !this.isPinned
      };
    },

    // Флаги отображения
    showCanvas() {
      return this.hasBodyPix && this.hasVideo;
    },

    hasVideo() {
      return this.participant.hasCamera && this.isVideoActive;
    },

    hasAudio() {
      return true; // Всегда есть аудио трек
    },

    // Данные для оператора
    participantData() {
      return {
        ...this.participant,
        isMicActive: this.isMicActive,
        isVideoActive: this.isVideoActive,
        isConnected: this.isConnected
      };
    }
  },

  methods: {
    // Получение элемента по ID
    getElement(id) {
      return document.getElementById(`${id}_${this.participant.id}`);
    },

    // Показать/скрыть элемент
    showElement(elementId) {
      const el = this.getElement(elementId);
      if (el) el.style.display = 'block';
    },

    hideElement(elementId) {
      const el = this.getElement(elementId);
      if (el) el.style.display = 'none';
    },

    // Управление видео
    onVideoClick() {
      if (!this.isPinned && !this.isScreenShare) {
        this.$emit('pin', this.participant.id);
      }
    },

    onVideoLoaded() {
      this.$emit('video-loaded', this.participant.id);
    },

    // Управление микрофоном
    toggleMic() {
      if (this.canControlMic) {
        this.$emit('toggle-mic', {
          id: this.participant.id,
          name: this.participant.name,
          isOperator: this.isOperator
        });
      }
    },

    // Управление закреплением
    togglePin() {
      if (this.canPin) {
        this.$emit('toggle-pin', this.participant.id);
      }
    },

    // Полноэкранный режим
    toggleFullscreen() {
      this.$emit('toggle-fullscreen', this.participant.id);
    },

    // Закрытие участника
    closeParticipant() {
      if (this.canClose) {
        this.$emit('close', {
          id: this.participant.id,
          name: this.participant.name
        });
      }
    },

    // Управление оператором
    showOperatorMenu() {
      this.showOperatorPopup = true;
    },

    handleOperatorAction(action) {
      this.$emit('operator-action', {
        participantId: this.participant.id,
        action: action
      });
      this.showOperatorPopup = false;
    },

    // Управление трансляцией
    stopTranslation() {
      this.$emit('stop-translation', this.participant.id);
    },

    // Управление контролами
    showControlsWithDelay() {
      clearTimeout(this.controlsTimeout);
      this.showControls = true;
    },

    hideControls() {
      this.controlsTimeout = setTimeout(() => {
        if (!this.isMouseOver) {
          this.showControls = false;
        }
      }, 1000);
    },

    // Совместимость с оригинальным кодом
    setupOriginalEventListeners() {
      const videoElement = this.$refs.video;
      const infoElement = this.getElement('devDevBlockVideoInfo');

      if (videoElement) {
        videoElement.addEventListener('click', () => {
          if (!this.isPinned) {
            this.$emit('video-clicked', this.participant.id);
          }
        });
      }

      if (infoElement) {
        infoElement.addEventListener('click', () => {
          if (!this.isPinned) {
            this.$emit('info-clicked', this.participant.id);
          }
        });

        // Мышка над элементом
        if (!this.isFirefox) {
          infoElement.addEventListener('mousemove', (e) => {
            const delHMax = e.srcElement.clientHeight - e.srcElement.clientHeight / 4;
            const delHMin = e.srcElement.clientHeight / 4;
            const delWMax = e.srcElement.clientWidth - e.srcElement.clientWidth / 4;
            const delWMin = e.srcElement.clientWidth / 4;
            const x = e.offsetX;
            const y = e.offsetY;

            if (delHMin < y && y < delHMax && delWMin < x && x < delWMax) {
              this.onIn();
            } else {
              this.onOut();
            }
          });
        }
      }
    },

    onIn() {
      this.isMouseOver = true;
      this.showControlsWithDelay();
    },

    onOut() {
      this.isMouseOver = false;
      this.hideControls();
    },

    // Анализ аудио
    setupAudioAnalysis() {
      if (this.audioAnalyser) {
        this.audioAnalyser.disconnect();
      }

      if (this.mediaStream) {
        const audioContext = new (window.AudioContext || window.webkitAudioContext)();
        this.audioAnalyser = audioContext.createAnalyser();
        const source = audioContext.createMediaStreamSource(this.mediaStream);
        source.connect(this.audioAnalyser);

        this.startAudioMonitoring();
      }
    },

    startAudioMonitoring() {
      if (!this.showAudioBars) return;

      const updateAudioLevels = () => {
        if (!this.audioAnalyser) return;

        const dataArray = new Uint8Array(this.audioAnalyser.frequencyBinCount);
        this.audioAnalyser.getByteFrequencyData(dataArray);

        const average = dataArray.reduce((a, b) => a + b) / dataArray.length;
        const normalized = Math.min(average / 128, 1);

        // Обновляем уровни с плавной анимацией
        this.audioLevels = this.audioLevels.map((level, index) => {
          const target = normalized * (1 - index * 0.3);
          return level + (target - level) * 0.1;
        });

        this.animationFrame = requestAnimationFrame(updateAudioLevels);
      };

      this.animationFrame = requestAnimationFrame(updateAudioLevels);
    },

    barStyle(index) {
      const height = Math.max(10, this.audioLevels[index] * 40);
      return {
        height: `${height}px`,
        opacity: this.audioLevels[index] * 0.8 + 0.2
      };
    },

    // Управление медиа потоком
    setMediaStream(stream) {
      this.mediaStream = stream;

      if (this.$refs.video) {
        this.$refs.video.srcObject = stream;
      }

      if (this.$refs.audio) {
        const audioTracks = stream.getAudioTracks();
        if (audioTracks.length > 0) {
          const audioStream = new MediaStream([audioTracks[0]]);
          this.$refs.audio.srcObject = audioStream;
        }
      }

      if (this.showAudioBars) {
        this.setupAudioAnalysis();
      }
    },

    // Обновление состояния из родителя
    updateState(state) {
      Object.keys(state).forEach(key => {
        if (key in this.$data) {
          this[key] = state[key];
        }
      });
    },

    // Получение DOM элементов для родительского компонента
    getVideoElement() {
      return this.$refs.video;
    },

    getAudioElement() {
      return this.$refs.audio;
    },

    getCanvasElement() {
      return this.$refs.canvas;
    },

    // Очистка
    cleanup() {
      if (this.animationFrame) {
        cancelAnimationFrame(this.animationFrame);
      }

      if (this.audioAnalyser) {
        this.audioAnalyser.disconnect();
      }

      if (this.mediaStream) {
        this.mediaStream.getTracks().forEach(track => track.stop());
      }

      if (this.$refs.video) {
        this.$refs.video.srcObject = null;
      }

      if (this.$refs.audio) {
        this.$refs.audio.srcObject = null;
      }

      clearTimeout(this.controlsTimeout);
    }
  },

  watch: {
    showAudioBars: {
      immediate: true,
      handler(newVal) {
        if (newVal && this.mediaStream) {
          this.setupAudioAnalysis();
        } else if (this.animationFrame) {
          cancelAnimationFrame(this.animationFrame);
        }
      }
    },

    participant: {
      deep: true,
      handler(newParticipant) {
        if (newParticipant.mediaStream && newParticipant.mediaStream !== this.mediaStream) {
          this.setMediaStream(newParticipant.mediaStream);
        }
      }
    },

    isMicActive(newVal) {
      // Обновление отображения иконки микрофона
      this.hideElement('micVideo');
      if (!newVal) {
        this.showElement('micVideo');
      }
    },

    isVideoActive(newVal) {
      // Обновление отображения видео
      if (this.$refs.video) {
        this.$refs.video.style.display = newVal ? 'block' : 'none';
      }
    }
  },

  mounted() {
    // Инициализация событий мыши
    const container = this.$el.querySelector('.video-container');
    if (container) {
      container.addEventListener('mouseenter', () => {
        this.onIn();
      });

      container.addEventListener('mouseleave', () => {
        this.onOut();
      });
    }

    // Установка начального медиа потока
    if (this.participant.mediaStream) {
      this.setMediaStream(this.participant.mediaStream);
    }

    // Настройка оригинальных слушателей событий
    this.setupOriginalEventListeners();

    // Инициализация скрытых элементов
    this.hideElement('micVideo');
    this.hideElement('micVoiseSaveAT');
    this.hideElement('micVoiseSaveATflag');
    this.hideElement('minBitrateVideo');
    this.hideElement('micVoiseActive');
    this.hideElement('devDevIconNoVideo');
    this.hideElement('iconMainVideo1');
    this.hideElement('iconMainVideo2');
    this.hideElement('iconOperFunc1');
    this.hideElement('iconOperFunc2');
    this.hideElement('devBlockVideoBtn4');
    this.hideElement('devAudioTranslate');
    this.hideElement('spanStatusTranslate');

    if (this.$refs.canvas) {
      this.$refs.canvas.style.display = 'none';
    }
  },

  beforeDestroy() {
    this.cleanup();
  }
};
</script>

<style scoped>
/* Сохраняем оригинальные классы */
.winVideoRoom1 {
  position: relative;
  width: 320px;
  height: 240px;
  border-radius: 8px;
  overflow: hidden;
  background: #1a1a1a;
  margin: 4px;
}

.divDevBlockOper {
  position: relative;
  width: 100%;
  height: 100%;
}

.devDevIconNoVideo {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  z-index: 5;
}

.imgDevIconNoVideoTextDev {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  font-weight: bold;
  color: white;
  background-size: cover;
  background-position: center;
}

.devVideoBlockFunc {
  position: absolute;
  top: 8px;
  right: 8px;
  z-index: 20;
}

.devVideoBlock1 {
  position: absolute;
  bottom: 8px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 4px;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 20px;
  padding: 4px;
  backdrop-filter: blur(4px);
  z-index: 30;
}

/* Оригинальные стили кнопок */
.v-btn_ot_ex {
  min-width: 32px !important;
  height: 32px !important;
  padding: 0 !important;
}

.v-btn_ot_ex_sp {
  font-size: 14px !important;
}

.v-span-video {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Типы кнопок */
.type1 { background: rgba(255, 255, 255, 0.1) !important; }
.type2 { background: rgba(244, 67, 54, 0.7) !important; }
.type3 { background: rgba(255, 255, 255, 0.1) !important; }
.type5 { background: rgba(255, 255, 255, 0.1) !important; }

.type1:hover { background: rgba(255, 255, 255, 0.2) !important; }
.type2:hover { background: rgba(244, 67, 54, 0.9) !important; }
.type3:hover { background: rgba(255, 255, 255, 0.2) !important; }
.type5:hover { background: rgba(255, 255, 255, 0.2) !important; }

/* Иконки слэшей */
.slash-icon-1 {
  position: absolute;
  font-size: 14px;
  color: #dee1e5;
  margin-top: 5px;
}

.slash-icon-2 {
  position: absolute;
  font-size: 14px;
  color: red;
}

/* Видео и canvas */
.winVideovideoCanvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.winVideovideo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  background: #2d2d2d;
}

.p1video {
  z-index: 10;
}

/* Аудио индикаторы */
.cssSoundBars {
  display: flex;
  align-items: flex-end;
  gap: 2px;
  height: 20px;
  cursor: pointer;
}

.barCssSound {
  width: 3px;
  background: #4CAF50;
  transition: height 0.1s ease;
  border-radius: 1px;
}

/* Микрофон */
.infoSpanWin1 {
  position: absolute;
  top: 8px;
  left: 8px;
  z-index: 15;
}

.minBitrateVideo {
  position: absolute;
  top: 8px;
  left: 40px;
  z-index: 15;
}

/* Трансляция */
.divAudioTranslate {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 25;
  cursor: pointer;
}

.spanSTra {
  position: absolute;
  bottom: 40px;
  left: 0;
  right: 0;
  text-align: center;
  z-index: 25;
}

.spanSTraText {
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
}

/* Анимация пульсации для записи */
.multi-ripple {
  position: absolute;
  top: 8px;
  right: 40px;
  z-index: 15;
}

.multi-rippleDiv {
  position: absolute;
  top: 8px;
  right: 40px;
  z-index: 16;
}

.blinkAudoiAT {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { opacity: 0.7; }
  50% { opacity: 1; }
  100% { opacity: 0.7; }
}

/* Подключение */
.devDevBlockConnectcss {
  position: absolute;
  bottom: 40px;
  left: 0;
  right: 0;
  text-align: center;
  background: rgba(0, 0, 0, 0.7);
  padding: 8px;
}

.pdivVideoConndectcss {
  color: white;
  margin: 0;
  font-size: 12px;
}

/* Информация о участнике */
.winVideoRoomInfo {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 40px;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  padding: 0 8px;
  z-index: 10;
}

.pdivVideoRow {
  color: white;
  font-size: 12px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  flex: 1;
}

/* Особые классы */
.winVideoRoomDevMy {
  border: 2px solid #2196F3;
}

.screenVideoN .winVideovideo {
  object-fit: contain !important;
}

.clVideoMirror {
  transform: scaleX(-1);
}

.screenSharingCss {
  border: 2px solid #FF9800;
}

/* Анимации */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Адаптивность */
@media (max-width: 768px) {
  .winVideoRoom1 {
    width: 100%;
    height: 200px;
  }
}
</style>
