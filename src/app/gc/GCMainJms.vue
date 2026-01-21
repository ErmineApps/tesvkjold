<template>
  <!-- Главный контейнер -->
  <div id="gcMain" style="height: 100%; width: 100%; top: 0; position: fixed; z-index: 200;" :class="containerClasses">

    <div class="v-toolbar__content" style="height: 48px;"><img src="/img/IKSAR-Logo-Rus-Yellow-Dot-white.d8fae3af.png"
                                                               height="26" class="pr-3 ml-3"></div>

    <!-- Динамический фон, который ИСЧЕЗАЕТ при полной загрузке -->
    <transition name="fade-out">
      <div
        v-if="showBackgroundOverlay"
        class="background-overlay"
        :class="backgroundClass"
      ></div>
    </transition>

    <!-- Затемнение фона для фокуса на контенте -->
    <!-- УБИРАЕМ shouldDimBackground - это некорректное условие -->
<!--    <div v-if="shouldShowStartStage" class="background-dim light"></div>-->
<!--    <div v-else-if="shouldShowSessionStage" class="background-dim dark"></div>-->

    <!-- Основной контент ВСЕГДА виден -->
    <div v-if="!isCallSessionActive" class="main-content">

      <!-- Контент с плавными переходами -->
      <transition name="page-fade" mode="out-in">

        <!-- Состояние 1: Нет активной ссылки -->
        <div
          v-if="shouldShowNoLinkMessage"
          key="no-link"
          class="state-container no-link-state"
        >
        </div>

        <!-- Состояние 2: Предзагрузка -->
        <div
          v-else-if="isPreloading"
          key="preloading"
          class="state-container preloading-state"
        >
          <div class="loading-card">
            <div class="loading-spinner large">
              <div class="spinner"></div>
            </div>
            <h2 class="loading-title">{{ loadingTitle }}</h2>
            <p class="loading-description">{{ loadingDescription }}</p>
            <div class="loading-progress" v-if="loadingProgress > 0">
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: loadingProgress + '%' }"
                ></div>
              </div>
              <span class="progress-text">{{ Math.round(loadingProgress) }}%</span>
            </div>
          </div>
        </div>

        <!-- Состояние 3: Стартовый экран -->
        <GCStartStage
          v-else-if="shouldShowStartStage"
          key="start-stage"
          ref="startStage"
          :roomId="roomId"
          :name-room-big-gr="nameRoomBigGr"
          :users-call-list="usersCallList"
          :sender-name.sync="senderName"
          :pw-user.sync="pwUser"
          :is-pw="isPw"
          :name-room="nameRoom"
          :usersCallList="usersCallList"
          :is-firefox="isFirefox"
          :is-spin-start="isSpinStart"
          :info-data-date="infoDataDate"
          :videoValue="videoValue"
          @startCallGC="startCallGC"
          @clickDialogSett="clickDialogSett"
          @toggle-mic="toggleMic"
          @toggle-video="toggleVideo"
        />

        <!-- Состояние 4: Активная сессия -->
        <div
          v-else-if="shouldShowSessionStage"
          key="session-stage"
          class="session-stage-container"
        >


        </div>

      </transition>
    </div>

    <!-- Уведомления -->
    <transition-group name="notification-slide" tag="div" class="notifications-container">
      <div
        v-for="notification in activeNotifications"
        :key="notification.id"
        :class="['notification', `notification-${notification.type}`]"
        @click="dismissNotification(notification.id)"
      >
        <div class="notification-icon">
          <i :class="notification.icon"></i>
        </div>
        <div class="notification-content">
          <h4 class="notification-title">{{ notification.title }}</h4>
          <p class="notification-message">{{ notification.message }}</p>
        </div>
        <XrBtn
          :round="true"
          small
          class="notification-close"
          icon="fas fa-times"
          @click.stop="dismissNotification(notification.id)"
        />
      </div>
    </transition-group>



    <div id="mainRoom" class="cardPositionGC xr2gc">

      <button id="butDisNone" class="ff12New">
        <span class="mdi mdi-grid-off v-btn_ot_ex_sp v-span-video" aria-hidden="true"></span>
      </button>

      <div v-if="isVideoBackground" class="cardPositionGCPrev xr-gc-video-background">
        <div class="text-center pos100GC videoPrevData">
          <!--          <video-background :src="require(`@/assets/icons/mmIksarGCMS.mp4`)"-->
          <!--          url('/mdb/xrfonts/Rooftop-Regular-Web.ttf') format('truetype');-->

          <video-background :src="'/mdb/audio/mmIksarGCMS.mp4'"
                            :loop="false"
                            style="max-height: 100%; height: 100%;"
                            @ended="endedVideoBackground"></video-background>
        </div>
      </div>

      <div v-if="!isActiveLink && !isPreloading && !isCallSessionActive" class="mx-auto">
        <p class="pNoLink" v-t="'gc.mess.no-link-available'"></p>
      </div>

      <!-- ############################################################################ -->
      <!-- Стартовая страница                                                           -->
      <!-- ############################################################################ -->
      <div v-if="isActiveLink && !isPreloading && !isCallSessionActive" class="pos100GC">
        <v-row id="divMainpos100GC" class="posH100">
          <v-col id="colMainpos100GC" cols="12" class="posH100">
            <v-row style="display: none" class="posH100" :align="'center'" :justify="'center'">

              <!-- ============================================================== -->
              <!-- Левая часть -->
              <v-card id="vcardId1" class="vCard-1" outlined tile>
                <video id="videoPre" autoplay="" data-uid="31" class="pos100GC videoPrevDiv"
                       width="320" height="240">
                </video>

                <div v-if="!isStartVideo || !hasCamera" id="devIconNoVideoPre" class="devDevIconNoVideoPre"/>

                <!-- Кнопки -------------------------------------->
                <v-row class="divBtnPreGC">
                  <!-- Кнопка: Микрофон -->
                  <v-btn color="white" fab
                         class="mr-1 btnFooterMic"
                         :title="isStartMic ? $t('gc.func.disable-mic') : $t('gc.func.play-mic')"
                         @click="setStartMic">
                    <v-icon v-if="isStartMic" style="font-size: 16px;">fas fa-microphone-alt</v-icon>
                    <v-icon v-if="!isStartMic" style="font-size: 16px;">fas fa-microphone-alt-slash</v-icon>
                  </v-btn>
                  <!-- Кнопка: Камера -->
                  <v-btn color="white" fab
                         class="mr-1 btnFooterVid"
                         :title="hasCamera ? (isStartVideo ? $t('gc.func.disable-camera') : $t('gc.func.play-camera')) : $t('ex.error.video')"
                         @click="setStartVideo">
                    <v-icon v-if="isStartVideo && hasCamera" style="font-size: 16px;">fas fa-video</v-icon>
                    <v-icon v-else style="font-size: 16px;">fas fa-video-slash</v-icon>
                  </v-btn>
                </v-row>
                <!-- Кнопка: Настройка -->
                <v-btn v-if="!isFirefox" color="white"
                       class="btnSetting"
                       :title="$t('settings')"
                       @click="clickDialogSett">
                  <v-icon style="font-size: 16px;">fas fa-sliders-h</v-icon>
                </v-btn>
                <!------------------------------------------------>

                <p v-if="!isDeviceYes || !hasCamera" class="pErrorDevices">{{ this.$t('ex.error.video') }}</p>
                <v-progress-circular
                  v-if="isDownVideo && isDeviceYes"
                  class="mainCircular"
                  :size="50"
                  color="xr2L1"
                  indeterminate
                ></v-progress-circular>
                <p v-if="isDownVideo && isDeviceYes" class="progText">{{ this.$t('dw_video') }}</p>
              </v-card>

              <!-- ============================================================== -->
              <!-- Правая часть -->
              <v-card id="vcardId2" class="vCard-2" outlined tile>
                <v-card-title>
                  <span style="margin-left: 8px">{{ $t('gc.prev.title') }}</span>
                </v-card-title>

                <v-card-text class="pa-0">

                  <!-- Наименование группы -->
                  <!--                  <div id="userNameHeader" class="pl-1 pr-1 mb-0 xr2D2 xr2L2&#45;&#45;text">-->
                  <div id="userNameHeader" class="pl-1 pr-1 mb-0 toolbar-accent">
                    <p class="pWidthRoom">{{ nameRoomBigGr }}</p>
                  </div>

                  <!-- Текущие активные пользователи -->
                  <div class="userList pl-2 pr-2 mt-0">
                    <template v-if="isUserCallList">
                      <v-list disabled color="xr2L4" class="mt-0">
                        <v-list-item v-for="(item, i) in usersCallList" :key="i" dense>
                          <img v-if="!item.isScrs" :src="item.avatar" class="infoUserLinsIkonImgGC">

                          <v-icon v-if="item.isScrs" class="infoUserLinsIkonScrsGC">fa fa-chalkboard-teacher</v-icon>

                          <div v-if="item.isOper" class="infoUserOperImgGC">
                            <v-icon
                              size="14"
                              style="stroke: green; stroke-width: 10px; color: green"
                            >fas fa-mobile-alt</v-icon>
                          </div>
                          <v-list-item-title>
                            {{ i + 1 }}. {{ item.senderName }}
                          </v-list-item-title>
                        </v-list-item>
                      </v-list>
                    </template>
                    <template v-else>
                      <v-alert text outlined color="xr2D2" icon="mdi-fire" class="ma-3 pa-2">
                        <!-- В данном групповом звонке пока нет активных пользователей. Вы будете первым. -->
                        {{ $t('gc.no-members-alert') }}
                      </v-alert>
                    </template>
                  </div>

                </v-card-text>

                <!-- Футер правой части -->
                <div class="right-footer">

                  <hr class="v-divider xr2L1 mt-1 mb-2"/>

                  <!-- Имя пользователя (вводим руками) -->
                  <v-row>
                    <v-text-field
                      height="36px"
                      v-model="senderName"
                      v-if="!issenderName"
                      :prepend-icon="'mdi-account-outline'"
                      :label="$t('gc.name-reg')"
                      outlined
                      placeholder=" "
                      required
                      :max-length="400"
                      :rules="[rules.required]"
                    />
                  </v-row>

                  <v-row>
                    <!-- Пароль для входа (если требуется) -->
                    <v-col class="mr-4">
                      <v-text-field
                        height="36px"
                        v-model="pwUser"
                        v-if="isPw"
                        :prepend-icon="'mdi-shield-key-outline'"
                        :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                        :type="show2 ? 'text' : 'password'"
                        name="newPassword"
                        :label="$t('password') + ' *'"
                        persistent-hint
                        @click:append="show2 = !show2"
                        outlined
                        placeholder=" "
                        autocomplete="new-password"
                        :rules="[rules.required]"
                      />
                    </v-col>
                    <!-- Кнопка: Присоединиться -->
                    <v-col cols="auto">
                      <XrBtn
                        :icon="isSpinStart ? 'mdi-spin mdi-loading' : 'fas fa-phone'"
                        class="btnCallPrev mb-2"
                        @click="startCallGC"
                        type="hot"
                        :text="$t('gc.prev.btn.ready')"
                        :title="$t('gc.prev.btn.ready.title')">
                      </XrBtn>
                    </v-col>
                  </v-row>

                </div>

              </v-card>
            </v-row>
          </v-col>

        </v-row>
      </div>
      <!-- ############################################################################ -->

      <div id="idnewMyVideoClass" class="newMyVideoClass"></div>

      <v-row v-if="isCallSessionActive" id="participants" style="height: 100%; background: #3c3c3d">

        <div id="divTestRow" class="vDivTestRow">
          <v-row id="idVrowPosition1" class="vRowMin16Video1">
          </v-row>
          <v-row id="idVrowPosition2" class="vRowMin16Video2">
          </v-row>
          <v-row id="idVrowPosition3" class="vRowMin16Video3">
          </v-row>
          <v-row id="idVrowPosition4" class="vRowMin16Video4">
          </v-row>
        </div>

        <div id="devMiniVideos" class="devMinivideos"></div>
        <div v-if="isShowInfo" class="infoDevShow">
          <p class="pInfoTool">{{ usersCall }}</p>

          <v-list>
            <v-list-item-group class="info-list-users">
              <v-list-item
                v-for="(item, i) in itemsUsersInfo"
                :key="i"
                disabled
                class="info-item-users"
              >
                <v-list-item-icon class="divinfo2ISpan">
                  <v-icon :color=item.color v-text="item.icon" class="divinfo2I"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title v-text="item.text" style="color: black" :color=item.colorText></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
          <p class="pInfoTool" v-t="'gc.info.title'"></p>
          <p class="pInfoData">{{ nameRoomBigGr }}</p>
          <p class="pInfoData">{{ infoDataDate }}</p>
          <p class="pInfoData">{{ infoDataLive }}</p>
          <p class="pInfoData">{{ infoDataOU }}</p>
          <p class="pInfoData"></p>
          <p v-if="isDeveloper" class="pInfoData">{{ levelRTT }} <i id="idLevelRTT" class="fas fa-signal"
                                                                    style="font-size: 14px;"></i></p>

        </div>

        <v-row id="idRowInfo" class="pa-6 text-center infoTooldevPanel">
          <TimerGroupCall ref="timerGroupCall"></TimerGroupCall>
          <p class="containerGNane" :title="nameRoomBig">&nbsp;{{ nameRoomBig }}</p>
        </v-row>

        <div id="idDivLocalVideo" class="iddivlocalvideo">
          <div id="devDevIconNoVideoMain1" class="devDevIconNoVideo">
            <img id="imgDevIconNoVideoMain1" class="imgDevIconNoVideoTextDev" style="display: none">
            <div id="noImgDevIconNoVideoMain1" class="devDevIconNoVideoTextDev" style="display: none">{{ miniName }}
            </div>
          </div>
        </div>

        <!--          <v-row id="idRowInfo" class="pa-6 text-center grey lighten-2 rounded-bl-xl infoTooldev"-->
        <!--                 style="border: solid 1px rgba(0, 0, 0, 0.21);"-->
        <!--                 @mouseover="infoOver"-->
        <!--                 @mouseout="infoOut"-->
        <!--          >-->
        <!--            <div class="divinfo2">-->
        <!--              <TimerGroupCall ref="timerGroupCall"></TimerGroupCall>-->
        <!--              &lt;!&ndash;        <div id="time-node" class="container2"><p>{{timeNode}}</p></div>&ndash;&gt;-->
        <!--              <div class="divQO3Z3c"></div>-->
        <!--              <div id="divInfoNameRoom" class="container3">-->
        <!--                <p style="pointer-events: none" :title="nameRoomBig">{{ nameRoom }}</p>-->
        <!--              </div>-->
        <!--            </div>-->

        <!--            <div class="divinfo2">-->
        <!--              <v-btn class="divinfo2Btn" @click="clickInfoShow">-->
        <!--            <span class="fa-layers fa-fw">-->
        <!--              <i class="fas fa-user-tag" style="font-size: 16px;"></i>-->
        <!--              <span class="fa-layers-counter"-->
        <!--                    style="-->
        <!--                margin-left: 2px;-->
        <!--                padding-top: -6px;-->
        <!--                font-size: 18px;-->
        <!--                color: #00aa47;">{{ numUsers }}-->
        <!--              </span>-->
        <!--            </span>-->
        <!--              </v-btn>-->
        <!--            </div>-->
        <!--            <div class="divQO3Z3c"></div>-->

        <!--            <TimerGroupCall ref="timerGroupCall"></TimerGroupCall>-->
        <!--            &lt;!&ndash;        <div id="time-node" class="container2"><p>{{timeNode}}</p></div>&ndash;&gt;-->
        <!--            <div class="divQO3Z3c"></div>-->
        <!--            <div id="divInfoNameRoom" class="container3">-->
        <!--              <p style="pointer-events: none" :title="nameRoomBig">{{ nameRoom }}</p>-->
        <!--            </div>-->
        <!--            <div id="idDivLocalVideo" class="iddivlocalvideo">-->
        <!--              <div id="devDevIconNoVideoMain1" class="devDevIconNoVideo1">-->
        <!--                <img id="imgDevIconNoVideoMain1" class="imgDevIconNoVideoTextDev" style="display: none">-->
        <!--                <div id="noImgDevIconNoVideoMain1" class="devDevIconNoVideoTextDev" style="display: none">{{miniName}}</div>-->
        <!--              </div>-->
        <!--            </div>-->
        <!--          </v-row>-->
      </v-row>

      <v-icon class="noVisIcon">mdi-star-outline</v-icon>

      <RoomXRCallWinJms ref="roomXRCallWin" :isCallSessionActive="isCallSessionActive" :isStartCall="isStartCall" :isUserLic="isUserLic"
                     :wsURL="wsURL" :isExternalUser="isExternalUser"
                     :hasCamera="hasCamera" :hasMic="hasMic" :isMirror="isMirror" :numUsers="numUsers"
                     :numUsersExternal="numUsersExternal" :isDegradation="isDegradation" :isFirefox="isFirefox"
                     :avatar="avatar" :itemsUsersInfo="itemsUsersInfo" :paramValueRoom="roomID"
                     :is-micro-with-space-bar="isMicroWithSpaceBar"
                     @dialogSettCall="onDlgSettCall" @showFuncOper="showFuncOper" @closeFuncOper="closeFuncOper"
                     @onLevelRTT="onLevelRTT" @addOperDialog="addOperDialog" @soundRecAT="soundRecAT"
                     @updateSettUser="updateSettUser" @addUserChat="addUserChat" @showChat="showChat"
                     @setTranslation="setTranslation" @updateDataOper="updateDataOper"
                     @openChikPanel="openChikPanel"
                     @devLog="devLog" @isFullScreen="setFullScreen" @sendScreenshotToChat="sendScreenshotToChat"></RoomXRCallWinJms>


      <Confirm ref="refConfirm" @agree="confirmAgree" @cancel="confirmCancel"></Confirm>

      <StreamMask await ref="vueStreamRef" :backgroundBlurAmount="backgroundBlurAmount"
                  :videoConstraints="videoConstraints" :background="urlBackgroundFon" :isCall="isCallSessionActive"
                  :senderName="senderNameReplace"></StreamMask>

      <FuncOperator ref="funcOperator" @sendMessage="sendMessageCall" :isExternalUser="isExternalUser" @setPaint="setPaint" @setArrow="setArrow"
                    @funcUpdateIconMenu="funcUpdateIconMenu" @saveScreenshot="saveScreenshot"></FuncOperator>

    </div>

    <ChatMessageGC ref="chatMessage" :isExternalUser="isExternalUser" :room="roomID" :senderId="senderId" :wsURL="wsURL"
                   @chatIsPlan="chatIsPlan" @clickCloseChat="clickCloseChat" @sendBlobAudioTranslate="sendBlobAudioTranslate"
                   @sendMessage="chatSendMessage" @sendTextTranslate="sendTextTranslate"
                   @soundRecAT="soundRecAT" @stopNoTranslate="stopNoTranslate"></ChatMessageGC>

    <DialogSettingsCall ref="dialogSettCall" :hasCamera="hasCamera" :isExternalUser="isExternalUser"
                        @getValue="valueSett"  @onDegradation="onDegradation" @onErrorDevices="onErrorDevices"
                        @onSaveSettingCall="saveSettingCall" @onSaveSettingMain="saveSettingMain" @setBackground="setBackground">
    </DialogSettingsCall>

    <LinkUserInfo ref="linkUserInfo" :numUsers="numUsers" :itemsUsersInfo="itemsUsersInfo" :isExternalUser="isExternalUser" :itemsUsersInfoExternal="itemsUsersInfoExternal"
                  :numUsersExternal="numUsersExternal" @addNewOper="addOperDialog" @clickCloseUser="clickCloseUser"
                  @addUser="addUser" @disUser="disUser" @clickMic="clickMic"
                  @clickCloseInfoUser="clickCloseInfoUser"></LinkUserInfo>

    <NotificationExternalUser ref="notificationExternalUser" :itemsUsersInfoExternal="itemsUsersInfoExternal"
                              @acceptUser="addUser" @rejectUser="disUser"></NotificationExternalUser>

    <ChikPanel ref="chikPanel" :mediaAttr="mediaAttr" @onclose="onChikPanelClose"></ChikPanel>

    <DialogAddOperLink v-if="isDialogListOper" ref="contactOperatorFormVue"
                       @cancel="isDlgContactOper = false"
                       @emitAddUser="emitAddUser"/>

    <NoConnectInfoPanel ref="noConnectInfoPanel"></NoConnectInfoPanel>




  </div>
</template>

<script>
import GCStartStage from '@/app/gcjms/components/GCStartStage.vue';
import XrBtn from '@/app/global/items/XrBtn.vue';

// ИМПОРТИРУЕМ this.signalingService
import SignalingService from '@/app/gcjms/services/SignalingService';


const URL_GET_GC_URL = `/groupCall/getGCURL`;

import bowserLib from 'bowser'

import av_gif from '@/assets/img/gifLoadGC.gif';
import RoomXRCallWinJms from "@/app/gc/RoomXRCallWinJms";
import $ from "jquery";

import StreamMask from "@/app/gc/func/StreamMask";
import DialogSettingsCall from "@/app/gc/func/DialogSettingsCall";
import Confirm from "@/app/gc/func/Confirm";
import ChatMessageGC from "@/app/gc/func/right/ChatMessageGC";
import FuncOperator from "@/app/gc/func/FuncOperator";
import TimerGroupCall from "@/app/gc/func/TimerGroupCall";

import expertRU from '@/lang/expert-ru.json'
import expertEN from '@/lang/expert-en.json'
import DialogAddOperLink from "@/app/gc/dialog/DialogAddOperLink";
import LinkUserInfo from "@/app/gc/func/right/LinkUserInfo";
import ChikPanel from "@/app/gc/func/right/ChikPanel";
import NoConnectInfoPanel from "@/app/gc/func/NoConnectInfoPanel";
import NotificationExternalUser from "@/app/gc/func/right/NotificationExternalUser.vue";

var This

let STREAM_VIDEO = null
let STREAM_VIDEO_FON = null;
let STREAM_AUDIO = null;

const URL_GET_GET_GC_URL = `/groupCall/getGCURL`


export default {
  name: 'GCMainJms',

  components: {
    DialogAddOperLink,
    StreamMask,
    TimerGroupCall,
    ChatMessageGC,
    FuncOperator,
    LinkUserInfo,
    ChikPanel,
    DialogSettingsCall,
    RoomXRCallWinJms,
    Confirm,
    NotificationExternalUser,
    NoConnectInfoPanel,
    GCStartStage,
    XrBtn
  },

  data() {
    return {
      signalingService: null,
      localParticipantMessage: {},
      // WebSocket состояние (больше не нужно, будет в SignalingService)
      hasUserLicense: false,

      isAdmin: false,

      // Состояние соединения
      connectionQuality: 'good',

      paramValueLang: this.$route.params.lang,
      roomId: this.$route.params.gcId,
      paramValueUserId: '',
      senderNameReplace: '',
      senderId: '',

      // Основные состояния приложения

      senderName: '',

      usersCallList: [],
      infoDataDate: '',


      // Другие состояния...
      showBackgroundOverlay: true,
      backgroundTimer: null,
      isMicEnabledStart: true,
      isCameraEnabledStart: true,

      nameRoomBig: '',
      usersCall: '',
      isShowInfo: false,

      infoDataLive: '',
      infoDataOU: '',

      debugInfo: '',

      // Медиа состояния
      isStreamMic: true,
      isStreamVideo: true,
      isScreenSharing: false,
      activeParticipantsCount: 0,

      initialParticipants: [],

      // Загрузка
      loadingProgress: 0,
      loadingTitle: 'Подготовка сессии...',
      loadingDescription: 'Проверка устройств и подключение',
      isRetrying: false,



      // Уведомления
      notifications: [],
      notificationIdCounter: 0,
      showDebugPanel: false,

      // Таймеры
      timeoutAllUsers: null,
      isClickClose: false,
      joinRoomMessageNewWS: null,

      // Для signaling
      userId: '',

      externalUsers: [],
      showExternalUserNotification: false,

      showLocalVideoPip: false,
      ro: true,

      isFullScreen: false,

      timeoutId: null,
      timeoutUsers: null,


      ws: null, // основной WebSocket для работы системы
      urlWS: '',
      isActiveLink: false, // доступна ли ссылка, ответ от сервера
      isPreloading: true, // подгрузка страницы
      isAvtoStart: false, // автоматически подключать прользователя
      isAvtoStartNew: false, // автоматически подключать прользователя
      dataLinkInfo: null, // информация по ссылке, получаем от сервера

      isFirefox: false, // определяем браузер, если firefox то false
      numScreenSharinWin: 0, // кол-во активных расшариваний экрана
      isSoundWinFunc: true, // отправляет ли пользователь звук

      nameRoom: 'нет имени', // имя группы
      nameRoomGr: this.$t('gc.group'), // имя группы
      nameRoomBigGr: this.$t('gc.group'), // длиное имя группы

      issenderName: true, // есть ли имя пользователя
      isUserLic: true, // пользователь зарегестрированный

      isCallSessionActive: false, // страница в режиме звонка
      isrep: false, // для дублирования имен

 roomID: this.$route.params.gcId, // данные о id группы
 senderIdold: '',

      numUsersCallList: 0,
      isUserCallList: false,

      isStartCall: false,
      joinRoomMessage: {},



      gcLinkInput: this.$t('gc.link.input'),


      serverUrl: '',
      wsURL: '',
      isDevUrl: false,


      //isStreamMic: true, // мой вкл.выкл микрофон
      //isStreamVideo: true, // мое вкл.выкл видео

      typeMaket: 1, // тип макета 1 - много, 2 - одно большое, 3 - много с моим видео
      isTypeWin: false, // отображение выбора макета

      //isShowInfo: false, // отображение подробной информации по ссылке
      numUsers: 0, // кол-во пользователей
      numUsersExternal: 0,
      isDownVideo: true,
      // isDialogSett: false,

      audioInputValue: null, // настройки - выбор микрофона
      audioOutputValue: null, // настройки - выбор динамика
      videoValue: null, // настройки - выбор камеры
      isMicroWithSpaceBar: false, // настройки - включить микрофон по пробелу

      videoQualtiySendValue: null,
      videoQualtiyRecvValue: null,

      isXrOper: false, //  пока спорная переменная
      isXrOperVid: false, // пока спорная переменная

      isAddFunc: false, // для отображения дополнительных функций
      //isScreenSharing: false, // для вкл\выкл расшаривания экрана
      wsScreen: null, // веб сокет для расшаривания экрана

      isMyVideo: false, // показывать мое видео вместе со всеми
      isDegradation: false, // размытие видео
      isDegradationSett: false, // размытие видео

      isJobFuncOper: false,//включение функции, окно оператора будет на главном экране для всех пользователей
      isDrawLine: false,// функция рисования

      isSound: false, // статус что я говорю, не отправлять запрос
      backgroundBlurAmount: 1, // размытие экрана, props размытия

      isAddToAll: false, // показывать меня со всеми вместе

      show2: false,
      isPw: false, // нужен ли пароль
      pw: '',
      pwUser: '',
      teamSound: true, // переменная для дельты времени говорящего

      av_gif_: av_gif,

      isVS_SS: true, // костыль, что бы убирать видео, если не нужно
      timeChackSession: 0, // каждые 10 сек определять жизнь сессии.

      typeBrowser: '',

      itemsUsersInfo: [],
      itemsUsersInfoExternal: [],
      numUserOfline: 0,

      videoConstraints: false,

      isDeveloper: false,
      isdevUser: false,
      avatar: '',
      levelRTT: '',
      urlBackgroundFon: '',

      isOneUser: true,
      showMiniInfo: false,
      miniName: '',
      isSpinStart: false,
      numRepetition: 1,
      senderNameOldRep: '',
      deltaCanvas: 1.7,
      wDraw: 0,
      gDraw: 0,
      oDraw: 'L',

      isDialogListOper: false,
      isDeviceYes: true,
      userEcpectNew: {},

      hasCamera: true,
      hasMic: true,
      isMirror: true,

      isStartMic: true,
      isStartVideo: true,
      isVideoBackground: true,// превью при запуске ссылки

      isDebug: false,// локальная разработка
      isExternalUser: false, // пользователь внешний
      isTranslateFunc: false, // доступна ли функция перевода для этой ссылки
      isDevGC: false, // режим разработки ссылки ГЗ

      mediaAttr: {} // Для чек-листов
    };
  },

  computed: {
    shouldShowStartStage() {
      return this.isActiveLink && !this.isPreloading && !this.isCallSessionActive;
    },

    shouldShowSessionStage() {
      return this.isCallSessionActive;
    },

    // Берем статус соединения из SignalingService
    connectionStatus() {
      return this.signalingService.connectionStatus || 'disconnected';
    },

    connectionStatusText() {
      const status = this.connectionStatus;
      const texts = {
        disconnected: 'Нет соединения',
        connecting: 'Подключение...',
        connected: 'Соединение установлено',
        reconnecting: 'Переподключение'
      };
      return texts[status] || 'Неизвестно';
    },

    connectionIcon() {
      const icons = {
        'connected': 'fas fa-wifi text-success',
        'connecting': 'fas fa-spinner fa-spin',
        'reconnecting': 'fas fa-sync-alt fa-spin text-warning',
        'disconnected': 'fas fa-wifi-slash text-danger'
      };
      return icons[this.connectionStatus] || 'fas fa-question-circle';
    },

    // Ваши локальные computed
    shouldShowNoLinkMessage() {
      return !this.isActiveLink && !this.isPreloading && !this.isCallSessionActive;
    },

    isInitialLoadComplete() {
      return !this.isPreloading && this.isActiveLink;
    },

    backgroundClass() {
      if (this.isPreloading) {
        return 'background-loading';
      } else if (this.shouldShowStartStage) {
        return 'background-start';
      }
      return '';
    },

    shouldDimBackground() {
      return this.shouldShowStartStage || this.shouldShowSessionStage;
    },

    activeNotifications() {
      return this.notifications.slice(-3);
    },

    containerClasses() {
      return {
        'gc-main-container': true,
        'has-video-background': this.isVideoBackground,
        'in-session': this.isCallSessionActive,
        'developer-mode': this.isDeveloper
      };
    }
  },

  watch: {
    // Следим за состоянием соединения
    numUsersCallList: {
      handler: function () {
        console.log('watch usersCallList '+this.numUsersCallList)
      }
    },

    isMirror: {
      handler: function () {
        if (this.isMirror) {
          console.log('mirror watch t')
          $(`#videoPre`).addClass('clVideoMirror')
        } else {
          console.log('mirror watch f')
          $(`#videoPre`).removeClass('clVideoMirror')
        }
      },
    connectionStatus(newStatus, oldStatus) {
      if (newStatus !== oldStatus) {
        this.handleConnectionStatusChange(newStatus);
      }
    }
  }
  },

    destroyed() {
      window.onbeforeunload = undefined
      This.setLocalStorage("gc.update.error", 'f')

      This.closeCall()
      console.log("destroyed ")
      //window.close()
    },

    beforeRouteLeave(to, from, next) {
      This.closeCall()
      //window.close()
    },

    create() {
      this.$root.setLocaleModule(expertRU, expertEN)
    },

  mounted() {
    // Создаем новый экземпляр для этой вкладки
    this.signalingService = new SignalingService();
    // Инициализируем через новый метод
    console.log('🚀 Начало инициализации GCMain');
    this.initialize();

    This = this
    document.title = this.$t('gc.prev.title')

    this.$root.setLocaleModule(expertRU, expertEN)

    // $(window).on('load', () => This.getBrowser().then((is) => {
    //   if(is){
    //     This.setMounted()
    //   }else{
    //     console.log('load false')
    //     This.waitingLoad()
    //   }
    // }))

    this.gcLinkInput = this.$t('gc.link.input')

    $('#idCanvasPrint').hide()

    setTimeout(() => {
      This.setMounted()
      This.getBrowser()
    }, 0)

    setTimeout(()=>{
      if(this.isVideoBackground){
        this.isVideoBackground=false
      }
    }, 3000)
  },

  beforeUnmount() {
    this.cleanup();
  },

  methods: {


    dismissNotification(id) {
      const index = this.notifications.findIndex(n => n.id === id);
      if (index !== -1) {
        this.notifications.splice(index, 1);
      }
    },



    cleanupMediaStreams() {
      // Остановка всех медиапотоков
      if (this.$refs.localVideoPip && this.$refs.localVideoPip.srcObject) {
        this.$refs.localVideoPip.srcObject.getTracks().forEach(track => track.stop());
      }

      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.cleanup();
      }
    },


    cleanup() {
      // Отписываемся от всех событий SignalingService
      this.signalingService.removeAllListeners();

      // Отключаемся от сервера
      this.signalingService.disconnect();

      // Очищаем таймеры
      clearTimeout(this.backgroundTimer);
      clearTimeout(this.timeoutAllUsers);
    },

    checkBrowser() {
      const userAgent = navigator.userAgent.toLowerCase();
      this.isFirefox = userAgent.includes('firefox');

      const browserInfo = this.getBrowserInfo();
      this.typeBrowser = browserInfo.browserName + ' v.' + browserInfo.version;
    },

    handleInitializationError(error) {
      console.error('Ошибка инициализации:', error);
      this.isPreloading = false;
      this.isActiveLink = false;

      this.showNotification({
        type: 'error',
        title: 'Ошибка загрузки',
        message: 'Не удалось подготовить систему видеозвонка',
        icon: 'fas fa-exclamation-triangle',
        duration: 0
      });
    },

    // ==================== НОВАЯ ИНИЦИАЛИЗАЦИЯ ====================
    async initialize() {
      try {
        console.log('🚀 Начало инициализации GCMain');

        // 1. Проверяем браузер
        this.checkBrowser();
        console.log('✅ Браузер проверен');

        // 2. Загружаем данные комнаты
        console.log('📡 Загружаем данные комнаты...');
        const roomData = await this.loadRoomData();
        console.log('✅ Данные комнаты загружены:', {
          roomId: this.roomId,
          userId: this.userId,
          senderName: this.senderName
        });

        // 3. Инициализируем SignalingService
        console.log('🔗 Инициализируем SignalingService...', roomData);
        await this.initializeSignaling(roomData);
        console.log('✅ SignalingService инициализирован');

        // 4. Приложение готово
        this.isPreloading = false;
        console.log('🎉 GCMain успешно инициализирован');

        this.showNotification({
          type: 'success',
          title: 'Готово',
          message: 'Система видеозвонка загружена',
          icon: 'fas fa-check-circle',
          duration: 2000
        });

      } catch (error) {
        console.error('❌ Ошибка инициализации:', error);
        this.isPreloading = false;

        // Базовая обработка ошибок
        this.showNotification({
          type: 'error',
          title: 'Ошибка загрузки',
          message: error.message || 'Не удалось загрузить приложение',
          icon: 'fas fa-exclamation-triangle',
          duration: 0
        });
      }
    },

    async loadRoomData() {
      console.log(`📡 Загрузка данных комнаты ${this.roomId}...`);

      const url = `${URL_GET_GC_URL}/${this.roomId}/${this.paramValueLang}`;

      try {
        const res = await this.$root.restGet(url);
        console.log('📊 Ответ сервера:', res);

        if (res.ErrorGC === 0) {
          // Сохраняем данные
          this.senderName = res.senderName;
          this.roomId = res.gcID;
          this.userId = res.userId;
          this.nameRoom = res.nameRoom;
          this.isExternalUser = res.isExternalUser;
          this.avatar = res.avatar;
          this.isTranslateFunc = res.isTranslate;
          this.isDevGC = res.isDevGC;
          this.isAdmin = res.isAdmin;
          this.hasUserLicense = res.hasUserLicense;

          console.log('this.roomId '+this.roomId)

          // Возвращаем данные для SignalingService
          return {
            wsURL: res.wsURL,
            isDev: res.isDevGC
          };

        } else {
          console.error('❌ Ошибка сервера:', res.TextError);
          throw new Error(res.TextError || 'Ошибка загрузки данных комнаты');
        }

      } catch (error) {
        console.error('❌ Ошибка загрузки данных:', error);
        throw error;
      }
    },

    async initializeSignaling(roomData = {}) {
      try {

        console.log('🔗 Инициализация SignalingService...',roomData);
        var url = roomData.wsURL
        if (roomData.isDev) {
          console.log('initWebSocket isDebug url = ' + `ws://localhost:8882/ms/groupcall`)
          this.urlWS = `ws://localhost:8882/ms/groupcall`;
        } else {
          console.log('initWebSocket url = ' + `${url}/ms/groupcall`)

          var hostname = window.location.host;
          console.log("host "+hostname)

          if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
            url = 'wss://'+window.location.host
            console.log("location.hostname "+hostname)
          }
          this.urlWS = `${url}/ms/groupcall`;
        }

        this.setupMessageListeners();

        await this.signalingService.connect(this.roomId, this.userId, this.urlWS, this.paramValueLang, roomData.isDev);


        console.log('Подключено к сигнальному серверу');

      } catch (error) {
        console.error('Ошибка подключения:', error);
      }
    },

    // ==================== ПРОСЛУШКА СООБЩЕНИЙ ====================

    // setupMessageListeners() {
    //   console.log('Компактная настройка обработчиков...');
    //
    //   // Маппинг событий на методы
    //   const eventToMethodMap = {
    //     // Статус пользователей
    //     'isUsers': 'statusUserCall',
    //     'isUsLink': 'statusUsersMain',
    //     'isUsLinkAll': 'statusUsers',
    //
    //     // WebRTC события
    //     'iceCandidate': 'handleIceCandidate',
    //     'existingParticipants': 'handleExistingParticipants',
    //     'newParticipantArrived': 'handleNewParticipant',
    //     'participantLeft': 'handleParticipantLeft',
    //
    //     // Ошибки и исключения
    //     'userRepetition': 'userRepetition',
    //     'joinRoomException': 'joinRoomException',
    //
    //     // Управление звонком
    //     'timeCall': 'handleTimeCall',
    //     'stopCall': 'handleStopCall',
    //     'isLinkAvailable': 'handleLinkAvailable',
    //
    //     // Медиа события
    //     'isMicUser': 'handleMicUser',
    //     'isSoundUser': 'handleSoundUser',
    //     'isVideoUser': 'handleVideoUser',
    //     'receiveVideoAnswer': 'handleVideoAnswer',
    //     'isMicToUser': 'handleMicToUser',
    //     'live': 'handleLive',
    //
    //     // Рисование
    //     'isStartDraw': 'handleStartDraw',
    //     'moveDraw': 'handleMoveDraw',
    //     'drawMode': 'handleDrawMode',
    //     'arrowDrawOther': 'handleArrowDraw',
    //     'moveDrawOther': 'handleMoveDrawOther',
    //     'drawModeOther': 'handleDrawModeOther',
    //     'clearCanvas': 'handleClearCanvas',
    //     'clearCanvasOther': 'handleClearCanvasOther',
    //
    //     // Функциональные операции
    //     'funcOperOther': 'handleFuncOperOther',
    //     'saveCall': 'handleSaveCall',
    //     'minBit': 'handleMinBit',
    //     'closeAllFuncOper': 'handleCloseAllFuncOper',
    //
    //     // Чат
    //     'chatSendMessage': 'handleChatMessage',
    //     'chatNewMessageOper': 'handleChatMessageOper',
    //     'chatUpdateMessage': 'handleChatUpdate',
    //
    //     // Перевод
    //     'asrtTranlsateResultOne': 'handleTranslateResultOne',
    //     'asrtTranlsateResult': 'handleTranslateResult',
    //     'asrtTranlsateResultError': 'handleTranslateError',
    //     'asrtTranlsateAudioVosk': 'handleTranslateAudioVosk',
    //     'soundRecAT': 'handleSoundRecAT',
    //     'newMessageAT': 'handleNewMessageAT',
    //     'asrtTranlsateResultAudio': 'handleTranslateResultAudio',
    //     'sendTextTranslate': 'handleTextTranslate',
    //     'asrtTranlsateNoResult': 'handleTranslateNoResult',
    //
    //     // Устройства и Google Cloud
    //     'deviceParam': 'handleDeviceParam',
    //     'MESS_WS_NO_GC': 'handleNoGC',
    //     'updateGcFileId': 'handleUpdateGcFileId',
    //
    //     // Планы и скриншоты
    //     'setPlanEl': 'handleSetPlanEl',
    //     'sendMyPlan': 'handleSendMyPlan',
    //     'sendScreenshotOper': 'handleScreenshotOper',
    //
    //     // Лимиты
    //     'exceededQuantity': 'handleExceededQuantity'
    //   };
    //
    //   // Автоматическая регистрация
    //   Object.entries(eventToMethodMap).forEach(([event, methodName]) => {
    //     const handler = this[methodName];
    //
    //     if (typeof handler === 'function') {
    //       // Создаем bound версию обработчика
    //       const boundHandler = handler.bind(this);
    //
    //       // Подписываемся на КОНКРЕТНЫЙ экземпляр
    //       this.signalingService.on(event, boundHandler);
    //       console.log(`✅ Подписан на ${event} (${methodName})`);
    //     } else {
    //       console.warn(`⚠️ Метод ${methodName} для события ${event} не найден`);
    //
    //       // Создаем заглушку
    //       this.signalingService.on(event, (message) => {
    //         console.log(`[Заглушка] ${event}:`, message);
    //       });
    //     }
    //   });
    //
    //   // Общие события - тоже используем this.signalingService
    //   this.signalingService.on('connected', () => {
    //     console.log('✅ Соединение установлено для этой вкладки');
    //     this.isConnected = true;
    //   });
    //
    //   this.signalingService.on('disconnected', (event) => {
    //     console.log('❌ Соединение разорвано для этой вкладки:', event);
    //     this.isConnected = false;
    //   });
    //
    //   this.signalingService.on('error', (error) => {
    //     console.error('🚨 Ошибка сигнального сервиса в этой вкладке:', error);
    //   });
    //
    //   console.log('✅ Все обработчики настроены для этой вкладки');
    // },

    setupMessageListeners() {
      console.log('🔄 Настройка обработчиков со стрелочными функциями...');

      if (!this.signalingService) {
        console.error('❌ signalingService не инициализирован');
        return;
      }

      // ОЧИЩАЕМ все предыдущие обработчики
      this.signalingService.removeAllListeners();

      // ==================== СТАТУС ПОЛЬЗОВАТЕЛЕЙ ====================
      this.signalingService.on('isUsers', (message) => {
        console.log('📞 Получен isUsers:', message);
        this.statusUserCall(message);
      });

      this.signalingService.on('isUsLink', (message) => {
        console.log('👥 Получен isUsLink:', message);
        this.statusUsersMain(message);
      });

      this.signalingService.on('isUsLinkAll', (message) => {
        console.log('👥👥 Получен isUsLinkAll:', message);
        this.statusUsers(message);
      });

      // ==================== WEBRTC СОБЫТИЯ ====================
      this.signalingService.on('iceCandidate', (message) => {
        console.log('❄️ Получен iceCandidate:', message);
        this.handleIceCandidate(message);
      });

      this.signalingService.on('existingParticipants', (message) => {
        console.log('👥 Получен existingParticipants:', message);
        this.handleExistingParticipants(message);
      });

      this.signalingService.on('newParticipantArrived', (message) => {
        console.log('🆕 Получен newParticipantArrived:', message);
        this.handleNewParticipant(message);
      });

      this.signalingService.on('participantLeft', (message) => {
        console.log('👋 Получен participantLeft:', message);
        this.handleParticipantLeft(message);
      });

      // ==================== ОШИБКИ И ИСКЛЮЧЕНИЯ ====================
      this.signalingService.on('userRepetition', (message) => {
        console.log('🔄 Получен userRepetition:', message);
        this.userRepetition(message);
      });

      this.signalingService.on('joinRoomException', (message) => {
        console.log('🚫 Получен joinRoomException:', message);
        this.joinRoomException(message);
      });

      // ==================== УПРАВЛЕНИЕ ЗВОНКОМ ====================
      this.signalingService.on('timeCall', (message) => {
        console.log('⏰ Получен timeCall:', message);
        this.handleTimeCall(message);
      });

      this.signalingService.on('stopCall', (message) => {
        console.log('🛑 Получен stopCall:', message);
        this.handleStopCall(message);
      });

      this.signalingService.on('isLinkAvailable', (message) => {
        console.log('🔗 Получен isLinkAvailable:', message);
        this.handleLinkAvailable(message);
      });

      // ==================== МЕДИА СОБЫТИЯ ====================
      this.signalingService.on('isMicUser', (message) => {
        console.log('🎤 Получен isMicUser:', message);
        this.handleMicUser(message);
      });

      this.signalingService.on('isSoundUser', (message) => {
        console.log('🔊 Получен isSoundUser:', message);
        this.handleSoundUser(message);
      });

      this.signalingService.on('isVideoUser', (message) => {
        console.log('📹 Получен isVideoUser:', message);
        this.handleVideoUser(message);
      });

      this.signalingService.on('receiveVideoAnswer', (message) => {
        console.log('📺 Получен receiveVideoAnswer:', message);
        this.handleVideoAnswer(message);
      });

      this.signalingService.on('isMicToUser', (message) => {
        console.log('🎤→👤 Получен isMicToUser:', message);
        this.handleMicToUser(message);
      });

      this.signalingService.on('live', (message) => {
        console.log('🔴 Получен live:', message);
        this.handleLive(message);
      });

      // ==================== РИСОВАНИЕ ====================
      this.signalingService.on('isStartDraw', (message) => {
        console.log('🎨 Получен isStartDraw:', message);
        this.handleStartDraw(message);
      });

      this.signalingService.on('moveDraw', (message) => {
        console.log('↔️ Получен moveDraw:', message);
        this.handleMoveDraw(message);
      });

      this.signalingService.on('drawMode', (message) => {
        console.log('🖌️ Получен drawMode:', message);
        this.handleDrawMode(message);
      });

      this.signalingService.on('arrowDrawOther', (message) => {
        console.log('🏹 Получен arrowDrawOther:', message);
        this.handleArrowDraw(message);
      });

      this.signalingService.on('moveDrawOther', (message) => {
        console.log('↔️ Получен moveDrawOther:', message);
        this.handleMoveDrawOther(message);
      });

      this.signalingService.on('drawModeOther', (message) => {
        console.log('🖌️ Получен drawModeOther:', message);
        this.handleDrawModeOther(message);
      });

      this.signalingService.on('clearCanvas', (message) => {
        console.log('🗑️ Получен clearCanvas:', message);
        this.handleClearCanvas(message);
      });

      this.signalingService.on('clearCanvasOther', (message) => {
        console.log('🗑️ Получен clearCanvasOther:', message);
        this.handleClearCanvasOther(message);
      });

      // ==================== ФУНКЦИОНАЛЬНЫЕ ОПЕРАЦИИ ====================
      this.signalingService.on('funcOperOther', (message) => {
        console.log('⚙️ Получен funcOperOther:', message);
        this.handleFuncOperOther(message);
      });

      this.signalingService.on('saveCall', (message) => {
        console.log('💾 Получен saveCall:', message);
        this.handleSaveCall(message);
      });

      this.signalingService.on('minBit', (message) => {
        console.log('📊 Получен minBit:', message);
        this.handleMinBit(message);
      });

      this.signalingService.on('closeAllFuncOper', (message) => {
        console.log('❌ Получен closeAllFuncOper:', message);
        this.handleCloseAllFuncOper(message);
      });

      // ==================== ЧАТ ====================
      this.signalingService.on('chatSendMessage', (message) => {
        console.log('💬 Получен chatSendMessage:', message);
        this.handleChatMessage(message);
      });

      this.signalingService.on('chatNewMessageOper', (message) => {
        console.log('💬 Получен chatNewMessageOper:', message);
        this.handleChatMessageOper(message);
      });

      this.signalingService.on('chatUpdateMessage', (message) => {
        console.log('✏️ Получен chatUpdateMessage:', message);
        this.handleChatUpdate(message);
      });

      // ==================== ПЕРЕВОД ====================
      this.signalingService.on('asrtTranlsateResultOne', (message) => {
        console.log('🌐 Получен asrtTranlsateResultOne:', message);
        this.handleTranslateResultOne(message);
      });

      this.signalingService.on('asrtTranlsateResult', (message) => {
        console.log('🌐 Получен asrtTranlsateResult:', message);
        this.handleTranslateResult(message);
      });

      this.signalingService.on('asrtTranlsateResultError', (message) => {
        console.log('❌ Получен asrtTranlsateResultError:', message);
        this.handleTranslateError(message);
      });

      this.signalingService.on('asrtTranlsateAudioVosk', (message) => {
        console.log('🎵 Получен asrtTranlsateAudioVosk:', message);
        this.handleTranslateAudioVosk(message);
      });

      this.signalingService.on('soundRecAT', (message) => {
        console.log('🎙️ Получен soundRecAT:', message);
        this.handleSoundRecAT(message);
      });

      this.signalingService.on('newMessageAT', (message) => {
        console.log('📨 Получен newMessageAT:', message);
        this.handleNewMessageAT(message);
      });

      this.signalingService.on('asrtTranlsateResultAudio', (message) => {
        console.log('🎵 Получен asrtTranlsateResultAudio:', message);
        this.handleTranslateResultAudio(message);
      });

      this.signalingService.on('sendTextTranslate', (message) => {
        console.log('📝 Получен sendTextTranslate:', message);
        this.handleTextTranslate(message);
      });

      this.signalingService.on('asrtTranlsateNoResult', (message) => {
        console.log('🚫 Получен asrtTranlsateNoResult:', message);
        this.handleTranslateNoResult(message);
      });

      // ==================== УСТРОЙСТВА И GOOGLE CLOUD ====================
      this.signalingService.on('deviceParam', (message) => {
        console.log('📱 Получен deviceParam:', message);
        this.handleDeviceParam(message);
      });

      this.signalingService.on('MESS_WS_NO_GC', (message) => {
        console.log('☁️ Получен MESS_WS_NO_GC:', message);
        this.handleNoGC(message);
      });

      this.signalingService.on('updateGcFileId', (message) => {
        console.log('📄 Получен updateGcFileId:', message);
        this.handleUpdateGcFileId(message);
      });

      // ==================== ПЛАНЫ И СКРИНШОТЫ ====================
      this.signalingService.on('setPlanEl', (message) => {
        console.log('📋 Получен setPlanEl:', message);
        this.handleSetPlanEl(message);
      });

      this.signalingService.on('sendMyPlan', (message) => {
        console.log('📨 Получен sendMyPlan:', message);
        this.handleSendMyPlan(message);
      });

      this.signalingService.on('sendScreenshotOper', (message) => {
        console.log('📸 Получен sendScreenshotOper:', message);
        this.handleScreenshotOper(message);
      });

      // ==================== ЛИМИТЫ ====================
      this.signalingService.on('exceededQuantity', (message) => {
        console.log('⚠️ Получен exceededQuantity:', message);
        this.handleExceededQuantity(message);
      });

      // ==================== ОБЩИЕ СОБЫТИЯ ====================
      this.signalingService.on('connected', () => {
        console.log('✅ Соединение установлено для этой вкладки');
        this.isConnected = true;
      });

      this.signalingService.on('disconnected', (event) => {
        console.log('❌ Соединение разорвано для этой вкладки:', event);
        this.isConnected = false;
      });

      this.signalingService.on('error', (error) => {
        console.error('🚨 Ошибка сигнального сервиса в этой вкладке:', error);
      });

      console.log('✅ Все обработчики настроены (стрелочные функции)');
    },

    // ==================== ОТПРАВКА СООБЩЕНИЙ ====================


    // ==================== ОБРАБОТЧИКИ СЕССИИ ====================
    handleExistingParticipants(message) {
      console.log('👥 Подключение к GCSessionStrang:', message);

      console.log('👥 this.$refs.sessionStage:', this.$refs.sessionStage);

      // Передаем в сессию
      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.handleExistingParticipants(message, this.senderName);
      } else{
        setTimeout(()=>{this.$refs.sessionStage.handleExistingParticipants(message, this.senderName);}, 2000)
      }
    },

    onNewParticipant(message) {
      console.log('👋 Новый участник:', message);

      // Добавляем участника в список
      const newParticipant = this.mapParticipantToFormat(message.participant);
      this.initialParticipants.push(newParticipant);

      // Передаем в сессию
      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.handleNewParticipant(message);
      }
    },

    onParticipantLeft(message) {
      console.log('🚪 Участник вышел:', message);

      // Удаляем участника из списка
      const index = this.initialParticipants.findIndex(p => p.id === message.participantId);
      if (index !== -1) {
        this.initialParticipants.splice(index, 1);
      }

      // Передаем в сессию
      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.handleParticipantLeft(message);
      }
    },

    onReceiveVideoAnswer(message) {
      console.log('📹 Получен видео ответ:', message);

      // Передаем в WebRTCService через сессию
      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.handleVideoAnswer(message);
      }
    },

    onIceCandidate(message) {
      console.log('🧊 Получен ICE кандидат:', message);

      // Передаем в WebRTCService через сессию
      if (this.$refs.sessionStage) {
        this.$refs.sessionStage.onIceCandidate(message);
      }
    },

    // ==================== ОБРАБОТЧИКИ СОБЫТИЙ ====================
    onStartCall(callData) {
      console.log('📞 Начало звонка:', callData);

      this.isSpinStart = true;

      this.showNotification({
        type: 'info',
        title: 'Подключение',
        message: 'Устанавливаем соединение...',
        icon: 'fas fa-spinner fa-spin',
        duration: 0
      });

      // Создаем локального участника

      this.localParticipantMessage = this.createJoinMessageRegistor(callData);

      // Уведомляем сервер о начале звонка
      this.signalingService.send(this.localParticipantMessage);

      // Запускаем сессию
      setTimeout(() => {
        this.isMicEnabledStart = callData.microphoneEnabled;
        this.isCameraEnabledStart = callData.cameraEnabled;
        this.isSpinStart = false;
        this.isCallSessionActive = true;
        this.activeParticipantsCount = 1;

        this.dismissNotification(this.notificationIdCounter);
      }, 1500);
    },

    onSessionEnded() {
      console.log('🛑 Сессия завершена');

      this.isCallSessionActive = false;
      this.initialParticipants = [];
    },

    onStopCall(message) {
      console.log('⏹️ Звонок остановлен:', message);

      this.isCallSessionActive = false;
      this.initialParticipants = [];

      // Показываем сообщение если кто-то завершил звонок
      if (message.userClose) {
        this.showNotification({
          type: 'info',
          title: 'Звонок завершен',
          message: `${message.userClose} завершил звонок`
        });
      }
    },

    // ==================== ВСПОМОГАТЕЛЬНЫЕ МЕТОДЫ ====================
    mapParticipantsToFormat(participants) {
      return participants.map(participant => this.mapParticipantToFormat(participant));
    },

    mapParticipantToFormat(participant) {
      return {
        id: participant.id || participant.senderId,
        name: participant.name || participant.senderName,
        senderName: participant.senderName || participant.name,
        isLocal: participant.id === this.userId,
        isAudioEnabled: participant.isMic !== false,
        isVideoEnabled: participant.isVideo !== false,
        isSpeaking: false,
        connectionQuality: 'good',
        avatar: participant.avatar || `https://i.pravatar.cc/150?img=${Math.floor(Math.random() * 70)}`,
        initials: this.getInitials(participant.name || participant.senderName),
      };
    },


    handleConnectionStatusChange(status) {
      console.log('📊 Статус соединения изменился:', status);

      switch (status) {
        case 'connected':
          this.showNotification({
            type: 'success',
            title: 'Соединение восстановлено',
            message: 'Подключение к серверу установлено',
            icon: 'fas fa-plug',
            duration: 2000
          });
          break;
        case 'disconnected':
          this.showNotification({
            type: 'warning',
            title: 'Отключено',
            message: 'Соединение с сервером потеряно',
            icon: 'fas fa-wifi-slash',
            duration: 3000
          });
          break;
      }
    },

    getBrowserInfo() {
      const userAgent = navigator.userAgent;
      let browserName = 'Unknown Browser';
      let version = 'Unknown Version';

      // Определение браузера
      if (userAgent.includes('Firefox') && !userAgent.includes('Seamonkey')) {
        browserName = 'Firefox';
        // Ищем версию Firefox
        const match = userAgent.match(/Firefox\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('Seamonkey')) {
        browserName = 'Seamonkey';
        const match = userAgent.match(/Seamonkey\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('Chrome') && !userAgent.includes('Chromium') && !userAgent.includes('Edg') && !userAgent.includes('OPR')) {
        browserName = 'Chrome';
        const match = userAgent.match(/Chrome\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('Chromium')) {
        browserName = 'Chromium';
        const match = userAgent.match(/Chromium\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('Safari') && !userAgent.includes('Chrome') && !userAgent.includes('Chromium')) {
        browserName = 'Safari';
        // Для Safari нужно искать версию иначе
        const match = userAgent.match(/Version\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
        else version = userAgent.match(/Safari\/(\d+)/)?.[1] || version;
      }
      else if (userAgent.includes('OPR') || userAgent.includes('Opera')) {
        browserName = 'Opera';
        const match = userAgent.match(/(?:OPR|Opera)\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('Edg') || userAgent.includes('Edge')) {
        browserName = 'Edge';
        const match = userAgent.match(/(?:Edg|Edge)\/(\d+(\.\d+)?)/);
        if (match) version = match[1];
      }
      else if (userAgent.includes('MSIE') || userAgent.includes('Trident')) {
        browserName = 'Internet Explorer';
        // Для IE это сложнее
        const tridentMatch = userAgent.match(/Trident\/(\d+)/);
        if (tridentMatch) {
          const tridentVersion = parseInt(tridentMatch[1]);
          // Сопоставление Trident с версией IE
          if (tridentVersion === 7) version = '11';
          else if (tridentVersion === 6) version = '10';
          else if (tridentVersion === 5) version = '9';
          else if (tridentVersion === 4) version = '8';
        }
        // Пробуем найти версию IE напрямую
        const ieMatch = userAgent.match(/MSIE (\d+(\.\d+)?)/);
        if (ieMatch) version = ieMatch[1];
      }

      return { browserName, version };
    },


      setFullScreen(isFullScreen){
        this.isFullScreen = isFullScreen
        console.log('УСТАНОВИЛИ isFullScreen',  this.isFullScreen)
      },
      //region Запуск страницы ___________________________________________________________________________________________

      /**region getBrowser оперделяем вид и версию браузера*/
      async getBrowser() {

        var ua = navigator.userAgent;
        console.log("UA ____ ",ua)

        if (ua.search(/Firefox/) > -1) {
          this.isFirefox = true
          alert(this.$t('gc.infoNoBrawser'))
          return false
          //window.close()
        }

        var  isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
        var  isIphone = /iPhone|iPod/i.test(navigator.userAgent);

        if (isMobile || isIphone) {
          // return this.$t('gc.mobile_Browser')

          alert(this.$t('gc.infoNoBrawser'))
          return false
          //window.close()

        }
        else {

          console.log('ua ',ua)

          var bName = function () {

            if (ua.search(/MSIE/) > -1) return "ie";

            if (ua.search(/Firefox/) > -1) return "firefox";

            if (ua.search(/Opera/) > -1) return "opera";

            if (ua.search(/Chrome/) > -1) return "chrome";

            if (ua.search(/Safari/) > -1) return "safari";

            if (ua.search(/Konqueror/) > -1) return "konqueror";

            if (ua.search(/Iceweasel/) > -1) return "iceweasel";

            if (ua.search(/SeaMonkey/) > -1) return "seamonkey";
          }();

          var version = function (bName) {

            switch (bName) {

              case "ie" :
                return (ua.split("MSIE ")[1]).split(";")[0];
                break;

              case "firefox" :
                return ua.split("Firefox/")[1];
                break;

              case "opera" :
                return ua.split("Version/")[1];
                break;

              case "chrome" :
                return (ua.split("Chrome/")[1]).split(" ")[0];
                break;

              case "safari" :
                return (ua.split("Version/")[1]).split(" ")[0];
                break;

              case "konqueror" :
                return (ua.split("KHTML/")[1]).split(" ")[0];
                break;

              case "iceweasel" :
                return (ua.split("Iceweasel/")[1]).split(" ")[0];
                break;

              case "seamonkey" :
                return ua.split("SeaMonkey/")[1];
                break;
            }
          }(bName);

          if (bName == 'chrome' || bName == 'opera') {
            This.typeBrowser = bName + ' v.' + version;
          } else {
            alert(this.$t('gc.infoNoBrawser'))
            return false
            //window.close()
          }
        }

        var browser = bowserLib.getParser(window.navigator.userAgent);
        var browserName = browser.getBrowserName()+
          ' '+browser.getBrowserVersion()+
          ' ('+browser.getOSName()+
          ' '+browser.getOSVersion()+')';
        This.typeBrowser = browserName;

        console.log('This.typeBrowser',This.typeBrowser)

        var hasWebRTC = navigator.getUserMedia ||
          navigator.webkitGetUserMedia ||
          navigator.mozGetUserMedia ||
          navigator.msGetUserMedia;
        if (!hasWebRTC) {
          alert(this.$t('gc.notHasWebRTC'))
        }
        return true
      }
      ,
      /**endregion*/

      /**region setMounted загрузка страницы*/
      setMounted() {

        this.isDebug = this.$root.isDebug()

        console.log("setMounted загрузка страницы")
        console.log("getLocalStorage(gc.update.error ",this.getLocalStorage("gc.update.error"))

        if (this.getLocalStorage("gc.update.error") != null) {
          if(this.getLocalStorage("gc.update.error") === 't'){
            This.isAvtoStartNew = true
          }else{
            This.isAvtoStartNew = false
          }
        }
        this.setLocalStorage("gc.update.error", 'f')

        if (this.getLocalStorage("gc.mirror", 'b') != null) {
          this.isMirror = this.getLocalStorage("gc.mirror", 'b')
        } else {
          this.setLocalStorage("gc.mirror", true)
        }
        console.log('this.getLocalStorage("gc.isAudioPlayATSender")', this.getLocalStorage("gc.isAudioPlayATSender"))
        if (this.getLocalStorage("gc.isAudioPlayATSender") == null) {
          this.setLocalStorage("gc.isAudioPlayATSender", 0)
        }
        if (this.getLocalStorage("gc.langSrc") == '') {
          this.setLocalStorage("gc.langSrc", 'RU')
        }
        if (this.getLocalStorage("gc.langTgtr") == '') {
          this.setLocalStorage("gc.langTgt", 'EN')
        }

        var url = `${URL_GET_GET_GC_URL}/${this.roomID}/${this.paramValueLang}`
        console.log('url',url)
        this.$root.restGet(url).then((res) => {
          try {
            console.log('getGCURL res')
            console.log(res)
            console.log(res.wsURL)

            if (res.ErrorGC == 0) {
              this.senderName = res.senderName
              this.roomID = res.gcID
              this.paramValueUserId = res.senderName
              this.userId = res.userId
              this.wsURL = res.wsURL
              this.isExternalUser= res.isExternalUser
              this.avatar = res.avatar
              This.isTranslateFunc = res.isTranslate
              This.isDevGC = res.isDevGC
              // this.serverUrl = res.serverUrl//'https://igcp0154.inlinegroup-c.ru:8881/webcall/ms'
              this.isDevUrl = false //res.isDevUrl;//

              setTimeout(() => {
                if (!this.isFirefox) {
                  this.startSettingsPreview()

                  this.initStream()
                } else {
                  this.startSettingsPreview()
                }

                this.addAudioTrack()

                $('#devMiniVideos').hide()
                $('#butDisNone').hide()

                window.onresize = () => {
                  This.resizeWindows()
                }

                this.initWebSocket(res.wsURL)

              }, 500)
            }
            else {
              console.log(res.TextError)
              This.$refs.noConnectInfoPanel.showLinkError(res.TextError, res.ErrorGC, res.nameLink)
              // if(res.ErrorGC == 3){
              //   alert(this.$t('gc.errorStartLink.3'))//res.TextError)
              // }else if(res.ErrorGC == 1){
              //   alert(this.$t('gc.errorStartLink.1'))//res.TextError)
              // }else if(res.ErrorGC == 2){
              //   alert(this.$t('gc.errorStartLink.2')+' '+res.TextError)
              // }else if(res.ErrorGC == 4){
              //   alert(this.$t('gc.errorStartLink.4'))//res.TextError)
              //
              // }else{
              //   alert(res.TextError)
              // }
              // window.close();
            }
          } catch (e) {
            console.log('Ошибка получения данных с сервера e',e)
            alert(this.$t('gc.errorStartLink.00'))
            // window.close();
          }
        });
      },
      /**endregion*/

      /**region waitingLoad загрузка страницы, если не произошла загрузки то ждем пока загрузиться*/
      waitingLoad() {
        setTimeout(() => {
          $(window).on('ready', () => This.getBrowser().then((is) => {
            if (is) {
              This.setMounted()
            } else {
              console.log('load false')
              This.waitingLoad()
            }
          }))
        }, 1000)
      },
      /**endregion*/

      /**region resizeWindows обновление во переворачиванием устройства*/
      resizeWindows() {
        this.setSizeUserList() // Расчет height для региона отображения списка активных пользователей
      },
      /**endregion*/

      /**region startSettingsPreview преднастройки для заглавного интерфейса*/
      startSettingsPreview() {

        This.isMirror = this.getLocalStorage("gc.mirror")
        This.videoValue = this.getLocalStorage("gc.videoValue")
        This.videoQualtiySendValue = this.getLocalStorage("gc.videoQualtiySendValue")
        This.videoQualtiyRecvValue = this.getLocalStorage("gc.videoQualtiyRecvValue")

        console.log('startSettingsPreview This.audioInputValue ',This.audioInputValue)
        console.log('startSettingsPreview This.audioOutputValue ',This.audioOutputValue)

        This.audioInputValue = this.getLocalStorage("gc.audioInputValue")
        This.audioOutputValue = this.getLocalStorage("gc.audioOutputValue")

        console.log('startSettingsPreview post This.audioInputValue ',This.audioInputValue)
        console.log('startSettingsPreview post This.audioOutputValue ',This.audioOutputValue)

        This.$refs.dialogSettCall.startSettingsPreview(This.isMirror, This.videoValue, This.videoQualtiySendValue,
          This.videoQualtiyRecvValue, This.audioInputValue, This.audioOutputValue, 'startSettingsPreview')
      }
      ,
      /**endregion*/

      /**region rejCallOper Ошибка загрузки страницы*/
      rejCallOper(parsedMessage) {

        if (parsedMessage.timeReject) {
          This.$refs.refConfirm.open(this.$t('reject.call.info'),
            this.$t('operator') + ' ' + parsedMessage.nameOper + ' ' + this.$t('reject.call.operator'),
            0, {color: 'warning'}, "")
        } else {
          This.$refs.refConfirm.open(this.$t('reject.call.info'),
            this.$t('operator') + ' ' + parsedMessage.nameOper + ' ' + this.$t('reject.call.operator_norime'),
            0, {color: 'warning'}, "")
        }
      }
      ,
      /**endregion*/
      //endregion

      //region WebSocket _________________________________________________________________________________________________

      /**region initWebSocket Запуск webSocket*/
      initWebSocket(url) {
        // var host = document.location.host;
        // var pathname = document.location.pathname;

        var isWSid = Date.now()

        if (this.isDebug) {
          console.log('initWebSocket isDebug url = ' + `ws://localhost:8882/ms/groupcall`)
          this.urlWS = `ws://localhost:8882/ms/groupcall`;
        } else {
          console.log('initWebSocket url = ' + `${url}/ms/groupcall`)

          var hostname = window.location.host;
          console.log("host "+hostname)

          if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
            url = 'wss://'+window.location.host
            console.log("location.hostname "+hostname)
          }
          this.urlWS = `${url}/ms/groupcall`;
        }

        this.ws = new WebSocket(this.urlWS);

        this.ws.onopen = function () {
          console.log('ws initWebSocket  onopen This.roomID = ' + This.roomID)
          console.log(This.ws)

          This.$refs.noConnectInfoPanel.closeInfo()

          This.startRunIsUsersAll()

          var valueUser = '123'

          // valueUser = This.paramValueUserId.replace(/ /g, '')
          if (This.issenderName) {
            This.paramValueUserId = This.paramValueUserId.substring(2, This.paramValueUserId.length)

            // This.isAvtoStart = true

            This.startRunIsUsers();

            if (This.roomID) {
              var message = {
                id: 'isLinkAvailable',
                user: This.userId,
                room: This.roomID,
                lang: This.paramValueLang,
                serverUrl: This.serverUrl,
                isDevUrl: This.isDevUrl
              }
              This.sendMessage(message);
            } else {
              This.infoCloseWS("нет данных о группе")
            }

          }
          else {
            if (This.roomID) {
              var message = {
                id: 'isLinkAvailable',
                user: This.userId,
                room: This.roomID,
                lang: This.paramValueLang,
                serverUrl: This.serverUrl,
                isDevUrl: This.isDevUrl
              }

              try {
                This.sendMessage(message);
              } catch (e) {
                console.error('error sendMessage isLinkAvailable sendMessage' + e)
              }

            } else {
              This.infoCloseWS("нет данных о группе")
            }
          }

          if(This.isAvtoStartNew){
            this.isVideoBackground=false
            console.log("isAvtoStartNew ws cool")
            This.startCallGC()
          }

        };

        this.ws.onmessage = function (message) {
          var parsedMessage = JSON.parse(message.data);

          //console.log(parsedMessage)

          switch (parsedMessage.id) {
            case 'isUsers' : // получение данных о текущем пользователе
              This.statusUserCall(parsedMessage)
              break;
            case 'isUsLink' :
              This.statusUsersMain(parsedMessage)
              break;
            case 'isUsLinkAll' : // получение данных о всех пользователях
              This.statusUsers(parsedMessage)
              break;
            case 'timeCall':
              This.$refs.timerGroupCall.startTimerMain(Number(parsedMessage.startTime), Number(parsedMessage.deltaTime))
              break;
            case 'stopCall':
              if (parsedMessage.userClose) {
                This.isClickClose = true

                var title = This.$t('reject.call.info.close.title')
                var stext = parsedMessage.userClose + ' ' + This.$t('reject.call.info.close')
                This.$refs.refConfirm.open(title, stext, 3, null, '')
              }
              document.exitFullscreen();
              setTimeout(() => {

                  This.$refs.roomXRCallWin.closeCall()

                  This.closeCall()
                }
                ,
                3000
              )

              break;
            case
            'userRepetition'
            :
              This.userRepetition()
              break;
            case 'joinRoomException':
              This.joinRoomException(parsedMessage)
              break;
            case
            'isLinkAvailable'
            :
              This.$refs.roomXRCallWin.isLinkLive(parsedMessage)
              break;
            case
            'existingParticipants'
            :
              setTimeout(() => {

                This.isStartCall = true
                clearTimeout(This.timeoutId);

                console.log('existingParticipants , this.isStartMic, this.isStartVideo ', This.isStartMic, This.isStartVideo)
                This.$refs.roomXRCallWin.onExistingParticipants(parsedMessage, This.isStartMic, This.isStartVideo);

                this.senderId = parsedMessage.senderId
                this.senderIdold = parsedMessage.senderId

                console.log('existingParticipants senderId ' + parsedMessage.senderId + ' senderIdold ' + this.senderIdold)

                This.addMyUserChat(parsedMessage.senderId, parsedMessage.senderName, '/exgc/avatar/' + This.roomID + '/' + parsedMessage.senderId);
                This.chatUpdateMessage(parsedMessage.chat)
                // This.planUpdateMessage(parsedMessage.plan)

              }, 500)


              break;
            case
            'newParticipantArrived'
            :
              This.$refs.roomXRCallWin.onNewParticipant(parsedMessage);

              this.delta = parsedMessage.delta
              this.oDraw = parsedMessage.orient


              break;
            case
            'participantLeft'
            :
              This.$refs.roomXRCallWin.onParticipantLeft(parsedMessage);
              break;
            case
            'isMicUser'
            :
              This.$refs.roomXRCallWin.isMicUser(parsedMessage.senderId, parsedMessage.isMic)
              break
            case
            'isSoundUser'
            :
              This.$refs.roomXRCallWin.isSoundUser(parsedMessage.senderId, parsedMessage.isSound)
              break
            case
            'isVideoUser'
            :
              console.log('roomXRCallWin.isVideoUser(parsedMessage.senderId, parsedMessage.isVideo,  parsedMessage.hasCamera)', parsedMessage)
              This.$refs.roomXRCallWin.isVideoUser(parsedMessage.senderId, parsedMessage.isVideo, parsedMessage.hasCamera)
              break
            case
            'receiveVideoAnswer'
            :
              This.$refs.roomXRCallWin.receiveVideoResponse(parsedMessage);
              break;
            case
            'iceCandidate'
            :
              This.$refs.roomXRCallWin.addIceCandidat(parsedMessage)
              break;
            case
            'isStartDraw'
            :
              This.$refs.roomXRCallWin.startDrawFunc(parsedMessage.operName)
              break;
            case
            'moveDraw'
            :
              break;
            case
            'drawMode'
            :
              break;
            case
            'arrowDrawOther'
            :
              This.$refs.roomXRCallWin.arrowDrawMess(parsedMessage)
              break;
            case
            'moveDrawOther'
            :
              This.$refs.roomXRCallWin.moveDrawMess(parsedMessage)
              break;
            case
            'drawModeOther'
            :
              This.$refs.roomXRCallWin.drawModeMess(parsedMessage)
              break;
            case
            'clearCanvas'
            :
              This.$refs.roomXRCallWin.clearCanvas(parsedMessage)
              break;
            case
            'clearCanvasOther'
            :
              This.$refs.roomXRCallWin.clearCanvas(parsedMessage)
              break;
            case
            'MESS_WS_NO_GC'
            :
              This.$refs.roomXRCallWin.rejCallOper(parsedMessage)
              break;
            case
            'isMicToUser'
            :
              This.$refs.roomXRCallWin.setMicToUser(parsedMessage)
              break;
            case
            'live'
            :
              break;
            case
            'funcOperOther'
            :
              This.$refs.roomXRCallWin.funcOperOther(parsedMessage)
              break;
            case
            'saveCall'
            :
              This.$refs.roomXRCallWin.statusSaveCall(parsedMessage)
              break;
            case
            'minBit'
            :

              This.$refs.roomXRCallWin.minBit(parsedMessage)

              break;
            case
            'closeAllFuncOper'
            :
              This.$refs.roomXRCallWin.closeAllFuncOper(parsedMessage)
              break;
            //message chat
            case
            'chatSendMessage'
            :
              This.chatNewMessage(parsedMessage)
              break;
            case
            'chatNewMessageOper'
            :
              This.chatNewMessageOper(parsedMessage)
              break;
            case
            'deviceParam'
            :
              This.setDeviceParam(parsedMessage)
              break;
            case
            'chatUpdateMessage'
            :
              This.chatUpdateMessage(parsedMessage)
              break;
            case
            'asrtTranlsateResultOne'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.restOneATmessage(parsedMessage)
              This.$refs.chatMessage.restOneATmessage(parsedMessage)
              break;
            case
            'asrtTranlsateResult'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.restATmessage(parsedMessage)
              This.$refs.chatMessage.restATmessage(parsedMessage)
              break;
            case 'asrtTranlsateResultError':
              This.asrtError(parsedMessage)
              break
            case
            'asrtTranlsateAudioVosk'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.asrtTranlsateAudioVosk(parsedMessage)
              This.$refs.chatMessage.restTranlsateAudioVosk(parsedMessage)
              break;
            case
            'soundRecAT'
            :
              This.$refs.roomXRCallWin.soundRecAT(parsedMessage)
              break;
            case
            'newMessageAT'
            :
              console.log('newMessageAT', parsedMessage)
              break;
            case
            'asrtTranlsateResultAudio'
            :
              This.$refs.roomXRCallWin.asrtTranlsateResultAudio(parsedMessage)
              break;
            case
            'sendTextTranslate'
            :
              This.$refs.roomXRCallWin.sendTextTranslateNewStatus(parsedMessage)
              break;
            case
            'asrtTranlsateNoResult'
            :
              This.$refs.roomXRCallWin.asrtTranlsateNoResult(parsedMessage)
              break;
            case
            'updateGcFileId'
            :
              This.$refs.roomXRCallWin.updateGcFileId(parsedMessage)
              break;
            case 'setPlanEl':
              This.$refs.chatMessage.setPlanEl(parsedMessage)
              break
            case 'sendMyPlan':
              This.planUpdateMessage(parsedMessage.plan)
              break;
            case 'exceededQuantity':
              This.setInfoNoConnect(parsedMessage, 1)
              break;
            case 'sendScreenshotOper':
              This.$refs.roomXRCallWin.sendScreenshotOper(parsedMessage)
              break;
            default:
              console.error('Unrecognized message', parsedMessage);
          }
        }

        this.ws.addEventListener('close', (evt) => {
          console.log('del ЗАКРЫТИЕ WebSocket с кодом: ' + evt.code);
          this.isInfoServer = false
          switch (evt.code) {
            case 1000: //CLOSE_NORMAL
              ////console.log("WS-close Правильное закрытие сокета.");
              // this.infoCloseWS("web socket - правильное закрытие сокета.", 1000)
              break;

            case 1001: //LWS_CLOSE_STATUS_GOINGAWAY
              ////console.log('WS-close сервер отключается или браузер перешел от страницы..');
              this.infoCloseWS('web socket отключается или браузер перешел от страницы..', 1001)
              break;

            case 1002: //LWS_CLOSE_STATUS_PROTOCOL_ERR
              this.setLocalStorage("gc.update.error", 't')
              location.reload();
              console.log('WS-close 1002 завершает соединение из-за ошибки протокола..');
              break;

            case 1003: //LWS_CLOSE_STATUS_UNACCEPTABLE_OPCODE
              ////console.log('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +'если она получает двоичное сообщение)..');
              this.infoCloseWS('web socket конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +
                'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +
                'если она получает двоичное сообщение)..', 1003)
              break;

            case 1006: //CLOSE_ABNORMAL
              ////console.log('WS-close Сервер временно недоступен. Попробуйте снова позднее.');
              // this.infoCloseWS('WS-close Сервер временно недоступен. Попробуйте снова позднее.', 1006);
              break;

            case 1009: //LWS_CLOSE_STATUS_MESSAGE_TOO_LARGE
              ////console.log('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.')
              this.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.', 1009)
              break

            case 1011: //LWS_CLOSE_STATUS_UNEXPECTED_CONDITION
              ////console.log('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.')
              this.infoCloseWS('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.', 1011)
              break;

            default:
              this.infoCloseWS('Нет данных подключения', 300)
              break;
          }

          try {
            This.$refs.timerGroupCall.stopTimer()
          } catch (e) {
            console.log(e)
          }

          setTimeout(() => {
            if(!This.isClickClose){
              This.restartWS()
            }
          }, 500)
        }, false);
      },
      /**endregion*/

      /**region infoCloseWS Информация о закрытии ws*/
      infoCloseWS(textClose, code) {
        console.log('infoCloseWS',textClose)
        console.log('code',code)

        this.ws.close()
        this.isPreloading = false
        this.isActiveLink = false

        // This.$refs.roomXRCallWin.closeCall()

        if(code == 1001){
          This.$refs.noConnectInfoPanel.showInfo('Обрыв соединения с сервером', 'Сервер не доступен, проверьте подключение к интернету. ' +
            'Идет попытка повторного подключения', this.$t('gc.title.group.call')+': '+this.nameRoom)
        }else{
          This.$refs.noConnectInfoPanel.showInfo(this.$t('gc.title.group.call.error'), this.$t('gc.title.group.call.errortext')+' ('+textClose+')', this.$t('gc.title.group.call')+': '+this.nameRoom)
        }
        // This.$refs.noConnectInfoPanel.showInfo('Обрыв соединения с сервером', 'Идет попытка восстановления соединения.', this.$t('gc.title.group.call')+': '+this.nameRoom)
      }
      ,
      /**endregion*/

      /**region sendMessage Отправка сообщения по ws*/
      sendMessage(message) {
        // console.log('sendMessage this.ws', this.ws)
        // console.log('sendMessage this.ws', this.ws.readyState)
        // console.log('sendMessage message', message)
        if (this.ws) {
          try{
            var jsonMessage = JSON.stringify(message)
            this.ws.send(jsonMessage)
            return true
          }catch (e) {
            console.log('sendMessage ERROR this.ws', e)
          }

        } else {
          this.infoCloseWS('close this.ws null')
          return false
        }
      }
      ,

      isLiveSession(){

      }
      ,

      restartSession() {
        console.log('restartSession')
        This.ws = new WebSocket(This.urlWS);

        This.ws.onopen = function () {
          console.log('ws initWebSocket  onopen This.roomID = ', This.roomID)
          console.log(This.ws)
          console.log("This.joinRoomMessageNewWS ", This.joinRoomMessageNewWS)

          This.$refs.noConnectInfoPanel.closeInfo()

          This.$refs.roomXRCallWin.setWS(This.ws)

          This.startRunIsUsersAll()

          var jsonMessage = JSON.stringify(This.joinRoomMessageNewWS)
          This.ws.send(jsonMessage)

          This.startRunIsUsers();
        };

        This.ws.onmessage = function (message) {
          var parsedMessage = JSON.parse(message.data);

          //console.log(parsedMessage)

          switch (parsedMessage.id) {
            case 'isUsers' : // получение данных о текущем пользователе
              This.statusUserCall(parsedMessage)
              break;
            case 'isUsLink' :
              This.statusUsersMain(parsedMessage)
              break;
            case 'isUsLinkAll' : // получение данных о всех пользователях
              This.statusUsers(parsedMessage)
              break;
            case 'timeCall':
              This.$refs.timerGroupCall.startTimerMain(Number(parsedMessage.startTime), Number(parsedMessage.deltaTime))
              break;
            case 'stopCall':
              if (parsedMessage.userClose) {
                This.isClickClose = true

                var title = This.$t('reject.call.info.close.title')
                var stext = parsedMessage.userClose + ' ' + This.$t('reject.call.info.close')
                This.$refs.refConfirm.open(title, stext, 3, null, '')
              }
              document.exitFullscreen();
              setTimeout(() => {

                  This.$refs.roomXRCallWin.closeCall()

                  This.closeCall()
                }
                ,
                3000
              )

              break;
            case
            'userRepetition'
            :
              This.userRepetition()
              break;
            case 'joinRoomException':
              This.joinRoomException(parsedMessage)
              break;
            case
            'isLinkAvailable'
            :
              This.$refs.roomXRCallWin.isLinkLive(parsedMessage)
              break;
            case
            'existingParticipants'
            :
              setTimeout(() => {

                This.isStartCall = true
                clearTimeout(This.timeoutId);

                console.log('existingParticipants , This.isStartMic, This.isStartVideo ', This.isStartMic, This.isStartVideo)
                This.$refs.roomXRCallWin.onExistingParticipants(parsedMessage, This.isStartMic, This.isStartVideo);

                This.senderId = parsedMessage.senderId
                This.senderIdold = parsedMessage.senderId

                console.log('existingParticipants senderId ' + parsedMessage.senderId + ' senderIdold ' + This.senderIdold)

                This.addMyUserChat(parsedMessage.senderId, parsedMessage.senderName, '/exgc/avatar/' + This.roomID + '/' + parsedMessage.senderId);
                This.chatUpdateMessage(parsedMessage.chat)
                // This.planUpdateMessage(parsedMessage.plan)

              }, 500)


              break;
            case
            'newParticipantArrived'
            :
              This.$refs.roomXRCallWin.onNewParticipant(parsedMessage);

              This.delta = parsedMessage.delta
              This.oDraw = parsedMessage.orient


              break;
            case
            'participantLeft'
            :
              This.$refs.roomXRCallWin.onParticipantLeft(parsedMessage);
              break;
            case
            'isMicUser'
            :
              This.$refs.roomXRCallWin.isMicUser(parsedMessage.senderId, parsedMessage.isMic)
              break
            case
            'isSoundUser'
            :
              This.$refs.roomXRCallWin.isSoundUser(parsedMessage.senderId, parsedMessage.isSound)
              break
            case
            'isVideoUser'
            :
              console.log('roomXRCallWin.isVideoUser(parsedMessage.senderId, parsedMessage.isVideo,  parsedMessage.hasCamera)', parsedMessage)
              This.$refs.roomXRCallWin.isVideoUser(parsedMessage.senderId, parsedMessage.isVideo, parsedMessage.hasCamera)
              break
            case
            'receiveVideoAnswer'
            :
              This.$refs.roomXRCallWin.receiveVideoResponse(parsedMessage);
              break;
            case
            'iceCandidate'
            :
              This.$refs.roomXRCallWin.addIceCandidat(parsedMessage)
              break;
            case
            'isStartDraw'
            :
              This.$refs.roomXRCallWin.startDrawFunc(parsedMessage.operName)
              break;
            case
            'moveDraw'
            :
              break;
            case
            'drawMode'
            :
              break;
            case
            'arrowDrawOther'
            :
              This.$refs.roomXRCallWin.arrowDrawMess(parsedMessage)
              break;
            case
            'moveDrawOther'
            :
              This.$refs.roomXRCallWin.moveDrawMess(parsedMessage)
              break;
            case
            'drawModeOther'
            :
              This.$refs.roomXRCallWin.drawModeMess(parsedMessage)
              break;
            case
            'clearCanvas'
            :
              break;
            case
            'clearCanvasOther'
            :
              This.$refs.roomXRCallWin.clearCanvas(parsedMessage)
              break;
            case
            'MESS_WS_NO_GC'
            :
              This.$refs.roomXRCallWin.rejCallOper(parsedMessage)
              break;
            case
            'isMicToUser'
            :
              This.$refs.roomXRCallWin.setMicToUser(parsedMessage)
              break;
            case
            'live'
            :
              break;
            case
            'funcOperOther'
            :
              This.$refs.roomXRCallWin.funcOperOther(parsedMessage)
              break;
            case
            'saveCall'
            :
              This.$refs.roomXRCallWin.statusSaveCall(parsedMessage)
              break;
            case
            'minBit'
            :

              This.$refs.roomXRCallWin.minBit(parsedMessage)

              break;
            case
            'closeAllFuncOper'
            :
              This.$refs.roomXRCallWin.closeAllFuncOper(parsedMessage)
              break;
            //message chat
            case
            'chatSendMessage'
            :
              This.chatNewMessage(parsedMessage)
              break;
            case
            'chatNewMessageOper'
            :
              This.chatNewMessageOper(parsedMessage)
              break;
            case
            'deviceParam'
            :
              This.setDeviceParam(parsedMessage)
              break;
            case
            'chatUpdateMessage'
            :
              This.chatUpdateMessage(parsedMessage)
              break;
            case
            'asrtTranlsateResultOne'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.restOneATmessage(parsedMessage)
              This.$refs.chatMessage.restOneATmessage(parsedMessage)
              break;
            case
            'asrtTranlsateResult'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.restATmessage(parsedMessage)
              This.$refs.chatMessage.restATmessage(parsedMessage)
              break;
            case 'asrtTranlsateResultError':
              This.asrtError(parsedMessage)
              break
            case
            'asrtTranlsateAudioVosk'
            :
              // This.$refs.roomXRCallWin.$refs.refAudioTranslation.asrtTranlsateAudioVosk(parsedMessage)
              This.$refs.chatMessage.restTranlsateAudioVosk(parsedMessage)
              break;
            case
            'soundRecAT'
            :
              This.$refs.roomXRCallWin.soundRecAT(parsedMessage)
              break;
            case
            'newMessageAT'
            :
              console.log('newMessageAT', parsedMessage)
              break;
            case
            'asrtTranlsateResultAudio'
            :
              This.$refs.roomXRCallWin.asrtTranlsateResultAudio(parsedMessage)
              break;
            case
            'sendTextTranslate'
            :
              This.$refs.roomXRCallWin.sendTextTranslateNewStatus(parsedMessage)
              break;
            case
            'asrtTranlsateNoResult'
            :
              This.$refs.roomXRCallWin.asrtTranlsateNoResult(parsedMessage)
              break;
            case
            'updateGcFileId'
            :
              This.$refs.roomXRCallWin.updateGcFileId(parsedMessage)
              break;
            case 'setPlanEl':
              This.$refs.chatMessage.setPlanEl(parsedMessage)
              break
            case 'sendMyPlan':
              This.planUpdateMessage(parsedMessage.plan)
              break;
            case 'exceededQuantity':
              This.setInfoNoConnect(parsedMessage, 1)
              break;
            case 'sendScreenshotOper':
              This.$refs.roomXRCallWin.sendScreenshotOper(parsedMessage)
              break;
            default:
              console.error('Unrecognized message', parsedMessage);
          }
        }

        this.ws.addEventListener('close', (evt) => {
          console.log('del ЗАКРЫТИЕ WebSocket с кодом: ' + evt.code);
          this.isInfoServer = false
          switch (evt.code) {
            case 1000: //CLOSE_NORMAL
              ////console.log("WS-close Правильное закрытие сокета.");
              // this.infoCloseWS("web socket - правильное закрытие сокета.", 1000)
              break;

            case 1001: //LWS_CLOSE_STATUS_GOINGAWAY
              ////console.log('WS-close сервер отключается или браузер перешел от страницы..');
              this.infoCloseWS('web socket отключается или браузер перешел от страницы..', 1001)
              break;

            case 1002: //LWS_CLOSE_STATUS_PROTOCOL_ERR
              this.setLocalStorage("gc.update.error", 't')
              location.reload();
              console.log('WS-close 1002 завершает соединение из-за ошибки протокола..');
              break;

            case 1003: //LWS_CLOSE_STATUS_UNACCEPTABLE_OPCODE
              ////console.log('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +'если она получает двоичное сообщение)..');
              this.infoCloseWS('web socket конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +
                'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +
                'если она получает двоичное сообщение)..', 1003)
              break;

            case 1006: //CLOSE_ABNORMAL
              ////console.log('WS-close Сервер временно недоступен. Попробуйте снова позднее.');
              // this.infoCloseWS('WS-close Сервер временно недоступен. Попробуйте снова позднее.', 1006);
              break;

            case 1009: //LWS_CLOSE_STATUS_MESSAGE_TOO_LARGE
              ////console.log('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.')
              this.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.', 1009)
              break

            case 1011: //LWS_CLOSE_STATUS_UNEXPECTED_CONDITION
              ////console.log('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.')
              this.infoCloseWS('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.', 1011)
              break;

            default:
              this.infoCloseWS('Нет данных подключения', 300)
              break;
          }

          try {
            This.$refs.timerGroupCall.stopTimer()
          } catch (e) {
            console.log(e)
          }

          setTimeout(() => {
            if(!This.isClickClose){
              This.restartWS()
            }
          }, 500)
        }, false);
      },
      /**endregion*/

      //endregion

      //region Данные о ссылке и участниках ______________________________________________________________________________
      statusUsersMain(mess) {
        //console.log('statusUsersMain mess ',mess)
        if (mess.usersActive.length != 0) {
          this.usersCallList = mess.usersActive.map(user => {
            const isScrsUser = user.senderName && user.senderName.startsWith('scrsXR_');

            return {
              ...user,
              isOper: isScrsUser ? false : user.isOper,
              isScrs: isScrsUser,
              senderName: isScrsUser
                ? 'Демонстрация экрана ' + user.senderName.replace('scrsXR_', '')
                : user.senderName
            };
          });
          this.isUserCallList = this.usersCallList.length > 0
          this.numUsersCallList = this.usersCallList.length
          this.usersCall = `${this.$t('gc.user-online')}: ${this.usersCall}`
        }
        else {
          this.usersCall = this.$t('gc.no-members')
          this.usersCallList = [];
          this.isUserCallList = false
          this.numUsersCallList = 0
        }
        //console.log("L "+this.isUserCallList+ " "+this.usersCallList.length, this.usersCallList)

        setTimeout(() => {
          this.setSizeUserList();
        })
      },

      /**region statusUsers обновление данных о пользователях*/
      statusUsers(mess) {
        //console.log('statusUsers(mess)', mess)
        try {
          if (This.$refs.roomXRCallWin.getSenderID()) {
            This.senderId = This.$refs.roomXRCallWin.getSenderID()
          }
        } catch (e) {
          console.log('statusUsers e ', e)
          This.closeCall()
        }

        This.$refs.roomXRCallWin.setStatusSaveCall(mess.isSaveVideo)
        This.$refs.roomXRCallWin.setFuncOperOther(mess.isFuncOper)


        This.timeChackSession++
        if (This.numUsers != mess.numUsers || This.timeChackSession >= 10) {
          This.timeChackSession = 0
          This.numUsers = This.$refs.roomXRCallWin.usersError(mess)
        } else {
          This.numUsers = mess.numUsers
        }

        This.numUsersExternal = mess.numUsersExternal

        if (mess.usersActive != "") {

          //XXX
          // if (This.isCallSessionActive) {
          if (true) {
            var arUsers = mess.usersActive.split(", ")
            var arUsersActiveSenderID = mess.usersActiveSenderID.split(", ")

            this.itemsUsersInfo = []

            var avatars = This.$refs.roomXRCallWin.getAvatar()

            for (var i = 0; i < arUsers.length; i++) {

              try {
                var oper = ''
                var srsc = ''

                if (arUsers[i].length > 12) {
                  oper = arUsers[i].substring(0, 9)
                  srsc = arUsers[i].substring(0, 6)
                  // console.log('oper '+oper+' srsc '+srsc)
                }

                var avatar = avatars[arUsersActiveSenderID[i].replace(/^\s/g, '')]

                var isMy = arUsersActiveSenderID[i].replace(/^\s/g, '') == This.senderId

                if (oper == 'Оператор_' || oper == 'Operator_') {
                  this.itemsUsersInfo[i] = {
                    senderId: arUsersActiveSenderID[i].replace(/^\s/g, ''),
                    senderName: arUsers[i],
                    text: arUsers[i].replace(/^\s/g, ''),
                    icon: 'fa-user',
                    avatar: `data:image/jpg;base64,${avatar}`,
                    color: '#43a047',
                    colorText: 'black',
                    isscrS: false,
                    isOper: true,
                    isMy: isMy,
                    isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i].replace(/^\s/g, ''))
                  }
                } else if (srsc === 'scrsXR') {
                  this.itemsUsersInfo[i] = {
                    senderId: arUsersActiveSenderID[i].replace(/^\s/g, ''),
                    senderName: arUsers[i],
                    text: arUsers[i].substring(8, arUsers[i].length) + this.$t('gc.scrS'),
                    icon: 'fa-chalkboard-teacher',
                    avatar: '',
                    color: '#43a047',
                    colorText: 'black',
                    isscrS: true,
                    isOper: false,
                    isMy: isMy,
                    isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i].replace(/^\s/g, ''))
                  }
                } else {
                  this.itemsUsersInfo[i] = {
                    senderId: arUsersActiveSenderID[i].replace(/^\s/g, ''),
                    senderName: arUsers[i],
                    text: arUsers[i].replace(/^\s/g, ''),
                    icon: 'fa-user-tie',
                    avatar: `data:image/jpg;base64,${avatar}`,
                    color: '#43a047',
                    colorText: 'black',
                    isscrS: false,
                    isOper: false,
                    isMy: isMy,
                    isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i].replace(/^\s/g, ''))
                  }
                }

              } catch (e) {
                console.log(e)
              }
            }

            //________________________________________________________________________
            //________________________________________________________________________
            //________________________________________________________________________
            var arUsersExternal = mess.usersActiveExternal.split(",")
            var usersActiveExternalSenderID = mess.usersActiveExternalSenderID.split(",")
            // console.log('arUsersExternal', arUsersExternal)
            // console.log('numUsersExternal', this.numUsersExternal)

            this.itemsUsersInfoExternal = []

            if (this.numUsersExternal > 0) {
              for (var i = 0; i < arUsersExternal.length; i++) {

                this.itemsUsersInfoExternal[i] = {
                  icon: 'fa-user',
                  avatar: '',
                  color: '#cb5400',
                  colorText: 'black',
                  isscrS: false,
                  isOper: true,
                  isMy: false,
                  senderName: arUsersExternal[i].replace(/^\s/g, ''),
                  senderId: usersActiveExternalSenderID[i].replace(/^\s/g, ''),
                  isMic: false
                }

                //будем показывать всплывающее уведомление если полноэкранный режим и панель скрыта
                // if(This.isFullScreen /*&& document.querySelector('.lowButtonsPanel').style.visibility === 'hidden'*/){
                //   console.log('положили в наш компонент  i участника')
                //   this.$refs.notificationExternalUser.show(this.itemsUsersInfoExternal[i])
                // }

                if (usersActiveExternalSenderID[i].replace(/^\s/g, '') in This.userEcpectNew) {
                  console.log('yessss', This.userEcpectNew)
                } else {
                  console.log('noooo')
                  This.userEcpectNew[usersActiveExternalSenderID[i].replace(/^\s/g, '')] = usersActiveExternalSenderID[i].replace(/^\s/g, '')
                  This.$refs.roomXRCallWin.newUserEcpect(arUsersExternal[i].replace(/^\s/g, ''), This.$t('gc.newUserEnsp'))

                  if(This.isFullScreen && document.querySelector('.lowButtonsPanel').style.visibility === 'hidden'){
                    console.log('положили в наш компонент  i участника')
                    this.$refs.notificationExternalUser.show(this.itemsUsersInfoExternal[i])
                  }
                }
              }
            } else {
            }
          }
        }
        // console.log('_______________this.itemsUsersInfoExternal ', this.itemsUsersInfoExternal)
      },
      /**endregion*/

      showNotification(itemUsersInfoExternal){

      },

      toUTF8Array(str) {
        var utf8 = [];
        for (var i = 0; i < str.length; i++) {
          var charcode = str.charCodeAt(i);
          if (charcode < 0x80) utf8.push(charcode);
          else if (charcode < 0x800) {
            utf8.push(0xc0 | (charcode >> 6),
              0x80 | (charcode & 0x3f));
          } else if (charcode < 0xd800 || charcode >= 0xe000) {
            utf8.push(0xe0 | (charcode >> 12),
              0x80 | ((charcode >> 6) & 0x3f),
              0x80 | (charcode & 0x3f));
          }
          // surrogate pair
          else {
            i++;
            // UTF-16 encodes 0x10000-0x10FFFF by
            // subtracting 0x10000 and splitting the
            // 20 bits of 0x0-0xFFFFF into two halves
            charcode = 0x10000 + (((charcode & 0x3ff) << 10)
              | (str.charCodeAt(i) & 0x3ff));
            utf8.push(0xf0 | (charcode >> 18),
              0x80 | ((charcode >> 12) & 0x3f),
              0x80 | ((charcode >> 6) & 0x3f),
              0x80 | (charcode & 0x3f));
          }
        }
        return utf8;
      },

      /**region statusUserCall получение данных о текущем пользователе*/
      statusUserCall(mess) {
        console.log('statusUserCall(mess)',mess)
        if (mess.pw == null || mess.pw == undefined) {
          this.isPw = false
          this.pw = mess.pw
        } else {
          this.isPw = true
          this.pw = mess.pw
        }

        this.isDeveloper = mess.dev
        this.isdevUser = mess.devUser

        // this.avatar = mess.avatar
        This.$refs.roomXRCallWin.addMyAvatar(this.avatar);

        //изображения работают
        //$(`#imgTEST`).attr('src', `data:image/jpg;base64,${this.avatar}`)


        if (This.isAvtoStart && mess.isLive) {
          This.$refs.dialogSettCall.updateDev()

          this.nameRoomBig = mess.nameRoom
          if (mess.nameRoom.length > 17) {
            this.nameRoom = mess.nameRoom.substring(0, 17) + '...'
          } else {
            this.nameRoom = mess.nameRoom.substring(0, 17)
          }

          this.dataLinkInfo = mess
          // This.senderId = This.paramValueUserId
          // setTimeout(() => {
          //   This.startCallGC()
          // }, 500);

        }
        else if (mess.isLive) {
          this.isPreloading = false
          this.isActiveLink = true

          if (mess.name != "") {
            this.senderName = mess.name
            this.isPw = false
            this.issenderName = false
          } else {
            this.issenderName = true
          }

          this.nameRoomBig = mess.nameRoom
          if (mess.nameRoom.length > 17) {
            this.nameRoom = mess.nameRoom.substring(0, 17) + '...'
          } else {
            this.nameRoom = mess.nameRoom.substring(0, 17)
          }

          this.dataLinkInfo = mess


          if (mess.usersActive != "") {
            this.usersCall = `${this.$t('gc.user-online')}: ${mess.usersActive.substring(0, mess.usersActive.length - 2)}`
          } else {
            this.usersCall = this.$t('gc.no-members')
          }

          this.usersCall = this.usersCall.replace(/(^\s*,)|(,\s*$)/g, '');
          console.log('this.usersCall', this.usersCall, 'mess.usersActive', mess.usersActive);

          this.nameRoomGr = this.$t('gc.group') + ': ' + this.nameRoom
          //this.nameRoomBigGr = this.$t('gc.group') + ': ' + this.nameRoomBig
          this.nameRoomBigGr = this.nameRoomBig

          this.infoDataDate = `${this.$t('gc.available-from')} ${this.dataLinkInfo.dateStart}`
          this.infoDataLive = `${this.$t('gc.during')}: ${this.dataLinkInfo.typeLive}`
          this.infoDataOU = `${this.$t('ou')}: ${this.dataLinkInfo.ouName}`

        }
        else {
          console.log('mess',mess)
          this.nameRoom = mess.nameRoom
          This.infoCloseWS("ошибка на сервере: "+mess.error)
        }
      }
      ,
      /**endregion*/
      //endregion

      //region Стартовая страница ________________________________________________________________________________________

      /**region previewVideo стартовое видео*/
      previewVideo() {
        this.isAvtoStart = false

        if (!this.isAvtoStart) {
          var audioSource = this.audioInputValue;
          var videoSource = this.videoValue;

          $(`#videoPre`).show()

          if (this.isMirror) {
            console.log('mirror setMounted t')
            $(`#videoPre`).addClass('clVideoMirror')
          } else {
            console.log('mirror setMounted t')
            $(`#videoPre`).removeClass('clVideoMirror')
          }

          if (STREAM_VIDEO) {
            if ($(`#videoPre`)[0]) {
              $(`#videoPre`)[0].srcObject = STREAM_VIDEO;
              This.isDownVideo = false
            }
          }
          // if (This.isDegradation) {
          //
          //   if (this.urlBackgroundFon == '') {
          //     $(`#videoPre`)[0].srcObject = document.getElementById('canvas-stream-mask').captureStream()
          //   } else {
          //     $(`#videoPre`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
          //   }
          // }
        }
      }
      ,
      /**endregion*/

      //endregion

      //region Подключение к ГЗ и отключение _____________________________________________________________________________
      /**region*/// начало соединения с ГЗ startCallGC
      startCallGC() {
this.isCallSessionActive = true
        this.isSpinStart = true;

        // Запускаем сессию

        if (this.isDeviceYes) {
          This.isSpinStart = true
          setTimeout(() => {
            This.isSpinStart = false
          }, 4000)


          if (this.isOneUser) {
            if (This.senderName == '') {
              this.$root.showErr(this.$t('gc.error.noname'))
              This.isSpinStart = false
              // }
              // else if (This.isAllowedChar(This.senderName)) {
              //   // this.$root.showErr(this.$t('gc.error.noname.char'))
              //   this.$root.showErr('Ошибка имени, используются неправильные знаки. Имя должно состоять из букв и цифр')
              //   This.isSpinStart = false
              // }
              // else if (This.isPw && This.pwUser == '') {
              //   this.$root.showErr(this.$t('gc.error.nopw'))
              //   This.isSpinStart = false
            } else if (This.isPw && This.pwUser != This.pw) {
              this.$root.showErr(this.$t('gc.error.errorpw'))
              This.isSpinStart = false
            } else {
              this.isOneUser = false
              this.senderNameReplace = this.senderName.replace(/ /g, '')
              var message = {}
              var message2 = {}

              this.miniName = this.setNameUC(this.senderName)

              if (this.avatar.length > 0) {
                message = {
                  id: 'joinRoom',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  serverUrl: this.serverUrl,
                  isDevUrl: this.isDevUrl,
                  hasCamera: this.hasCamera,
                  userId: this.userId,
                  hasMic: this.hasMic
                }
                this.joinRoomMessage = message;

                message2 = {
                  id: 'joinNewWS',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  serverUrl: this.serverUrl,
                  isDevUrl: this.isDevUrl,
                  hasCamera: this.hasCamera,
                  userId: this.userId,
                  hasMic: this.hasMic
                }
                this.joinRoomMessageNewWS = message2
              }
              else {
                this.avatar = This.$refs.chatMessage.generateAvatar(this.miniName)
                message = {
                  id: 'joinRoom',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  serverUrl: this.serverUrl,
                  isDevUrl: this.isDevUrl,
                  hasCamera: this.hasCamera,
                  userId: this.userId,
                  hasMic: this.hasMic
                }
                this.joinRoomMessage = message;
                message2 = {
                  id: 'joinNewWS',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  serverUrl: this.serverUrl,
                  isDevUrl: this.isDevUrl,
                  hasCamera: this.hasCamera,
                  userId: this.userId,
                  hasMic: this.hasMic
                }
                this.joinRoomMessageNewWS = message2
              }

              This.isrep = true
              this.startRunIsStartCall(2)
              this.sendMessage(message);

              setTimeout(() => {
                if (This.isrep) {
                  this.isCallSessionActive = true

                  STREAM_VIDEO_FON = null
                  console.log('начало соединения с ГЗ startCallGC '+this.senderName)
                  This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.senderName, this.hasCamera, this.typeBrowser, this.isTranslateFunc, this.isDevGC, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO,  this.serverUrl, this.isDevUrl)
                  // }

                  This.isSpinStart = false
                  this.infoOut()

                }
                this.isOneUser = true
              }, 300)
            }
          }
        } else {
          setTimeout(() => {
            this.onErrorDevices('ND', 8)
          }, 200)
        }
      }
      ,

      // setColorRnd(){
      //   var colors = ['#a26400','#a23900','#3d2607','#1e1403',
      //     '#3e70cc','#1f3f7e','#091556','#000525',
      //     '#852525','#751b1b','#4d0202','#170000',
      //     '#420724','#0f4821','#19261c','#081304',
      //     '#490a3c','#2f1c27']
      //
      //   return colors[Math.floor(Math.random() * 16)];
      // }
      // ,
      //
      // generateAvatar(text) {
      //
      //   const canvas = document.createElement("canvas");
      //   const context = canvas.getContext("2d");
      //
      //   var foregroundColor = '#ffffff'
      //   var backgroundColor = this.setColorRnd()
      //
      //   canvas.width = 200;
      //   canvas.height = 200;
      //
      //   // Draw background
      //   context.fillStyle = backgroundColor;
      //   context.fillRect(0, 0, canvas.width, canvas.height);
      //
      //   context.font = "bold 80px Rooftop-Regular, \"Helvetica Neue\", \"Segoe UI\", Helvetica, Arial, sans-serif";
      //   context.fillStyle = foregroundColor;
      //   context.textAlign = "center";
      //   context.textBaseline = "middle";
      //
      //   context.fillText(text, canvas.width / 2, canvas.height / 2 + 4);
      //
      //   return canvas.toDataURL();
      // },

      // region функция для определения заглавных букв для отключения видео
      setNameUC(name) {
        console.log(name)
        console.log(name[0])
        var newStr = name[0];
        for (var i = 1; i < name.length; i++) {
          if (name[i].toLowerCase() != name[i]) {
            newStr += name[i]
          }
          if (newStr.length == 2) {
            i = name.length
          }
        }
        return newStr
      },

      updateSettUser() {
        This.$refs.dialogSettCall.updateDev()
      },
      /**endregion*/

      /**region*/
      isAllowedChar(nameStr) {
        console.log('isAllowedChar ', nameStr)
        if (nameStr.includes('/')
          || nameStr.includes('\\')
          || nameStr.includes('|')
          || nameStr.includes(',')
          || nameStr.includes('.')
          || nameStr.includes('\'')
          || nameStr.includes('\"')
          || nameStr.includes('#')) {
          return true
        } else {
          return false
        }
      }
      ,
      /**endregion*/

      /**region*/
      closeCall() {
        try {
          This.isClickClose = true

          this.isCallSessionActive = false
          document.exitFullscreen();

          var keys = Object.keys(stream.calls);

          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];

            if (participant && participant.peer) {
              $('#video_' + participant.senderId).hide()
              $('#devVideo_' + participant.senderId).hide()
              delete stream.calls['' + keys[i]];
            }
          }

          if (!this.isAvtoStart) {
            this.$router.go(0)
          }

          this.ws.close()
        } catch (e) {
          console.log(' closeCall() error ', e)
          this.ws.close()
        }

        window.close();
      }
      ,
      /**endregion*/

      /**region*/
      userRepetition() {
        this.isrep = false
        // this.$root.showErr(this.$t('gc.error.secondname'))

        setTimeout(() => {
          if (This.senderNameOldRep == '') {
            This.senderNameOldRep = This.senderName
          }
          This.senderName = This.senderNameOldRep + ' (' + This.numRepetition + ')'
          This.numRepetition++

          this.isOneUser = false
          this.senderNameReplace = this.senderName.replace(/ /g, '')
          var message = {}
          var message2 = {}

          this.miniName = this.setNameUC(this.senderName.replace(/ /g, ''))


          if (this.avatar.length > 0) {
            message = {
              id: 'joinRoom',
              senderName: this.senderName,
              roomId: this.roomID,
              bodyPix: this.isDegradation,
              device: this.typeBrowser,
              avatar: this.avatar,
              serverUrl: this.serverUrl,
              isDevUrl: this.isDevUrl,
              hasCamera: this.hasCamera,
              userId: this.userId,
              hasMic: this.hasMic
            }
            this.joinRoomMessage = message;
            message2 = {
              id: 'joinNewWS',
              senderName: this.senderName,
              roomId: this.roomID,
              bodyPix: this.isDegradation,
              device: this.typeBrowser,
              avatar: this.avatar,
              serverUrl: this.serverUrl,
              isDevUrl: this.isDevUrl,
              hasCamera: this.hasCamera,
              userId: this.userId
            }
            this.joinRoomMessageNewWS = message2
          } else {
            this.avatar = This.$refs.chatMessage.generateAvatar(this.miniName)
            message = {
              id: 'joinRoom',
              senderName: this.senderName,
              roomId: this.roomID,
              bodyPix: this.isDegradation,
              device: this.typeBrowser,
              avatar: this.avatar,
              serverUrl: this.serverUrl,
              isDevUrl: this.isDevUrl,
              hasCamera: this.hasCamera,
              userId: this.userId,
              hasMic: this.hasMic
            }
            this.joinRoomMessage = message;
            message2 = {
              id: 'joinNewWS',
              senderName: this.senderName,
              roomId: this.roomID,
              bodyPix: this.isDegradation,
              device: this.typeBrowser,
              avatar: this.avatar,
              serverUrl: this.serverUrl,
              isDevUrl: this.isDevUrl,
              hasCamera: this.hasCamera,
              userId: this.userId
            }
            this.joinRoomMessageNewWS = message2
          }

          This.isrep = true
          this.sendMessage(message);

          setTimeout(() => {
            if (This.isrep) {
              this.isCallSessionActive = true
              // на полный экран

              // document.documentElement.requestFullscreen();


              STREAM_VIDEO_FON = null
              console.log('начало соединения с ГЗ userRepetition '+this.senderName)
              This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.senderName, this.hasCamera, this.typeBrowser, this.isTranslateFunc, this.isDevGC, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO, this.serverUrl, this.isDevUrl)
              // }

              This.isSpinStart = false
              this.infoOut()

            }
            this.isOneUser = true
          }, 300)


        }, 500)
      }
      ,

      joinRoomException(mess) {
        this.isrep = false
        // this.$root.showErr(this.$t('gc.error.secondname'))

        this.restartWS();


        this.$root.showErr(this.$root.showErr(this.$t('ex.error.no.reg')))
        console.error('Ошибка во время регистрации на медиа сервере ', mess)


        // setTimeout(() => {
        //   this.isOneUser = false
        //   this.senderNameReplace = this.senderName.replace(/ /g, '')
        //   var message = {}
        //
        //   this.miniName = this.setNameUC(this.senderName.replace(/ /g, ''))
        //
        //
        //   if (this.avatar.length > 0) {
        //     message = {
        //       id: 'joinRoom',
        //       senderName: this.senderName,
        //       roomId: this.roomID,
        //       bodyPix: this.isDegradation,
        //       device: this.typeBrowser,
        //       avatar: this.avatar,
        //       serverUrl: this.serverUrl,
        //       isDevUrl: this.isDevUrl,
        //       hasCamera: this.hasCamera,
        //       userId: this.userId
        //     }
        //   } else {
        //     this.avatar = This.$refs.chatMessage.generateAvatar(this.miniName)
        //     message = {
        //       id: 'joinRoom',
        //       senderName: this.senderName,
        //       roomId: this.roomID,
        //       bodyPix: this.isDegradation,
        //       device: this.typeBrowser,
        //       avatar: this.avatar,
        //       serverUrl: this.serverUrl,
        //       isDevUrl: this.isDevUrl,
        //       hasCamera: this.hasCamera,
        //       userId: this.userId
        //     }
        //   }
        //
        //   This.isrep = true
        //   this.sendMessage(message);
        //
        //   setTimeout(() => {
        //     if (This.isrep) {
        //       this.isCallSessionActive = true
        //       // на полный экран
        //
        //       // document.documentElement.requestFullscreen();
        //
        //       STREAM_VIDEO_FON = null
        //       This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.senderName, this.hasCamera, this.typeBrowser, this.isTranslateFunc, this.isDevGC, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO, this.serverUrl, this.isDevUrl)
        //       // }
        //
        //       This.isSpinStart = false
        //       this.infoOut()
        //
        //     }
        //     this.isOneUser = true
        //   }, 300)
        //
        //
        // }, 500)
      },
      /**endregion*/
      //endregion __________________________________________________________________________________

      //region Настройки видио и аудио ________This.videoQualtiySendValue, This.videoQualtiyRecvValue___________________________________________________________________________

      /**region saveSettingsCall Сохранить и приминить изменения*/
      saveSettingMain(_audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue, _isMirror, _isDegradationSett,
                      _isMicroWithSpaceBar) {
        console.log('saveSettingMain Сохранить и применить изменения*')

        var update = [false, false, false, false, false, false]

        /**
         * ИЗМЕНЕНИЕ БЫЛО ЗДЕСЬ **/
        if(this.isMicroWithSpaceBar != _isMicroWithSpaceBar){
          this.isMicroWithSpaceBar = _isMicroWithSpaceBar;
          console.log('saveSettingMain this.isMicroWithSpaceBar', this.isMicroWithSpaceBar)
        }
        /**
         * КОНЕЦ ИЗМЕНЕНИЙ **/

        if( this.audioInputValue != _audioInputValue){
          console.log('saveSettingCall this.audioInputValue ='+ _audioInputValue)
          this.audioInputValue = _audioInputValue
          this.setLocalStorage("gc.audioInputValue", _audioInputValue)
          update[0]=true
        }

        if(this.audioOutputValue != _audioOutputValue){
          console.log('saveSettingCall this.audioOutputValue ='+ _audioOutputValue)
          this.audioOutputValue = _audioOutputValue
          this.setLocalStorage("gc.audioOutputValue", _audioOutputValue)
          update[1]=true
        }

        if(this.videoValue != _videoValue){
          console.log('saveSettingCall this.videoValue ='+ _videoValue)
          this.videoValue = _videoValue
          this.setLocalStorage("gc.videoValue", _videoValue)
          update[2]=true
        }

        if(this.videoQualtiySendValue != _videoQualtiySendValue){
          console.log('saveSettingCall this.videoQualtiySendValue ='+ _videoQualtiySendValue)
          this.videoQualtiySendValue = _videoQualtiySendValue
          this.setLocalStorage("gc.videoQualtiySendValue", _videoQualtiySendValue)
          update[3]=true
        }

        if(this.videoQualtiyRecvValue != _videoQualtiyRecvValue){
          console.log('saveSettingCall this.videoQualtiyRecvValue ='+ _videoQualtiyRecvValue)
          this.videoQualtiyRecvValue = _videoQualtiyRecvValue
          this.setLocalStorage("gc.videoQualtiyRecvValue", _videoQualtiyRecvValue)
          update[4]=true
        }

        if(this.isMirror != _isMirror){
          console.log('saveSettingCall this.isMirror ='+ _isMirror)
          this.isMirror = _isMirror
          this.setLocalStorage("gc.mirror", _isMirror)
          update[5]=true
        }

        if(update[2]){
          This.stopVideoTrackPreview()

          var constraints = {
            video: {
              optional: [
                {sourceId: this.videoValue},
              ]
            },
            audio: false
          }

          if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
            navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
              STREAM_VIDEO = stream
            }).then(function () {

              console.log('$(`#videoPre`)[0].videoTracks()')

              console.log($(`#videoPre`)[0].srcObject)
              This.previewVideo()
            });
          }
        }
      },

      onErrorDevices(text, code) {
        console.log('onErrorDevices text ' + text + ' code ' + code)
        this.isDeviceYes = true
        if (code == 0) {
          this.isDeviceYes = true
          This.hasCamera = true
          This.hasMic = true
          console.log('ДОСТУП К КАМЕРЕ ЕСТЬ')
        } else if(code == 3){// нет только камеры
          This.isDownVideo = false
          This.hasCamera = false
          This.hasMic = true
          console.log('нет устройства '+text)
        } else if(code == 7){// нет только микрофона
          this.isDeviceYes = true
          This.hasCamera = true
          This.hasMic = false
          console.log('нет устройства '+text)
        } else if(code == 8){// нет микрофона и камеры
          This.isDownVideo = false
          This.hasCamera = false
          This.hasMic = false
          console.log('нет устройства '+text)
        } else if (text == 'NotAllowedError: Permission denied' || 'NotReadableError: Could not start video source' || code == 6) {
          // this.isDeviceYes = false
          //this.$root.showErr(this.$root.showErr(this.$t('ex.error.info.text1')))
          console.log('error (!)', this.$t('ex.error.info.text1'))
          //this.isStreamVideo = false // sig
          This.isDownVideo = false
          This.hasCamera = false
          This.hasMic = true
        } else if (text == 'ND') {
          this.isDeviceYes = false
          this.$root.showErr(this.$root.showErr(this.$t('ex.error.info.text2')))
        } else {
          this.isDeviceYes = false
          this.$root.showErr(this.$root.showErr(this.$t('ex.error.info.text3')))
          console.error('Нет доступа к камере и микрофону, возможно их занимает другое приложение. код: ' + code + ' текст ошибки: ' + text)
        }
      }
      ,
      /**endregion*/

      /**region saveSettingCall */
      saveSettingCall(_audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue, _isMirror, _isDegradationSett) {
        console.log('saveSettingCall сохранить изменения если идет звонок')
        console.log('saveSettingCall _STREAM_VIDEO', _audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue, _isMirror, _isDegradationSett)

        var update = [false, false, false, false, false, false]

        if( this.audioInputValue != _audioInputValue){
          console.log('saveSettingCall this.audioInputValue ='+ _audioInputValue)
          this.audioInputValue = _audioInputValue
          this.setLocalStorage("gc.audioInputValue", _audioInputValue)
          update[0]=true
        }

        if(this.audioOutputValue != _audioOutputValue){
          console.log('saveSettingCall this.audioOutputValue ='+ _audioOutputValue)
          this.audioOutputValue = _audioOutputValue
          this.setLocalStorage("gc.audioOutputValue", _audioOutputValue)
          update[1]=true
        }

        if(this.videoValue != _videoValue){
          console.log('saveSettingCall this.videoValue ='+ _videoValue)
          this.videoValue = _videoValue
          this.setLocalStorage("gc.videoValue", _videoValue)
          update[2]=true
        }

        if(this.videoQualtiySendValue != _videoQualtiySendValue){
          console.log('saveSettingCall this.videoQualtiySendValue ='+ _videoQualtiySendValue)
          this.videoQualtiySendValue = _videoQualtiySendValue
          this.setLocalStorage("gc.videoQualtiySendValue", _videoQualtiySendValue)
          update[3]=true
        }

        if(this.videoQualtiyRecvValue != _videoQualtiyRecvValue){
          console.log('saveSettingCall this.videoQualtiyRecvValue ='+ _videoQualtiyRecvValue)
          this.videoQualtiyRecvValue = _videoQualtiyRecvValue
          this.setLocalStorage("gc.videoQualtiyRecvValue", _videoQualtiyRecvValue)
          update[4]=true
        }

        if(this.isMirror != _isMirror){
          console.log('saveSettingCall this.isMirror ='+ _isMirror)
          this.isMirror = _isMirror
          this.setLocalStorage("gc.mirror", _isMirror)
          update[5]=true
        }

        try {
          if (STREAM_VIDEO) {
            STREAM_VIDEO.getTracks().forEach(function (track) {
              if (track.kind == 'video') {
                track.stop();
              }
            });
          }
          if ($(`#videoPre`)[0]) {
            $(`#videoPre`)[0].srcObject.getTracks().forEach(function (track) {
              if (track.kind == 'video') {
                track.stop();
              }
            });
          }
        } catch (e) {
          console.error('e main 5  ', e)
        }

        This.$refs.roomXRCallWin.saveSettingsCall(update, _audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue)
      }
      ,
      /**endregion*/

      /**region valueSett Получение изначальных настрокек видео и аудио*/
      valueSett(_audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue,
                _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {

        console.log('valueSett ', _audioInputValue, _audioOutputValue, _videoValue,
          _videoQualtiySendValue, _videoQualtiyRecvValue,
          _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO)

        this.audioInputValue = _audioInputValue
        this.audioOutputValue = _audioOutputValue
        this.videoValue = _videoValue

        this.videoQualtiySendValue = _videoQualtiySendValue
        this.videoQualtiyRecvValue = _videoQualtiyRecvValue

        // this.isDegradation = _isDegradationSett

        try {
          if (STREAM_VIDEO) {

            console.log('main 1 STREAM_VIDEO ', STREAM_VIDEO)

            STREAM_VIDEO.getTracks().forEach(function (track) {

              console.log('this.videoValue ' + This.videoValue)
              console.log('track.label' + track.label)

              if (track.kind == 'video') {
                track.stop();
                console.log('track.stop()')
              }
            });

          } else {
            console.log('main 1  else STREAM_VIDEO ', STREAM_VIDEO)
          }
        } catch (e) {
          console.error('e main 1  ', e)
        }

        var constraints = {
          video: {
            optional: [
              {sourceId: this.videoValue}
            ]
          },
          audio: false
        }
        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
            STREAM_VIDEO = stream
          }).then(function () {
            STREAM_AUDIO = _STREAM_AUDIO
            This.previewVideo()
          });
        }
        // if (this.isDegradation) {
        //   $(`#videoPre`).css('width', '1px')
        //   $(`#videoPre`).css('height', '1px')
        // } else {
        //   $(`#videoPre`).css('width', '100%')
        //   $(`#videoPre`).css('height', '100%')
        // }
      }
      ,
      /**endregion*/

      /**region clickDialogSett вызов диалога настроек*/
      clickDialogSett() {
        This.$refs.dialogSettCall.show()
      },
      /**endregion*/

      /**region onDlgSettCall вызов диалога настроек из звонка*/
      onDlgSettCall() {
        This.$refs.dialogSettCall.showCall()
      },
      /**endregion*/
      //endregion

      //region Функции для рисования _____________________________________________________________________________________
      /**region sendMessageCall отправка сообщение по ws*/
      sendMessageCall(message) {
        This.$refs.roomXRCallWin.sendMessage(message)
      },

      saveScreenshot(senderId){
        This.$refs.roomXRCallWin.saveScreenshot(senderId)
      },
      /**endregion*/

      /**region setPaint событие вкл. выкл. рисования*/
      setPaint(isPaint, sender) {

        This.$refs.roomXRCallWin.setPaint(isPaint, sender)
      },

      setArrow(isArrow, sender) {

        This.$refs.roomXRCallWin.setArrow(isArrow, sender)
      },
      /**endregion*/


      /**region funcUpdateIconMenu событие для иконок меню*/
      funcUpdateIconMenu(isMain, sender) {
        This.$refs.roomXRCallWin.funcUpdateIconMenu(isMain, sender)
      },
      /**endregion*/

      /**region showFuncOper событие для вкл. функций рисования*/
      showFuncOper(senderIdOper) {
        this.$refs.funcOperator.show(senderIdOper, This.roomID, This.senderId, This.$refs.roomXRCallWin.getOrient(senderIdOper), This.$refs.roomXRCallWin.getDelta(senderIdOper))
      },

      closeFuncOper(senderIdOper) {
        this.$refs.funcOperator.closePanel()
      },

      setDataPhone() {
        this.$refs.funcOperator.setDataPhone(this.oDraw, this.deltaCanvas)
      },

      /**endregion*/
      //endregion

      //region Функции stream и размытия _________________________________________________________________________________
      /**region*/
      addAudioTrack() {
        navigator.getUserMedia = navigator.getUserMedia ||
          navigator.webkitGetUserMedia ||
          navigator.mozGetUserMedia;
        if (navigator.getUserMedia) {
          navigator.getUserMedia({
              audio: true,
              video: false
            },
            function (stream) {
              STREAM_AUDIO = stream
            },
            function (err) {
              console.error("The following error occured: " + err.name)
            });
        } else {
          console.log("getUserMedia not supported");
        }
      }
      ,
      /**endregion*/

      /**region onDegradation применение размытия в диалоге*/
      onDegradation(_videoValue) {

      },
      // async onDegradation(_videoValue){
      //   // await This.$refs.vueStreamRef.init().then((stream) => {
      //   // })
      //   // setTimeout(()=>{
      //   //   This.$refs.vueStreamRef.init().then((stream) => {
      //   //     return stream
      //   //   })
      //   // },2000)
      //   //
      //   //
      //   //
      //   //
      //   //
      //   //
      //   //
      //   //
      //   // This.$refs.vueStreamRef.init().then((stream) => {
      //   //   return stream;
      //   // })
      // },
      /**endregion*/

      /**region replaceStream запуск размытия экрана*/
      // async replaceStream(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
      //   // if(This.videoValue){
      //   //   This.videoConstraints = {optional: [{sourceId: This.videoValue}]}
      //   // }else{
      //   //   This.videoConstraints = false
      //   // }
      //   //
      //   // await This.$refs.vueStreamRef.init().then((stream) => {
      //   //   // STREAM_VIDEO = _STREAM_VIDEO
      //   //   // STREAM_AUDIO = _STREAM_AUDIO
      //   //   // STREAM_VIDEO_FON = stream
      //   //   // This.$refs.roomXRCallWin.saveSett(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
      //   // })
      //   // setTimeout(()=>{
      //   //   This.$refs.vueStreamRef.init().then((stream) => {
      //   //     STREAM_VIDEO = _STREAM_VIDEO
      //   //     STREAM_AUDIO = _STREAM_AUDIO
      //   //     STREAM_VIDEO_FON = stream
      //   //     This.$refs.roomXRCallWin.saveSettingsCall(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
      //   //   })
      //   // },500)
      //
      // },
      /**endregion*/


      /**region*/
      async initStream() {
        // console.log('sig-test initStream - start')
        // console.log('initStream', This.videoValue)
        //if (This.videoValue) {
        if (This.videoValue != null) {
          This.videoConstraints = {optional: [{sourceId: This.videoValue}]}
        } else {
          This.videoConstraints = false
        }
        // console.log('initStream', This.videoValue, This.videoConstraints)

        // if (This.videoValue != null) {
        //   await This.$refs.vueStreamRef.init().then((stream) => {
        //     STREAM_VIDEO_FON = stream
        //     console.log('sig-test initStream - stream', stream)
        //   })
        // }
        // console.log('sig-test initStream - end')
      },
      /**endregion*/
      //endregion

      //region Доп функуии ###############################################################################################

      /**region режим разработчика, нагрузка сети отправки пакетов*/
      onLevelRTT(isDev, level) {

        if (level < 3) {
          $('#idLevelRTT').hide()
        } else if (level < 6) {
          $('#idLevelRTT').show()
          $('#idLevelRTT').css('color', 'orange')
        } else {
          $('#idLevelRTT').show()
          $('#idLevelRTT').css('color', 'red')
        }
        this.levelRTT = `${this.$t('levelRTT')}: ${level}`
      },

      /**region confirmAgree ответ диалога*/
      confirmAgree(type, mess) {
        if (type == 1) {
          This.sendMessage(mess);
        } else if (type == 2) {
          This.sendMessage(mess);
        }
      }
      ,
      /**endregion*/

      /**region confirmCancel ответ диалога*/
      confirmCancel() {

      }
      ,
      /**endregion*/

      /**region clickInfoShow Открытие окна с информацией*/
      clickInfoShow() {
        this.isShowInfo = !this.isShowInfo
      }
      ,

      setBackground(url) {
        this.urlBackgroundFon = url
      },

      // infoOver(){
      //   if(!this.isFirefox){
      //     $('#idRowInfo').css('width', '-webkit-fill-available')
      //   }else{
      //     $('#idRowInfo').css('width', '-moz-available')
      //   }
      //
      //   $('#divInfoNameRoom').show()
      //   $('#time-node').show()
      //   this.showMiniInfo=true;
      // },

      infoOut() {
        // this.showMiniInfo = false
        // setTimeout(()=>{
        //   if(!this.isShowInfo && !this.showMiniInfo){
        //     if(This.$refs.roomXRCallWin.isMyFullVideo){
        //       $('#idRowInfo').css('width', '60px')
        //     }else{
        //       $('#idRowInfo').css('width', '158px')
        //     }
        //
        //     $('#divInfoNameRoom').hide()
        //     $('#time-node').hide()
        //   }
        // },500)


      },
      /**endregion*/

      /**region*/
      chatNewMessage(parsedMessage) {
        console.log('chatNewMessage message', parsedMessage.message)
        This.$refs.chatMessage.newRecvMessage(parsedMessage.message)
        This.$refs.roomXRCallWin.newMessageChat(parsedMessage.message.content, parsedMessage.message.username)
      },

      /**region*/
      chatNewMessageOper(parsedMessage) {
        var message = JSON.parse(parsedMessage.message);
        console.log('chatNewMessageOper message', message)
        This.$refs.chatMessage.newRecvMessage(message)
        This.$refs.roomXRCallWin.newMessageChat(message.content, message.username)
      },

      setDeviceParam(paramMessage) {

        console.log('setDeviceParam paramMessage', paramMessage)

        This.wDraw = paramMessage.width
        This.hDraw = paramMessage.height
        This.oDraw = paramMessage.orient

        if (This.oDraw == 'L') {
          This.deltaCanvas = This.wDraw / This.hDraw
          console.log('delta L ' + This.deltaCanvas + ' w ' + paramMessage.width + ' h ' + paramMessage.height)
        } else {
          This.deltaCanvas = This.hDraw / This.wDraw
          console.log('delta P ' + This.deltaCanvas + ' w ' + paramMessage.width + ' h ' + paramMessage.height)
        }

        This.$refs.roomXRCallWin.setDelta(paramMessage.idTo, This.deltaCanvas)

        this.setDataPhone()
      },

      planUpdateMessage(messages) {
        setTimeout(() => {
          console.log('plan ', messages)
          var planMessage = JSON.parse(messages);
          for (var i = 0; i < planMessage.length; i++) {
            console.log('m ', planMessage[i])
            This.$refs.chatMessage.setPlanEl(planMessage[i])
          }
        }, 2000)
      },

      chatUpdateMessage(messages) {
        setTimeout(() => {
          console.log('chat ', messages)
          var chatMessage = JSON.parse(messages);
          for (var i = 0; i < chatMessage.length; i++) {
            console.log('m ', chatMessage[i])
            This.$refs.chatMessage.newRecvMessage(chatMessage[i])
          }
          var message = {
            id: 'sendMyPlan',
            roomId: This.roomID,
          };
          this.sendMessage(message)
        }, 1000)
        // This.$refs.chatMessage.updateMessages(messages)
      },

      chatIsPlan(gcChatId, isPlan) {
        var message = {
          id: 'chatIsPlan',
          room: This.roomID,
          gcChatId: gcChatId,
          isPlan: isPlan
        };
        this.sendMessage(message)
      },

      chatSendMessage(senderId, message) {

        console.log('send chat senderId  ' + senderId)

        var message = {
          id: 'chatSendMessage',
          senderName: This.senderName,
          room: This.roomID,
          senderId: senderId,
          message: message
        };

        console.log('chatSendMessage message ', message)

        this.sendMessage(message)
      },

      addMyUserChat(senderId, senderName, avatar) {
        This.$refs.chatMessage.addMeUserChat(This.roomID, senderId, This.senderName, avatar, This.wsURL);
      },

      addUserChat(senderId, senderName, avatar) {
        console.log('addUserChat(senderId, senderName, avatar)', senderId, senderName, avatar)
        This.$refs.chatMessage.addUserChat(senderId, senderName, avatar);
      },

      showChat() {
        This.$refs.chatMessage.showChat();
      },

      setTranslation(isTranslate) {
        This.$refs.chatMessage.setTranslation(isTranslate);
      },

      clickCloseChat() {
        This.$refs.roomXRCallWin.setChatMessage(false);
      },

      clickCloseInfoUser() {
        This.$refs.roomXRCallWin.setPanelInfoUser(false);
      },

      addNewOper() {
        this.isDlgContactOper = true
        return this.$root.restGet(`/webcall/listGroupCallUs`)
          .then(r => {
            // this.$refs.contactOperatorFormVue.initD(r)
            This.$refs.contactOperatorFormVue.show(r)
          })
        // this.isDialogListOper = !this.isDialogListOper
        // if( this.isDialogListOper){
        //
        // }else{
        //   this.$refs.contactOperatorFormVue.onClear()
        // }
      },
      // this.$refs.funcOperator.show(sender, this.roomIDuserName)
      /**endregion*/

      emitAddUser(id, opersenderName) {
        this.$root.showInfo(this.$t('gc.addOperMessInfo'))

        var jsonex = {'dv_id': id, 'nameOper': opersenderName, 'gcID': This.roomID}

        console.log('ADD USER OPER json ', jsonex)

        this.$root.restPost('callea/postGCOperLink', jsonex)
          .then((res) => {
          })
      },

      addOperDialog() {
        this.isDialogListOper = true
        if (this.isDialogListOper) {
          var uri = `/webcall/listGroupCallUs`
          return this.$root.restGet(uri)
            .then(r => {
              // this.$refs.contactOperatorFormVue.initD(r)
              this.$refs.contactOperatorFormVue.show(r)
            })
        }

      },

      clickCloseUser(senderId, user) {
        This.$refs.roomXRCallWin.clickDeleteUserCall(senderId, user)
      },

      clickMic(senderId, userName) {
        /**
         * ИЗМЕНЕНИЕ БЫЛО ЗДЕСЬ **/
        console.log('clickMic')
        if(!this.isMicroWithSpaceBar){
          console.log('отключаем микрофон')
          if (userName.replace(/^\s/g, '').substring(0, 9) == 'Оператор_' || userName.replace(/^\s/g, '').substring(0, 9) == 'Operator_') {
            This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, true)
          } else {
            This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, false)
          }
        }
      },

      addUser(_senderId, senderName) {
        var message = {
          id: 'addUser',
          senderId: _senderId,
          room: This.roomID,
        }
        This.sendMessage(message);
      },

      disUser(_senderId, senderName) {
        var message = {
          id: 'disUser',
          senderId: _senderId,
          room: This.roomID,
        }
        This.sendMessage(message);
      },

      // this.$refs.funcOperator.show(sender, this.roomIDuserName)
      /**endregion*/

      setLocalStorage(name, value) {
        localStorage[name] = value
      },

      getLocalStorage(name, type) {
        return localStorage[name];
      },

      // eraseCookie(name) {
      //   document.cookie = name + '=;';
      // },

    //изменение статуса микрофона при подключении
    toggleMic(isStatus) {
      this.isStartMic = isStatus
      // if(this.isMicroWithSpaceBar)
      //   this.isStartMic = false
      // else
    },

    //изменение статуса микрофона при подключении
    toggleVideo(isStatus) {
      this.isStartVideo = isStatus
      // if(this.isMicroWithSpaceBar)
      //   this.isStartMic = false
      // else
    },

      //изменение статуса микрофона при подключении
      setStartMic() {
        this.isStartMic = !this.isStartMic
        // if(this.isMicroWithSpaceBar)
        //   this.isStartMic = false
        // else
      },

      //изменение статуса видео при подключении
      setStartVideo() {
        this.isStartVideo = !this.isStartVideo
      },
      //endregion

      getTimeTranslate() {

      },

      // отправка аудио на сервер
      sendBlobAudioTranslate(blob, langSrc, langTgt, timestamp) {
        console.log('blob ' + blob)
        var message = {
          id: 'sendBlobTranslate',
          messageId: -1,
          room: This.roomID,
          senderId: this.senderId,
          blob: blob,
          timestamp: timestamp,
          langSrc: langSrc,
          langTgt: langTgt,
          senderName: This.senderName,
          deviceId: '-1',
          deviceName: 'Chrome'
        };
        This.sendMessage(message);
      },

      // отправка текста на сервер для перевода
      sendTextTranslate(text, langSrc, langTgt, timestamp, messageId) {
        // console.log('room this.paramValueRoom '+this.paramValueRoom)
        // console.log('room This.paramValueRoom '+This.paramValueRoom)
        // console.log('room this.roomID '+this.roomID)
        console.log('senderId This.senderId '+This.senderId)
        console.log('text ' + text + ' langSrc ' + langSrc + ' langTgt ' + langTgt + ' timestamp ' + timestamp)
        var message = {
          id: 'sendTextTranslate',
          room: This.roomID,
          senderId: This.senderId,
          text: text,
          timestamp: timestamp,
          langSrc: langSrc,
          langTgt: langTgt,
          senderName: This.senderName,
          deviceId: '-1',
          deviceName: This.typeBrowser,
          messageId:messageId
        };
        console.log('sendTextTranslate message', message)
        This.sendMessage(message);
      },

      stopNoTranslate(timestamp){
        var message = {
          id: 'stopNoTranslate',
          room: This.roomID,
          senderId: This.senderId,
          text: '',
          timestamp: timestamp,
          langSrc: '',
          langTgt: '',
          senderName: This.senderName,
          deviceId: '-1',
          deviceName: This.typeBrowser
        };
        console.log('sendTextTranslate message', message)
        This.sendMessage(message);
      },

      //отправить событие начала записи
      soundRecAT(isStatus, langSrc, langTgt, wav) {
        var message = {
          id: 'soundRecAT',
          room: This.roomID,
          senderId: this.senderId,
          isStatus: isStatus,
          langSrc: langSrc,
          langTgt: langTgt,
          wav: wav
        };
        This.sendMessage(message);
      },

      //отправить событие начала записи
      setSoundRecAT(langSrc, langTgt) {

      },

      endedVideoBackground(e) {
        //isPreloading && !isCallSessionActive &&
        This.isVideoBackground = false
      },

      // Расчет height для региона отображения списка активных пользователей
      setSizeUserList() {
        let z = $('.userList');
        if (z && z.length != 0) {
          let t = $('#userNameHeader')
          let b = $('.right-footer')
          let h = b.offset().top - t.offset().top - t.height()
          z.height(h)
        }
      },

      asrtError(parsedMessage){
        console.log('asrtError ', parsedMessage)

        var textError = ''
        This.$refs.chatMessage.errorAudiotoText()

        if(This.paramValueLang == 'ru'){
          if(parsedMessage.errorObject.translationResult){
            textError = parsedMessage.errorObject.ru+' (№ ' +parsedMessage.errorObject.code+') \n'+parsedMessage.errorObject.translationResult.ru
          }else if(parsedMessage.errorObject.recognitionResult){
            textError = parsedMessage.errorObject.ru+' (№ '+parsedMessage.errorObject.code+') \n'+parsedMessage.errorObject.recognitionResult.ru
          }else{
            textError = parsedMessage.errorObject.ru+' (№ '+parsedMessage.errorObject.code
          }
        }else{
          if(parsedMessage.errorObject.translationResult){
            textError = parsedMessage.errorObject.en+' (№ '+parsedMessage.errorObject.code+') \n'+parsedMessage.errorObject.translationResult.en
          }else if(parsedMessage.errorObject.recognitionResult){
            textError = parsedMessage.errorObject.en+' (№ '+parsedMessage.errorObject.code+') \n'+parsedMessage.errorObject.recognitionResult.en
          }else{
            textError = parsedMessage.errorObject.en+' (№ '+parsedMessage.errorObject.code
          }
        }
        This.$root.showErr(textError)
      },

      // статуст что соединение не прошло или закончилось
      setInfoNoConnect(parsedMessage, type){
        console.log('setInfoNoConnect type ', type)
        if(type == 1)
          if(parsedMessage.isMy){
            This.$refs.noConnectInfoPanel.showInfo(this.$t('gc.title.group.call.error'), this.$t('gc.no.connect.info.1.panel.text'), this.$t('gc.title.group.call')+': '+this.nameRoom)
          }else{
            This.$root.showErr(parsedMessage.nameUser + this.$t('gc.no.connect.info.1.message'))
          }
      },

      //--------------------------------------------------------------
      updateDataOper(dataOpers){
        This.mediaAttr = dataOpers
        console.log('mediaAttr',This.mediaAttr)
      },

      openChikPanel(dataOpers) {
        console.log('openChikPanel dataOpers',dataOpers)
        this.$refs.chikPanel.open()
      },

      onChikPanelClose(){
        This.$refs.roomXRCallWin.setPanelChik(false);
      },

      stopVideoTrackPreview(){
        if(STREAM_VIDEO){
          console.log('остановить video трек ввезде')
          STREAM_VIDEO.getTracks().forEach(function(track) {
            track.stop();
          });
        }
      },
      //--------------------------------------------------------------
      sendScreenshotToChat(blob){
        this.$refs.chatMessage.formAndSendMessage(blob);
      },

      startRunIsUsersAll(){
        console.log('startRunIsUsersAll ');
        clearTimeout(This.timeoutAllUsers);

        This.timeoutAllUsers = setTimeout(function run() {
          if (This.roomID && This.ws) {
            var message = {
              id: 'isUsLinkAll',
              room: This.roomID,
            }
            This.sendMessage(message);
          }
          setTimeout(run, 1000);
        }, 1000);
      },

      startRunIsUsers(){
        console.log('startRunIsUsers ');
        clearTimeout(This.timeoutUsers);

        This.timeoutUsers = setTimeout(function run() {
          if (This.roomID && This.ws) {
            var message2 = {
              id: 'isUsLink',
              room: '' + This.roomID,
            }
            This.sendMessage(message2);
          }
          setTimeout(run, 1000);
        }, 1000);
      },

      startRunIsStartCall(type){
        console.log('startRunIsStartCall ',type);
        clearTimeout(This.timeoutId);

        This.timeoutId = setTimeout(() => {
          console.log('startRunIsStartCall 10 секунд прошло', This.isStartCall);
          if(!This.isStartCall){
            console.log('startRunIsStartCall 10 секунд прошло', This.joinRoomMessage);
            This.restartWS()
            This.startRunIsStartCall(3)
          }
        }, 10000);
      },

      restartWS(){
        console.log('restartWS')
        this.ws.close()
        this.ws = null

        This.$refs.roomXRCallWin.restartWinCall()

        setTimeout(()=>{
          This.restartSession()
        }, 2000)
      },

      devLog(type){
        //this.restartWS();
      }
    }
}
</script>

<style scoped>
/* ==================== ОСНОВНЫЕ СТИЛИ ==================== */
#gcMain.gc-main-container {
  width: 100%;
  height: 100%;
  padding: 0 !important;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 200;
  overflow: hidden;
}

/* Фон */
.video-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.video-background video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.background-dim {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: var(--v-xr2L3-base);
  z-index: 2;
}

/* Скрытая кнопка */
.hidden-control-button {
  position: absolute;
  top: 10px;
  right: 10px;
  z-index: 1000;
  background: transparent;
  border: none;
  color: rgba(255, 255, 255, 0.3);
  cursor: pointer;
  font-size: 20px;
  padding: 8px;
  border-radius: 50%;
  transition: all 0.3s;
}

.hidden-control-button:hover {
  color: rgba(255, 255, 255, 0.8);
  background: rgba(0, 0, 0, 0.2);
}

/* ==================== ПЕРЕХОДЫ ==================== */
.page-fade-enter-active,
.page-fade-leave-active {
  transition: all 0.5s ease;
}

.page-fade-enter-from,
.page-fade-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

/* ==================== СОСТОЯНИЯ ==================== */
.state-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 3;
}

.no-link-state {
  background: var(--v-xr2L1-base);;
}

.state-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 40px;
  max-width: 500px;
  width: 90%;
  text-align: center;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
}

.state-icon {
  font-size: 60px;
  color: var(--v-xr2L1-base);
  margin-bottom: 20px;
}

.state-title {
  font-size: 1.8rem;
  font-weight: 700;
  color: #333;
  margin-bottom: 15px;
}

.state-description {
  color: #666;
  font-size: 1.1rem;
  line-height: 1.5;
  margin-bottom: 30px;
}

.state-actions {
  display: flex;
  justify-content: center;
  gap: 15px;
}

/* Состояние загрузки */
.preloading-state {
  background: #1e1f22;
}

.loading-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 6px;
  padding: 40px;
  max-width: 500px;
  width: 90%;
  text-align: center;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
}

.loading-spinner.large {
  margin: 0 auto 30px;
}

.loading-spinner.large .spinner {
  width: 60px;
  height: 60px;
  border: 4px solid rgba(102, 126, 234, 0.2);
  border-radius: 50%;
  border-top-color: #ffc033;
  animation: spin 1s linear infinite;
  margin: 0 auto;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.loading-title {
  font-size: 1.5rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 10px;
}

.loading-description {
  color: #666;
  margin-bottom: 25px;
}

.loading-progress {
  margin-top: 25px;
}

.progress-bar {
  height: 8px;
  background: #e0e0e0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 10px;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--v-xr2L3-base), var(--v-xr2L1-base));
  border-radius: 4px;
  transition: width 0.3s ease;
}

.progress-text {
  font-size: 0.9rem;
  color: #666;
}

/* ==================== СЕССИЯ ==================== */
.session-stage-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 3;
}

/* PIP видео */
.local-video-pip {
  position: absolute;
  bottom: 100px;
  right: 20px;
  width: 200px;
  height: 150px;
  z-index: 1000;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
  cursor: move;
  transition: all 0.3s;
}

.local-video-pip.dragging {
  opacity: 0.8;
}

.local-video-pip.minimized {
  width: 100px;
  height: 75px;
}

.pip-video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.pip-controls {
  position: absolute;
  top: 5px;
  right: 5px;
  display: flex;
  gap: 5px;
  opacity: 0;
  transition: opacity 0.3s;
}

.local-video-pip:hover .pip-controls {
  opacity: 1;
}

.pip-toggle-btn :deep(.v-btn),
.pip-close-btn :deep(.v-btn) {
  background: rgba(0, 0, 0, 0.7) !important;
  color: white !important;
  width: 28px !important;
  height: 28px !important;
  min-width: 28px !important;
}

/* ==================== УВЕДОМЛЕНИЯ ==================== */
.notifications-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 10000;
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 350px;
}

.notification {
  background: white;
  border-radius: 10px;
  padding: 15px;
  display: flex;
  align-items: center;
  gap: 15px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.15);
  cursor: pointer;
  transition: transform 0.3s, opacity 0.3s;
  border-left: 4px solid #f4c325;
}

.notification:hover {
  transform: translateX(-5px);
}

.notification-success {
  border-left-color: #4CAF50;
}

.notification-warning {
  border-left-color: #FF9800;
}

.notification-error {
  border-left-color: #f44336;
}

.notification-info {
  border-left-color: #2196F3;
}

.notification-icon {
  font-size: 1.5rem;
  flex-shrink: 0;
}

.notification-success .notification-icon {
  color: #4CAF50;
}

.notification-warning .notification-icon {
  color: #FF9800;
}

.notification-error .notification-icon {
  color: #f44336;
}

.notification-info .notification-icon {
  color: #2196F3;
}

.notification-content {
  flex: 1;
}

.notification-title {
  font-weight: 600;
  color: #333;
  margin: 0 0 5px 0;
  font-size: 0.95rem;
}

.notification-message {
  color: #666;
  margin: 0;
  font-size: 0.85rem;
  line-height: 1.4;
}

.notification-close {
  flex-shrink: 0;
}

.notification-close :deep(.v-btn) {
  width: 24px !important;
  height: 24px !important;
  min-width: 24px !important;
  background: transparent !important;
  color: #999 !important;
}

.notification-slide-enter-active,
.notification-slide-leave-active {
  transition: all 0.4s ease;
}

.notification-slide-enter-from {
  opacity: 0;
  transform: translateX(100px);
}

.notification-slide-leave-to {
  opacity: 0;
  transform: translateX(100px);
}

/* ==================== ПАНЕЛЬ ОТЛАДКИ ==================== */
.debug-panel {
  position: fixed;
  bottom: 20px;
  left: 20px;
  z-index: 10000;
  background: rgba(0, 0, 0, 0.9);
  color: #fff;
  border-radius: 10px;
  max-width: 400px;
  max-height: 300px;
  overflow: hidden;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
}

.debug-panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 15px;
  background: rgba(255, 255, 255, 0.1);
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.debug-panel-header h4 {
  margin: 0;
  font-size: 0.9rem;
  font-weight: 600;
}

.debug-panel-header :deep(.v-btn) {
  background: transparent !important;
  color: #fff !important;
}

.debug-panel-content {
  padding: 15px;
  overflow-y: auto;
  max-height: 250px;
  font-size: 0.8rem;
  line-height: 1.4;
}

.debug-panel-content pre {
  margin: 0;
  white-space: pre-wrap;
  word-wrap: break-word;
}

/* ==================== АДАПТИВНОСТЬ ==================== */
@media (max-width: 768px) {
  .state-card,
  .loading-card {
    padding: 30px 20px;
    width: 95%;
  }

  .local-video-pip {
    width: 150px;
    height: 112px;
    bottom: 80px;
  }

  .notifications-container {
    left: 20px;
    right: 20px;
    max-width: none;
  }

  .debug-panel {
    right: 20px;
    left: 20px;
    max-width: none;
  }
}

@media (max-width: 480px) {
  .state-title {
    font-size: 1.5rem;
  }

  .state-icon {
    font-size: 50px;
  }

  .local-video-pip {
    width: 120px;
    height: 90px;
    bottom: 70px;
  }
}

/* ==================== АНИМАЦИИ ФОНА ==================== */
.fade-out-enter-active,
.fade-out-leave-active {
  transition: opacity 0.5s ease;
}

.fade-out-enter-from,
.fade-out-leave-to {
  opacity: 0;
}

/* ==================== ФОНЫ ==================== */
.background-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  pointer-events: none; /* Не блокирует клики */
}

.background-overlay.background-loading {
  background: linear-gradient(135deg, var(--v-xr2L4-base) 0%, var(--v-xr2L5-base) 100%);
}

.background-overlay.background-start {
  background: linear-gradient(135deg, var(--v-xr2L4-base) 0%, var(--v-xr2L5-base) 100%);
  opacity: 0.7; /* Полупрозрачный для стартового экрана */
}

/* ==================== ОСНОВНОЙ КОНТЕНТ ==================== */
.main-content {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2; /* Всегда поверх фона */
  pointer-events: all; /* Кликабельный */
}

/* Если хотите, чтобы GCStartStage имел свой фон */
.start-stage-with-bg {
  width: 100%;
  height: 100%;
  background: transparent; /* Или свой градиент */
}

/* Анимации для контента */
.page-fade-enter-active,
.page-fade-leave-active {
  transition: all 0.3s ease;
}

.page-fade-enter-from,
.page-fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}


.winVideoRoomDN {
  display: none;
}

.noBackgroundSrcS {
  width: 110px !important;
  left: calc(50% - 55px) !important;
}

.noBackground {
  background: transparent !important;
}


@keyframes marching-ants {
  0% {
    background-position: 0 0, 10px 100%, 0 10px, 100% 0;
  }
  100% {
    background-position: 10px 0, 0 100%, 0 0, 100% 10px;
  }
}

.btnVideo {
  padding: 2px;
  background-image: -webkit-gradient(linear, left top, right top, color-stop(50%, #8d8d8d), color-stop(50%, transparent)), -webkit-gradient(linear, left top, right top, color-stop(50%, #8d8d8d), color-stop(50%, transparent)), -webkit-gradient(linear, left top, left bottom, from(0), color-stop(50%, #8d8d8d), color-stop(50%, transparent)), -webkit-gradient(linear, left top, left bottom, from(0), color-stop(50%, #8d8d8d), color-stop(50%, transparent));
  background-image: linear-gradient(
    90deg, #8d8d8d 50%, transparent 50%), linear-gradient(
    90deg, #8d8d8d 50%, transparent 50%), linear-gradient(0, #8d8d8d 50%, transparent 50%), linear-gradient(0, #8d8d8d 50%, transparent 50%);
  background-repeat: repeat-x, repeat-x, repeat-y, repeat-y;
  background-size: 10px 2px, 10px 2px, 2px 10px, 2px 10px;
  -webkit-animation: marching-ants 400ms infinite linear;
  animation: marching-ants 100ms infinite linear;
}

.div-mainwin {
  z-index: 2;
  position: absolute;
  top: 0;
  width: 100%;
}

.p-mainwin {
  color: wheat;
  position: inherit;
  text-align: center;
  width: 85%;
  background-color: #71717112;
}

.p-mainwinFuncOther {
  color: wheat;
  position: inherit;
  text-align: left;
  width: 85%;
  background-color: rgba(66, 65, 65, 0.07);
}

.i-mainwin {
  font-size: 14px;
}

button.ml-6.fabIconLayout.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default.white {
  width: 60px;
  height: 44px;
  margin: 0;
  border-radius: 0;
  margin-left: 4px !important;
  right: 1px !important;
  position: relative;
}

button.fabAddSet {
  margin: 0 !important;
  border-bottom-right-radius: 0px !important;
  border-top-right-radius: 0px !important;
  height: 44px !important;
  position: relative !important;
}

button.ml-6.fabIconLayout1.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  width: 60px;
  height: 44px;
  margin: 0;
  border-radius: 0;
  margin-left: 1px !important;
  margin-top: 2px;
  margin-bottom: 2px;
  right: 1px !important;
  position: relative;
}

button.ml-6.fabIconLayout2.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  width: 60px;
  height: 44px;
  margin: 0;
  border-radius: 0;
  margin-left: 1px !important;
  margin-top: 2px;
  margin-bottom: 2px;
  right: 1px !important;
  position: relative;
}

button.ml-6.fabIconLayout3.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  width: 60px;
  height: 44px;
  margin: 0;
  border-radius: 0;
  margin-left: 1px !important;
  margin-top: 2px;
  margin-bottom: 2px;
  right: 1px !important;
  position: relative;
}

.rowIconLayout {
  pointer-events: all;
  position: absolute;
  right: 1px;
  bottom: 80px;
  width: 60px;
}

.div-moreWin {
  position: absolute;
  right: calc(50% - 120px);
  bottom: 82px;
  z-index: 17;
}

.itemList-more {
  height: 38px;
  min-height: 0px;
  border-bottom: solid 1px rgba(0, 0, 0, 0.21);
  padding-left: 10px;
}

.itemList-more-itemicon {
  min-width: 0;
  min-height: 0;
  margin: 0;
  margin-right: 14px !important;
  width: 20px;
  height: 100%;
}

.itemList-more-icon {
  font-size: 18px !important;
}

.itemList-more-title {
  font-size: 16px !important;
}

.divDevBlockOper {

}

.canvasPrintOther {
  position: absolute;
  background: transparent;
  z-index: 1;
  top: 19%;
  width: 1px;
  height: 1px;
  left: 1px;
}

.rowSaveCall {
  z-index: 1000;
  position: absolute;
  height: 27px;
  bottom: 0px;
  padding: 0px;
  padding-bottom: 32px;
  margin: 0;
  right: 0;
  border-radius: 5px;
}

/*i.v-icon.notranslate.iconSvaeCall.fas.fa-record-vinyl.theme--light{*/
/*  color: rgb(193, 52, 52);*/
/*  height: 38px;*/
/*  font-size: 18px;*/
/*  padding-left: 6px;*/
/*}*/

button.fabAddSet.save.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default.xr-btn-normal {
  background-color: #c13434 !important;
  color: #f0f0f0;
  margin: 0 !important;
  margin-right: 4px !important;
  font-size: 14px;
  min-width: 54px;
}

.devVideoBlockFunc {
  background: rgba(0, 0, 0, 0.5);
  width: 40px;
  height: 40px;
  position: absolute;
  top: calc(50% - -14px);
  left: calc(50% - 18px);
  z-index: 212541;
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext {
  z-index: 10;
  width: 30px !important;
  height: 30px !important;
  min-width: 30px;
  background: rgba(255, 255, 255, 0.80);
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext.type1 {
  left: 2px;
  border-top-right-radius: 0;
  border-bottom-right-radius: 0;
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext.type2 {
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
  left: -2px;
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext.type3 {
  border-radius: 0;
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext.type5 {
  left: 2px;
  border-top-right-radius: 0;
  border-top-left-radius: 0;
}

.imgDevIconNoVideoTextDevMainMin {
  width: 30px;
  height: 30px;
  border-radius: 84px !important;;
  background: #6ba5ff !important;;
  font-size: 36px !important;;
  text-align: center !important;;
  font-family: revert !important;
  margin-top: 28px;
}

.imgDevIconNoVideoTextDevBig {
  width: 240px;
  height: 240px;
  border-radius: 284px !important;
  background: #6ba5ff !important;
  font-size: 36px !important;
  text-align: center !important;
  font-family: revert !important;
  margin-top: -78px;
  margin-left: -64px;
}

.vFooterRoom {
  z-index: 16
}

.infoTooldevPanel {
  position: absolute;
  right: 0;
  width: 400px;
  max-width: 400px;
  z-index: 10;
}

.cardPositionGC {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  overflow: hidden;
  background: black;
}

.cardPositionGCBlack {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  overflow: hidden;
  background: black;
}

/*.pos100GC {*/
/*  position: absolute;*/
/*  width: 100%;*/
/*  height: 100%;*/
/*}*/

.divMainpos100GCPhone {
  position: absolute;
  width: 100%;
  height: 100%;
  margin: 0 !important;
  padding: 0 !important;
}

.posWH100 {
  width: 100%;
  height: 100%;
}

.progText {
  height: 50px;
  /*color: #056abf;*/
  text-align: center;
  font-weight: 500;
  width: 100% !important;
  top: calc(50% + 30px);
  z-index: 1222;
  position: absolute;
}

.pNoLink {
  text-align: center;
  margin-top: 50px;
  font-size: 24px;
}

.videoPreVideoIst {
  width: 100px;
  height: 100px;
}

.akVideoLocalDiv {
  height: 416px;
  align-items: center;
  border-bottom-left-radius: 8px;
  border-bottom-right-radius: 8px;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  box-shadow: rgba(60, 64, 67, 0.3) 0px 1px 2px 0px, rgba(60, 64, 67, 0.15) 0px 1px 3px 1px;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-basis: 740px;
  flex-direction: column;
  flex-grow: 0;
  flex-shrink: 1;
  font-size: 16px;
  height: 416px;
  line-height: 24px;
  margin-bottom: 16px;
  margin-left: 16px;
  margin-right: 8px;
  margin-top: 16px;
  min-width: 448px;
  overflow-x: hidden;
  overflow-y: hidden;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  position: relative;
  tab-size: 4;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  width: 740px;
  word-break: normal;
  -webkit-box-align: center;
  -webkit-box-direction: normal;
  -webkit-box-flex: 0;
  -webkit-box-orient: vertical;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.akStreamVideoDiv {
  background-repeat-x:;
  background-repeat-y:;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  display: block;
  font-size: 16px;
  height: 416px;
  left: 0px;
  line-height: 24px;
  margin-bottom: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-top: 0px;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  position: absolute;
  tab-size: 4;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  top: 0px;
  width: 740px;
  word-break: normal;
  -webkit-box-direction: normal;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.akStreamVideo {
  background-repeat-x:;
  background-repeat-y:;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  display: block;
  font-size: 16px;
  height: 418px;
  left: -1px;
  line-height: 24px;
  margin-bottom: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-top: 0px;
  object-fit: contain;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  pointer-events: none;
  position: absolute;
  tab-size: 4;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  top: -1px;
  transform: matrix(-1, 0, 0, 1, 0, 0);
  width: 742px;
  word-break: normal;
  -webkit-box-direction: normal;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.divInfoGC {
  align-items: center;
  background-repeat-x:;
  background-repeat-y:;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-basis: 448px;
  flex-direction: column;
  flex-grow: 0;
  flex-shrink: 0;
  font-size: 16px;
  height: 540px;
  justify-content: center;
  line-height: 24px;
  margin-bottom: 16px;
  margin-left: 8px;
  margin-right: 16px;
  margin-top: 16px;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  position: relative;
  tab-size: 4;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  width: 448px;
  word-break: normal;
  -webkit-box-align: center;
  -webkit-box-direction: normal;
  -webkit-box-flex: 0;
  -webkit-box-orient: vertical;
  -webkit-box-pack: center;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.infoLabel {
  background-repeat-x:;
  background-repeat-y:;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  cursor: default;
  display: block;
  font-size: 28px;
  font-weight: 400;
  height: 36px;
  letter-spacing: normal;
  line-height: 36px;
  margin-bottom: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-top: 0px;
  overflow-x: hidden;
  overflow-y: hidden;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  tab-size: 4;
  text-align: center;
  text-overflow: ellipsis;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  white-space: nowrap;
  width: 400px;
  word-break: normal;
  -webkit-box-direction: normal;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.div2Info {
  background-repeat-x:;
  background-repeat-y:;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  font-size: 16px;
  height: 540px;
  justify-content: center;
  line-height: 24px;
  margin-bottom: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-top: 0px;
  padding-bottom: 0px;
  padding-left: 0px;
  padding-right: 0px;
  padding-top: 0px;
  tab-size: 4;
  text-rendering: optimizelegibility;
  text-size-adjust: 100%;
  width: 448px;
  word-break: normal;
  -webkit-box-direction: normal;
  -webkit-box-flex: 1;
  -webkit-box-orient: vertical;
  -webkit-box-pack: center;
  -webkit-font-smoothing: antialiased;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.carWinH {
  height: 488px;
}

.ma-2.cardWinH.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0 {
  height: 488px;
}

/*.ma-2.vCard48.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0 {*/
/*  border-top-left-radius: 10px !important;*/
/*  border-bottom-left-radius: 10px !important;*/
/*  height: 65%;*/
/*  width: 45%;*/
/*  margin: 0px !important;*/
/*}*/

/*.ma-2.vCard482.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0 {*/
/*  border-top-right-radius: 10px !important;*/
/*  border-bottom-right-radius: 10px !important;*/
/*  height: 65%;*/
/*  width: 30%;*/
/*  margin: 0px !important;*/
/*  margin-left: -1px !important;*/
/*  background: white;*/
/*}*/

.pWidth {
  border: solid 1px #9e9e9e !important;
  background: #eeeeee;
  border-radius: 5px;
  padding: 8px;
  margin-left: 8px;
  margin-right: 8px;
  margin-bottom: 4px !important;
  overflow: auto;
  height: 80px;
  font-size: 16px;
  color: #606060;
}

.pWidthRoom {
  /*color: #434343;*/
  border-radius: 5px;
  padding: 8px;
  font-size: 16px;
  margin-bottom: 4px !important;
}

.v-text-field.v-input--has-state .v-input__control > .v-text-field__details > .v-counter, .v-text-field.v-input--is-disabled .v-input__control > .v-text-field__details > .v-counter, .v-text-field.v-input--is-disabled .v-input__control > .v-text-field__details > .v-messages, .v-text-field .v-input__control, .v-text-field fieldset {
  height: 42px;
}

footer.v-footer.vFooterRoom.v-sheet.theme--light.v-footer--absolute.v-footer--padless {
  background: transparent;
}

.winVideoRoom2 {
  object-fit: contain;

  width: 100%;
  height: 100%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev2 {
  width: 100%;
  height: 100%;
  position: absolute;
}

.winVideoRoom3 {
  object-fit: contain;

  width: 50%;
  height: 100%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev3 {
  width: 50%;
  height: 100%;
  position: absolute;
}

.winVideoRoom45 {
  object-fit: contain;

  width: 50%;
  height: 50%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev45 {
  width: 50%;
  height: 50%;
  position: absolute;
}

.winVideoRoom67 {
  object-fit: contain;

  width: 33%;
  height: 50%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev67 {
  width: 33%;
  height: 50%;
  position: absolute;
}

.winVideoRoom89 {
  object-fit: contain;

  width: 25%;
  height: 50%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev89 {
  width: 25%;
  height: 50%;
  position: absolute;
}

.winVideoRoom101112 {
  object-fit: contain;

  width: 25%;
  height: 33%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev101112 {
  width: 25%;
  height: 33%;
  position: absolute;
}


.winVideoRoom13_16 {
  object-fit: contain;

  width: 25%;
  height: 25%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDev13_16 {
  width: 25%;
  height: 25%;
  position: absolute;
}

.winVideoRoomFrom17 {
  object-fit: contain;

  width: 25vw;
  height: 100%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

.winVideoRoomDevFrom17 {
  width: 25vw;
  height: 100%;
  position: absolute;
}

.winVideovideoCanvas {
  object-fit: contain;
  width: 100%;
  height: 100%;
}

.winVideovideo {
  object-fit: contain;
  width: 100%;
  height: 100%;
}

.winVideoRoomInfo.row {
  padding-left: 0px
}

.winVideoRoomInfo {
  padding-left: 0px;
  background: linear-gradient(to top, #00000066, #0000000d);
  position: absolute;
  width: 100%;
  height: 34px;
  bottom: 0;
  border-radius: 5px;
  color: #ffffff;
  font-size: 16px;
}

.pdivVideoRow {
  width: 70%;
  overflow: hidden;
  display: unset;
  text-overflow: ellipsis;
  -moz-box-orient: vertical;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  line-clamp: 3;
  box-orient: vertical;
  margin: 4px;
  verflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev {
  height: 50px;
  padding: 0px !important;
  background-color: #dedede !important;
  margin: 0;
  background: #dedede !important;
}

.winVideoLocal {
  object-fit: contain;
  width: 100%;
  height: 100%;
}

div.container2 {
  margin-left: 6px;
  padding-right: 6px;
  width: 50px;
  position: relative;
  font-size: 20px;
  font-weight: 400;
  /*border-right: solid 1px rgba(0, 0, 0, 0.21);*/
}

/* 1 */
div.container2 p {
  margin: 0;
  position: absolute; /* 2 */
  top: 47%; /* 3 */
  transform: translate(0, -50%)
}

/* 1 */
p.containerGNane {
  cursor: default;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  float: left;
  font-weight: 400;
  text-align: start;
  margin: 0;
  margin-left: 62px;
  width: calc(100% - 62px);
  height: 50px;
  position: inherit;
  z-index: 10;
  text-shadow: -1px 0 rgb(0 0 0 / 68%), 0 1px rgb(0 0 0 / 68%), 1px 0 rgb(0 0 0 / 0%), 0 -1px rgb(0 0 0 / 0%);
  /*color: white;*/
  color: var(--v-xr2L5-base);
  top: 28px;
  font-size: 18px;
  -webkit-transform: translate(0, -50%);
  transform: translate(0, -50%);
}

div.container3 {
  margin-left: 50px;
  top: 14px;
  right: 190px;
  width: 43px;
  height: 30px;
  position: absolute;
  z-index: 70;
}

/* 1 */
div.container3 p {
  cursor: default;
  margin: 0;
  margin-left: 50px;
  width: 168px;
  height: 50px;
  position: inherit;
  z-index: 10;
  text-shadow: -1px 0 rgba(0, 0, 0, 0.68), 0 1px rgba(0, 0, 0, 0.68), 1px 0 rgba(0, 0, 0, 0), 0 -1px rgba(0, 0, 0, 0);
  /*font-family: sans;*/
  color: white;
  top: 47%; /* 3 */
  transform: translate(0, -50%)
}

button.divinfo2Btn.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default {
  background: transparent;
  margin: 0;
  width: 60px;
  height: 50px;
  border-color: transparent;
  border-bottom-left-radius: 23px;
  padding-left: 0px !important;
  padding-right: 16px !important;
  padding-bottom: 4px !important;
  -webkit-box-shadow: 0 2px 4px -1px rgb(0 0 0 / 0%), 0 4px 5px 0 rgb(0 0 0 / 0%), 0 1px 10px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 2px 4px -1px rgb(0 0 0 / 0%), 0 4px 5px 0 rgb(0 0 0 / 0%), 0 1px 10px 0 rgb(0 0 0 / 0%);
}

.winVideoRoomDevMy {
  position: absolute !important;
  width: 100% !important;
  height: 100% !important;
}

.devVideoBlockMy {
  /* background: rgba(0, 0, 0, 0.5); */
  border-radius: 20px;
  width: 100%;
  height: 100%;
  position: absolute;
  top: calc(50% - 30px);
  left: calc(50% - 53px);
  /* top: calc(50%); */
  /* left: calc(50%); */
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybtnDraw {
  width: 50px;
  height: 50px;
  border-radius: 50px;
  left: 4px;
  top: 3px;
  background: rgba(255, 255, 255, 0.98);
}

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontextmy {
  z-index: 10;
  width: 40px !important;
  height: 40px !important;
  min-width: 40px;
  border-radius: 50px;
  left: 4px;
  top: 7px;
  background: rgba(255, 255, 255, 0.80);
}

.ma-2.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0.v1 {
  width: 50%;
  height: 100%;
  margin: 0 !important;
}

.ma-2.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0.v2 {
  width: 100%;
  height: 50%;
  position: fixed;
  top: 0;
  left: 0;
  margin: 0 !important;
}

.ma-2.v-card.v-sheet.v-sheet--outlined.theme--light.rounded-0.v3 {
  width: 100%;
  height: 50%;
  position: fixed;
  top: 50%;
  left: 0;
  margin: 0 !important;
}

.infoDevShow {
  text-align: left;
  width: 399px;
  background: white;
  top: 0px;
  position: absolute;
  right: 0;
  padding-top: 50px;
  z-index: 10;
}

.pInfoTool {
  margin-bottom: 8px !important;
  margin-top: 16px;
  background: #dedede;
  padding-left: 10px;
  padding-top: 0px;
  font-size: 16px;
  font-weight: 500;
}

.pInfoData {
  padding-left: 10px;
  padding-top: 0px;
  font-size: 16px;
  font-weight: 400;
  margin-bottom: 8px !important;
}

.onePre {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  z-index: 1;
  padding: 0;
  margin: 0;
}

.divQO3Z3c {
  pointer-events: none;
  background-color: rgba(0, 0, 0, 0.21);
  margin: 8px 0 11px 0px;
  width: 1px;
}

.v-menu__content.theme--light.menuable__content__active {
  z-index: 1200000 !important;
}

.fabAddSetSS {
  position: absolute;
  left: 4px;
  bottom: 80px;
  font-size: 30px;
  width: 50px !important;
  height: 50px !important;
  border-radius: 50px;
}

.winVideoLocalScreen {
  position: absolute;
  top: 0;
  left: 0;
  width: 400px;
  z-index: 900000000000000000;
}

.screenVideoN {
  object-fit: contain;
}

.newMyVideoClass {
  height: 100%;
  position: absolute;
}

/*button.ml-6.btnSettingPrev.v-btn {*/
/*padding-top: 4px!important;*/
/*border: solid 1px #9e9e9e !important;*/
/*position: absolute;*/
/*left: 8px;*/
/*bottom: 8px;*/
/*font-size: 30px;*/
/*width: 50px;*/
/*height: 50px;*/
/*border-radius: 50px;*/
/*margin-left: 0 !important;*/
/*box-shadow: 0 3px 5px -1px rgba(0, 0, 0, 0.11), 0 6px 10px 0 rgba(0, 0, 0, 0.08), 0 1px 18px 0 rgba(0, 0, 0, 0.05);*/
/*}*/

/*button.btnCallPrev {*/
/*  position: absolute;*/
/*  right: 8px;*/
/*  bottom: 8px;*/
/*  margin-left: 0 !important;*/
/*}*/

span.headlineSett {
  left: -6px !important;
  font-size: 22px !important;
}

.vcheckboxSettings {
  padding: 4px;
  margin-top: 0px;
}

.vselectSettings {
  padding: 8px;
}

.v-select__selections {
  line-height: 26px;
  padding-left: 8px !important;
}

/*.videoPrevDiv {*/
/*  object-fit: contain;*/
/*  border-bottom-left-radius: 10px;*/
/*}*/


.fas {
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  display: inline-block;
  font-style: normal;
  font-variant: normal;
  text-rendering: auto;
  line-height: 1;
}

.fa-circle:before {
  content: "\F111";
}

.fa-palette:before {
  content: "\F53F";
}

.fas {
  font-family: Font Awesome\ 5 Free;
}

.fas {
  font-weight: 900;
}

*, :after, :before {
  background-repeat: no-repeat;
  -webkit-box-sizing: inherit;
  box-sizing: inherit;
}

:after, :before {
  text-decoration: inherit;
  vertical-align: inherit;
}

* {
  padding: 0;
  margin: 0;
}

input {
  border-radius: 0;
}

input {
  font: inherit;
}

input {
  background-color: transparent;
  border-style: none;
}

img {
  border-style: none;
}

.v-application ::-ms-clear, .v-application ::-ms-reveal {
  display: none;
}

.v-application .text-center {
  text-align: center !important;
}

.row {
  display: -webkit-box;
  /*display: -ms-flexbox;*/
  display: flex;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  -webkit-box-flex: 1;
  -ms-flex: 1 1 auto;
  flex: 1 1 auto;
  margin-right: -12px;
  margin-left: -12px;
}

/*! CSS Used from: https://xr-new-dev.inlinegroup-c.ru/css/app.543ce3aa.css */
.row {
  margin: 0;
}

.sp-preview, .sp-replacer {
  -webkit-user-select: none;
  -moz-user-select: -moz-none;
  -o-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.sp-replacer {
  margin: 0;
  overflow: hidden;
  cursor: pointer;
  display: inline-block;
  *zoom: 1;
  *display: inline;
  border: 1px solid #91765d;
  color: #333;
  vertical-align: middle;
  height: 20px;
  position: relative;
}

.sp-replacer:hover {
  border-color: #f0c49b;
  color: #111;
}

.sp-dd {
  padding: 2px 2px;
  height: 1px;
  width: -2px;
  line-height: 13px;
  float: left;
  font-size: 8px;
  position: absolute;
  right: 4px;
  top: 0;
  color: #000;
}

.sp-preview {
  width: 25px;
  height: 70px;
  border: 1px solid #222;
  margin-right: 5px;
  margin-top: 10px;
  float: left;
  z-index: 0;
}

.sp-preview {
  position: relative;
  background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAwAAAAMCAIAAADZF8uwAAAAGUlEQVQYV2M4gwH+YwCGIasIUwhT25BVBADtzYNYrHvv4gAAAABJRU5ErkJggg==);
}

.sp-preview-inner {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}

.sp-replacer {
  width: 99%;
  height: 22px;
  border: none;
  padding-bottom: 5px;
  border-bottom: 1px solid #91765d;
}

.sp-replacer .sp-preview {
  width: 100%;
}

.sp-dd {
  display: none;
}

.iconPosIc {
  color: #343434;
  font-size: 20px;
  position: absolute;
  margin-left: -9px;
  margin-top: 9px;
}

.pointerTrail {
  z-index: 3000;
  height: 16px;
  width: 16px;
  border-radius: 8px;
  background: #000;
  position: fixed;
  pointer-events: none;
}

div#idRowSett {
  width: 55px;
  position: absolute;
  bottom: 568px;
  margin-left: 59px;
  z-index: 50000;
}

.sp-replacer.sp-light {
  position: revert;
  padding: 0;
  margin-right: 2px;
  left: 50%;
  background: #fff;
  border-radius: 30px !important;
  height: 40px !important;
  width: 40px !important;
  font-weight: 700;
  font-size: 12px;
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
}

.sp-preview {
  position: relative;
  width: 43px !important;
  height: 42px;
  margin: 0;
  padding: 0;
  border: 1px solid rgba(75, 75, 75, 0);
  top: -2px;
  left: -2px;
}

.clVrowRange {
  bottom: 42px;
}

.clVrowColor, .clVrowRange {
  background: #fff;
  position: absolute;
  left: 0;
  width: 200px;
  height: 48px;
  border-radius: 5px;
  padding: 4px;
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12);
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12);
}

.clVrowColor {
  bottom: -4px;
}

.clVrowType {
  background: #fff;
  position: absolute;
  left: 0;
  width: 250px;
  bottom: 89px;
  height: 48px;
  border-radius: 5px;
  padding: 4px;
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12);
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12);
}

.settSizeDraw {
  margin-right: 8px;
  border: 1px solid #4b4b4b;
  left: 50%;
  background: #fff;
  border-radius: 30px !important;
  height: 40px !important;
  width: 40px !important;
  font-weight: 700;
  font-size: 12px;
  border: 1px solid rgba(2, 2, 2, .07) !important;
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
}

.pointerTrailS1 {
  width: 8px;
  height: 8px;
  margin-left: 15px;
  margin-top: 15px;
}

.pointerTrailS1, .pointerTrailS2 {
  background: #000;
  border-radius: 20px;
}

.pointerTrailS2 {
  width: 12px;
  height: 12px;
  margin-left: 13px;
  margin-top: 13px;
}

.pointerTrailS3 {
  width: 16px;
  height: 16px;
  margin-left: 11px;
  margin-top: 11px;
}

.pointerTrailS3, .pointerTrailS4 {
  background: #000;
  border-radius: 20px;
}

.pointerTrailS4 {
  width: 20px;
  height: 20px;
  margin-left: 9px;
  margin-top: 9px;
}

.pointerColorS1 {
  background: #ff050c;
}

.pointerColorS2 {
  background: #121212;
}

.pointerColorS3 {
  background: #3d84ff;
}

.pointerColorS4 {
  background: #5eff4f;
}

.settBtnSize, .settBtnSize1 {
  margin: 4px;
  border: 1px solid #b4b4b4;
  position: relative;
  bottom: 0;
  background: #fff;
  border-radius: 30px;
  height: 40px;
  width: 40px;
  font-weight: 700;
  font-size: 12px;
  border: 1px solid rgba(2, 2, 2, .07);
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
}

.settBtnSize1 {
  padding-top: 8px;
  top: 2px;
}

.settBtnSize2 {
  padding-top: 4px;
  margin: 4px;
  border: 1px solid #b4b4b4;
  position: relative;
  bottom: 0;
  top: 4px;
  background: #fff;
  border-radius: 30px;
  height: 40px;
  width: 40px;
  font-weight: 700;
  font-size: 12px;
  border: 1px solid rgba(2, 2, 2, .07);
  -webkit-box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, .2), 0 6px 10px 0 rgba(0, 0, 0, .14), 0 1px 18px 0 rgba(0, 0, 0, .12) !important;
}

.disNone {
  display: none;
}

* {
  margin: 0;
  padding: 0;
}

.noVisIcon {
  display: none !important;
}

.avMediaLocalVideo {
  position: absolute;
  bottom: 4px;
  left: 4px;
}

.screenSharingCss {
  object-fit: contain !important;
}

.isNOVideoCssDiv {
  position: absolute;
}

.isNOVideoMic1 {
  position: absolute;
}

.isNOVideoVoise {
  position: absolute;
}

.isNOVideoCssMic {
  position: absolute;
}

.theme--light.v-btn:not(.v-btn--flat):not(.v-btn--text):not(.v-btn--outlined) {
  background-color: transparent;
}


.v-input__append-inner {
  padding-top: 4px;
}

.ff12New {
  position: absolute;
  top: 3000px;
}

.devDevIconNoVideoText {
}

.fabIconLayout {
  position: absolute;
  right: 16px;
  font-size: 30px;
  width: 50px;
  height: 50px;
  border-radius: 50px;
}

.v-app_isPhone {
  position: revert;
}

.info-list-users {
  max-height: 264px;
  overflow-y: auto;
  background: #dedede;
}

.info-item-users {
  margin-top: 1px;
  margin-bottom: 1px;
  background: white;
  height: 42px;
  min-height: 1px;
}

.divinfo2ISpan {
  margin: 10px;
  margin-right: 16px !important;
  margin-left: 0px;
}

.divinfo2I {
  font-size: 22px;
}

.vDivTestRow {
  height: 100%;
  width: -webkit-fill-available;
  position: absolute;
  overflow-x: auto;
  overflow-y: hidden;
}

.vRowMin16Video1 {
  height: 25%;
  width: max-content;
  margin: 0;
  margin-top: 0 !important;
  position: absolute;
}

.vRowMin16Video2 {
  height: 25%;
  top: 25%;
  width: max-content;
  margin: 0;
  margin-top: 0 !important;
  position: absolute;
}

.vRowMin16Video3 {
  height: 25%;
  top: 50%;
  width: max-content;
  margin: 0;
  margin-top: 0 !important;
  position: absolute;
}

.vRowMin16Video4 {
  height: 24%;
  top: 75%;
  width: max-content;
  margin: 0;
  margin-top: 0 !important;
  position: absolute;
}


/*!* полоса прокрутки (скроллбар) *!*/
/*::-webkit-scrollbar {*/
/*  width: 24px; !* ширина для вертикального скролла *!*/
/*  height: 16px; !* высота для горизонтального скролла *!*/
/*  background-color: #f3faf7;*/
/*}*/

/*!* ползунок скроллбара *!*/
/*::-webkit-scrollbar-thumb {*/
/*  background-color: #279df3;*/
/*  border-radius: 9em;*/
/*  box-shadow: inset 1px 1px 10px #279df3;*/
/*}*/

/*::-webkit-scrollbar-thumb:hover {*/
/*  background-color: #279df3;*/
/*}*/

.row.pa-6.text-center.infoTooldevPanel {
  margin: 0;
  padding: 0 !important;
  height: 30px;
  width: 15%;
}

.divinfo2 {
  margin: 0;
  z-index: 10;
  width: 100%;
  padding: 0px;
  height: 50px;
  border-color: transparent;
  border-bottom-left-radius: 26px;
  /*border-right: solid 1px rgba(0, 0, 0, 0.21);*/
}

.pErrorDevices {
  font-size: 24px;
  color: red;
  font-weight: 400;
  width: 100%;
  text-align: center;
  top: calc(50% - 30px);
  z-index: 1222;
  position: absolute;
}

.clVideoMirror {
  transform: rotateY(180deg);
  -webkit-transform: rotateY(180deg);
  -moz-transform: rotateY(180deg);
}

.divBtnPreGC {

}

/*button.mr-1.btnFooterMic {*/
/*  pointer-events: all !important;*/
/*  font-size: 30px !important;*/
/*  width: 44px !important;*/
/*  height: 44px !important;*/
/*  border-bottom-right-radius: 0 !important;*/
/*  border-top-right-radius: 0 !important;*/
/*  border-top-left-radius: 5px!important;*/
/*  border-bottom-left-radius: 5px!important;*/
/*  padding-top: 8px!important;*/
/*}*/

/*button.mr-1.btnFooterVid {*/
/*  pointer-events: all !important;*/
/*  font-size: 30px !important;*/
/*  width: 44px !important;*/
/*  height: 44px !important;*/
/*  border-bottom-right-radius: 5px !important;*/
/*  border-top-right-radius: 5px !important;*/
/*  border-top-left-radius: 0!important;*/
/*  border-bottom-left-radius: 0!important;*/
/*  padding-top: 8px!important;*/
/*}*/

/*.row.divBtnPreGC {*/
/*  position: absolute;*/
/*  bottom: 12px;*/
/*  left: calc(50% - 50px);*/
/*}*/

.devDevIconNoVideoPre {
  border-radius: 0;
  background: black;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

/*------------------------------------------------------------------*/
.pos100GC {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0px;
  bottom: 0px;
}

.videoPrevData{
  z-index: 90870790879806879068;
  display: block!important;
}

/*------------------------------------------------------------------*/
.xr2gc .vCard-1 {
  border-top-left-radius: 10px !important;
  border-bottom-left-radius: 10px !important;
  height: 65%;
  width: 45%;
  margin: 0px !important;
  background: var(--v-xr2D2-base);
  min-height: 300px;
  min-width: 200px;
  border-width: 3px;
  border-color: var(--v-xr2D2-base);
}

.xr2gc .vCard-2 {
  border-top-right-radius: 10px !important;
  border-bottom-right-radius: 10px !important;
  height: 65%;
  width: 30%;
  margin: 0px !important;
  margin-left: -1px !important;
  /*background: var(--v-xr2L4-base);*/
  background: var(--xr-gc-pane-background-color);
  border-width: 3px;
  border-color: var(--v-xr2D2-base);
  /*border-left: none;*/
  min-height: 300px;
  min-width: 230px;
}

.xr2gc .vCard-2 .v-card__title {
  border-top-right-radius: 6px !important;
  background: var(--xr-gc-pane-header-background-color);
}

.xr2gc .vCard-2 .v-card__title > span{
  color: var(--xr-gc-pane-header-color);
}

.xr2gc .videoPrevDiv {
  object-fit: contain;
  /*border-radius: 10px !important;*/
  border-top-left-radius: 10px !important;
  border-bottom-left-radius: 10px !important;
}

.xr2gc .divBtnPreGC {
  position: absolute;
  bottom: 12px;
  left: calc(50% - 50px);
}

.xr2gc .btnFooterMic {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-right-radius: 0 !important;
  border-top-right-radius: 0 !important;
  border-top-left-radius: 5px !important;
  border-bottom-left-radius: 5px !important;
  padding-top: 8px !important;
}

.xr2gc .btnFooterVid {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-right-radius: 5px !important;
  border-top-right-radius: 5px !important;
  border-top-left-radius: 0 !important;
  border-bottom-left-radius: 0 !important;
  padding-top: 8px !important;
}

.xr2gc .btnSetting {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-radius: 5px !important;

  position: absolute;
  bottom: 12px;
  right: 12px;
}

.xr2gc .userList {
  overflow: auto;
}

.xr2gc .right-footer {
  position: absolute;
  bottom: 8px;
  width: 100%;
  /*background: var(--v-xr2L4-base);*/
  background: var(--xr-gc-pane-background-color);
  /*background: red;*/
  padding-left: 12px;
  padding-right: 12px;
}

.xr2gc .right-footer >>> label {
  background: var(--v-xr2L4-base);
  font-size: 16px !important;
}

.xr2gc .btnCallPrev {
  margin-top: 6px;
}

/*------------------------------------------------------------------*/
.mainCircular {
  height: 50px;
  width: 100% !important;
  top: calc(50% - 25px);
  z-index: 1222;
  position: absolute;
}

/*------------------------------------------------------------------*/
.vCard-2 ::-webkit-scrollbar {
  height: 6px; /* высота для горизонтального скролла */
  width: 6px;
}

.vCard-2 ::-webkit-scrollbar-track {
  background: #343434;
}

.vCard-2 ::-webkit-scrollbar-thumb {
  /*background: #318de2;*/
  background: var(--v-xr2L1-base);
  border-radius: 2px;
}

.vCard-2 ::-webkit-scrollbar-thumb:vertical:hover,
.vCard-2 ::-webkit-scrollbar-thumb:horizontal:hover {
  /*background: #47c0ff;*/
  background: var(--v-xr2L1-base);
}

.vCard-2 :hover::-webkit-scrollbar-thumb {
  /*background: #318de2;*/
  background: var(--v-xr2L1-base);
}

.xr2gc .toolbar-accent {
  background: var(--xr-gc-pane-accent-background-color);
  color: var(--xr-gc-pane-accent-color);
}

.infoUserLinsIkonImgGC{
  width: 34px;
  height: 34px;
  border-radius: 36px;
  margin-right: 8px;
}

.infoUserOperImgGC{
  font-size: 14px;
  display: inherit;
  margin-right: 8px;
  border-radius: 34px;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  width: 18px;
  height: 18px;
  position: absolute;
  top: 22px;
  left: 22px;
  background: #ffffff;
}

.infoUserLinsIkonScrsGC{
  color: var(--v-xr2D2-base);
  font-size: 14px;
  display: inherit;
  min-width: 34px;
  height: 34px;
  margin-right: 8px;
  border-radius: 34px;
  align-items: center;
  justify-content: center;
  background: var(--v-xr2L1-base);
}

/*.infoUserOperImgGC{*/
/*  display: inherit;*/
/*  min-width: 34px;*/
/*  height: 34px;*/
/*  margin-right: 8px;*/
/*  border-radius: 34px;*/
/*  align-items: center;*/
/*  justify-content: center;*/
/*  background: var(--v-xr2L1-base);*/
/*}*/

/*.disable-events {*/
/*  pointer-events: none*/
/*}*/
</style>
