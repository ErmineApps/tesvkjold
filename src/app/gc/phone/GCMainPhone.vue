<template>
  <div id="gcMainPhone" class="gcMainPhoneCss">
    <div id="mainRoomPhone" class="cardPositionGC xr2gcPh">
      <div v-if="isWaitConnection" id="idDivWaitConnectionPh" class="divWaitConnPh">

        <v-card
          class="ma-2 divWaitConnBlockIPh"
          outlined
          tile
        >
          <v-card-title>
            <div class="pl-1 pr-2 ">
              <span>{{ textTitleWait }}</span>
            </div>
          </v-card-title>

          <v-card-text class="vctWaitt">
            <div class="pl-1 pr-2 vpspanWait">
              <span>{{ textWait }}</span>
            </div>
            <v-progress-circular
              v-if="isWaitAdd"
              :size="50"
              class="mainCircularStartWt"
              color="primary"
              indeterminate
            ></v-progress-circular>
          </v-card-text>

          <v-card-actions class="pl-3 pr-3">
            <v-row>
              <!--              <XrBtn-->
              <!--                v-if="isWaitAdd"-->
              <!--                class="btnCallPrev"-->
              <!--                @click="closeCallWh"-->
              <!--                type="hot"-->
              <!--                :text="$t('gc.exit')"-->
              <!--                :title="$t('gc.exit')"/>-->
            </v-row>
          </v-card-actions>
        </v-card>
      </div>

      <button id="butDisNone" class="ff12New">
        <span aria-hidden="true" class="mdi mdi-grid-off v-btn_ot_ex_sp v-span-video"></span>
      </button>

      <div v-if="isVideoBackground" class="cardPositionGCPrev">
        <!--              <div class="cardPositionGC" style="z-index: 12312412">-->
        <div class="text-center pos100GC">
          <video-background :loop="false"
                            :src="'/mdb/audio/mmIksarGCMS.mp4'"
                            style="max-height: 100%; height: 100%;"
                            @ended="endedVideoBackground"></video-background>
          <!--            <div :data-vide-bg="mmiksar2" data-vide-options="playbackRate: 0.1, position: '0 100%'">-->
          <!--              <div style="padding:200px 10px; font-size:30px; text-align:center; color: #BFE2FF">-->
          <!--                Адаптивное видео для фона-->
          <!--              </div>-->
          <!--            </div>-->
          <!--            <img :src="av_gif_" alt="" style=" width: 580px;-->
          <!--              height: 245px;-->
          <!--              position: absolute;-->
          <!--              top: calc(50% - 124px);-->
          <!--              left: calc(50% - 290px);">-->
          <!--            <p style="-->
          <!--          color: black;-->
          <!--          position: inherit;-->
          <!--          text-align: center;-->
          <!--          width: 100%;-->
          <!--          top: calc(50% - -47px);">{{ gcLinkInput }}</p>-->
        </div>
      </div>
      <div v-if="!isActiveLink && !isPreloading && !isCallSessionActive" class="mx-auto">
        <p v-t="'gc.mess.no-link-available'" class="pNoLink"></p>
      </div>

      <!-- ############################################################################ -->
      <!-- Стартовая страница                                                           -->
      <!-- ############################################################################ -->
      <div v-if="isActiveLink && !isPreloading && !isCallSessionActive" class="pos100GC">
        <v-row id="divMainpos100GC" class="posH100">
          <div class="dImgLabelGC">
            <img src="/img/IKSAR-Logo-Rus-Yellow-Dot-white.d8fae3af.png" width="130" >
          </div>

          <v-card-title class="xr2L1" style="width: 100%">
            <span style="margin-left: 8px">{{ $t('gc.prev.title') }}</span>
          </v-card-title>

          <v-col v-if="isOrientland" class="posH100" cols="12">
            <v-row style="height: 100%">
              <v-col
                cols="6"
                md="6"
              >
                <v-card id="vcardId1" class="vCard-1PhP posH100Pgcmain" outlined tile>

                  <v-card-text class="pa-0">
                    <!-- Наименование группы -->
                    <!--                    <div id="userNameHeader" class="pl-1 pr-1 mb-0 xr2D2 xr2L2&#45;&#45;text">-->
                    <div id="userNameHeader" class="pl-1 pr-1 mb-0 toolbar-accent">
                      <p class="pWidthRoom">{{ nameRoomBigGr }}</p>
                    </div>

                    <!-- Текущие активные пользователи -->
                    <div class="userListPh pl-2 pr-2 mt-0" style="height: 29vw">
                      <template v-if="isUserCallList">
                        <v-list class="mt-0" color="xr2L4" disabled>
                          <v-list-item v-for="(item, i) in usersCallListName" :key="i" dense>
                            <v-list-item-title>
                              <v-icon class="mr-1" color="green darken-1">mdi-account-circle-outline</v-icon>
                              {{ i + 1 }}. {{ item }}
                            </v-list-item-title>
                          </v-list-item>
                        </v-list>
                      </template>
                      <template v-else>
                        <v-alert class="ma-3 pa-2" color="xr2D2" icon="mdi-fire" outlined text>
                          <!-- В данном групповом звонке пока нет активных пользователей. Вы будете первым. -->
                          {{ $t('gc.no-members-alert') }}
                        </v-alert>
                      </template>
                    </div>

                  </v-card-text>
                </v-card>
              </v-col>
              <v-col
                cols="6"
                md="6"
              >
                <v-card id="vcardId2" class="vCard-2PhP posH100Pgcmain" outlined tile>
                  <!-- Футер правой части -->
                  <div class="right-footer">

                    <hr class="v-divider xr2L1 mt-1 mb-2"/>

                    <!-- Имя пользователя (вводим руками) -->
                    <v-row>
                      <v-text-field
                        v-model="senderName"
                        :label="$t('gc.name-reg')"
                        :max-length="400"
                        :prepend-icon="'mdi-account-outline'"
                        :rules="[rules.required]"
                        height="36px"
                        outlined
                        placeholder=" "
                        required
                      />
                    </v-row>

                    <v-row>
                      <!-- Пароль для входа (если требуется) -->
                      <v-col class="mr-4">
                        <v-text-field
                          v-if="isPw"
                          v-model="pwUser"
                          :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                          :label="$t('password') + ' *'"
                          :prepend-icon="'mdi-shield-key-outline'"
                          :rules="[rules.required]"
                          :type="show2 ? 'text' : 'password'"
                          autocomplete="new-password"
                          height="36px"
                          name="newPassword"
                          outlined
                          persistent-hint
                          placeholder=" "
                          @click:append="show2 = !show2"
                        />
                      </v-col>
                      <!-- Кнопка: Присоединиться -->
                      <v-col cols="auto">
                        <XrBtn
                          :icon="isSpinStart ? 'mdi-spin mdi-loading' : 'fas fa-phone'"
                          :text="$t('gc.prev.btn.ready')"
                          :title="$t('gc.prev.btn.ready.title')"
                          class="btnCallPrev mb-2"
                          type="hot"
                          @click="startCallGC">
                        </XrBtn>
                      </v-col>
                    </v-row>

                  </div>

                </v-card>
              </v-col>
            </v-row>
          </v-col>

          <v-col v-else class="posH100" cols="12">
            <v-row :align="'center'" :justify="'center'" class="colMainGCPh" >
              <!-- ============================================================== -->
              <!-- Левая часть -->
              <v-card id="vcardId1" class="vCard-1PhP" outlined tile>

                <v-card-text class="pa-0">
                  <!-- Наименование группы -->
                  <!--                  <div id="userNameHeader" class="pl-1 pr-1 mb-0 xr2D2 xr2L2&#45;&#45;text">-->
                  <div id="userNameHeader" class="pl-1 pr-1 mb-0 toolbar-accent">
                    <p class="pWidthRoom">{{ nameRoomBigGr }}</p>
                  </div>

                  <!-- Текущие активные пользователи -->
                  <div class="userListPh pl-2 pr-2 mt-0">
                    <template v-if="isUserCallList">
                      <v-list class="mt-0" color="xr2L4" disabled>
                        <v-list-item v-for="(item, i) in usersCallListName" :key="i" dense>
                          <v-list-item-title>
                            <v-icon class="mr-1" color="green darken-1">mdi-account-circle-outline</v-icon>
                            {{ i + 1 }}. {{ item }}
                          </v-list-item-title>
                        </v-list-item>
                      </v-list>
                    </template>
                    <template v-else>
                      <v-alert class="ma-3 pa-2" color="xr2D2" icon="mdi-fire" outlined text>
                        <!-- В данном групповом звонке пока нет активных пользователей. Вы будете первым. -->
                        {{ $t('gc.no-members-alert') }}
                      </v-alert>
                    </template>
                  </div>

                </v-card-text>
              </v-card>
              <!-- ============================================================== -->
              <!-- Правая часть -->
              <v-card id="vcardId2" class="vCard-2PhP" outlined tile>
                <!-- Футер правой части -->
                <div class="right-footer">

                  <hr class="v-divider xr2L1 mt-1 mb-2"/>

                  <!-- Имя пользователя (вводим руками) -->
                  <v-row>
                    <v-text-field
                      v-model="senderName"
                      :label="$t('gc.name-reg')"
                      :max-length="400"
                      :prepend-icon="'mdi-account-outline'"
                      :rules="[rules.required]"
                      height="36px"
                      outlined
                      placeholder=" "
                      required
                    />
                  </v-row>

                  <v-row>
                    <!-- Пароль для входа (если требуется) -->
                    <v-col class="mr-4">
                      <v-text-field
                        v-if="isPw"
                        v-model="pwUser"
                        :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                        :label="$t('password') + ' *'"
                        :prepend-icon="'mdi-shield-key-outline'"
                        :rules="[rules.required]"
                        :type="show2 ? 'text' : 'password'"
                        autocomplete="new-password"
                        height="36px"
                        name="newPassword"
                        outlined
                        persistent-hint
                        placeholder=" "
                        @click:append="show2 = !show2"
                      />
                    </v-col>
                    <!-- Кнопка: Присоединиться -->
                    <v-col cols="auto">
                      <XrBtn
                        :icon="isSpinStart ? 'mdi-spin mdi-loading' : 'fas fa-phone'"
                        :text="$t('gc.prev.btn.ready')"
                        :title="$t('gc.prev.btn.ready.title')"
                        class="btnCallPrev mb-2"
                        type="hot"
                        @click="startCallGC">
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

      <v-row v-if="isCallSessionActive" id="participants" class="partPH" style="height: 100%; background: #3c3c3d">



        <!--        <v-sheet-->
        <!--          id="flexParticipants"-->
        <!--          class="d-flex justify-center align-content-space-around flex-wrap bg-surface-variant flexParticipantsCss"-->
        <!--          min-height="200"-->
        <!--        >-->
        <!--        </v-sheet>-->

        <v-sheet
          id="flexParticipants"
          class="d-flex flex-column pl-2 justify-lg-center mb-6 bg-surface-variant flexParticipantsCss"
          min-height="200"
        >
        </v-sheet>


        <!--        <div  id="sflexParticipants" class="flexParticipantsCss d-flex justify-center mb-6 bg-surface-variant">-->

        <!--            <div class="ff2"></div>-->
        <!--            <div class="ff2"></div>-->
        <!--            <div class="ff2"></div>-->
        <!--            <div class="ff2"></div>-->
        <!--            <div class="ff2"></div>-->

        <!--        </div>-->

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

        <div id="devMiniVideos" class="devMinivideoPhs"></div>
        <div v-if="isShowInfo" class="infoDevShow">
          <p class="pInfoTool">{{ usersCall }}</p>

          <v-list>
            <v-list-item-group class="info-list-users">
              <v-list-item
                v-for="(item, i) in itemsUsersInfo"
                :key="i"
                class="info-item-users"
                disabled
              >
                <v-list-item-icon class="divinfo2ISpan">
                  <v-icon :color=item.color class="divinfo2I" v-text="item.icon"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title :color=item.colorText style="color: black" v-text="item.text"></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
          <p v-t="'gc.info.title'" class="pInfoTool"></p>
          <p class="pInfoData">{{ nameRoomBigGr }}</p>
          <p class="pInfoData">{{ infoDataDate }}</p>
          <p class="pInfoData">{{ infoDataLive }}</p>
          <p class="pInfoData">{{ infoDataOU }}</p>
          <p class="pInfoData"></p>
          <p v-if="isDeveloper" class="pInfoData">{{ levelRTT }} <i id="idLevelRTT" class="fas fa-signal"
                                                                    style="font-size: 14px;"></i></p>

        </div>

        <v-row id="idRowInfo" class="pa-6 text-center infoTooldevPh">
          <v-col lg="5">
            <TimerGroupCall ref="timerGroupCall"></TimerGroupCall>
          </v-col>
          <v-col class="pt-1 pr-1" lg="5" style="max-width: 200px; width: -webkit-fill-available;">
            <p :title="nameRoomBig" class="containerGNanePH">&nbsp;{{ nameRoomBig }}</p>
          </v-col>
        </v-row>

        <!--        <div id="idDivLocalVideo" class="iddivlocalvideo" style="display: none">-->
        <!--          <div id="devDevIconNoVideoMain1" class="devDevIconNoVideo">-->
        <!--            <img id="imgDevIconNoVideoMain1" class="imgDevIconNoVideoTextDev" style="display: none">-->
        <!--            <div id="noImgDevIconNoVideoMain1" class="devDevIconNoVideoTextDev" style="display: none">{{ miniName }}-->
        <!--            </div>-->
        <!--          </div>-->
        <!--        </div>-->
      </v-row>

      <v-icon class="noVisIcon">mdi-star-outline</v-icon>

      <RoomXRCallWinPhone ref="roomXRCallWin" :avatar="avatar" :hasCamera="hasCamera"
                          :isCallSessionActive="isCallSessionActive" :isDegradation="isDegradation"
                          :isExternalUser="isExternalUser" :isFirefox="isFirefox" :isMirror="isMirror"
                          :isOrientland="isOrientland" :isUserLic="isUserLic" :numUsers="numUsers"
                          :numUsersExternal="numUsersExternal" :paramValueRoom="roomID" :wsURL="wsURL"
                          @addOperDialog="addOperDialog" @addUserChat="addUserChat" @closeFuncOper="closeFuncOper"
                          @dialogSettCall="onDlgSettCall" @onLevelRTT="onLevelRTT" @setTranslation="setTranslation"
                          @showChat="showChat" @showFuncOper="showFuncOper" @soundRecAT="soundRecAT"
                          @updateSettUser="updateSettUser"     ></RoomXRCallWinPhone>


      <Confirm ref="refConfirm" :isExternalUser="isExternalUser" @agree="confirmAgree" @cancel="confirmCancel"></Confirm>

      <StreamMask ref="vueStreamRef" :background="urlBackgroundFon" :backgroundBlurAmount="backgroundBlurAmount" :isCall="isCallSessionActive"
                  :isExternalUser="isExternalUser" :senderName="senderNameReplace" :videoConstraints="videoConstraints"
                  await></StreamMask>

      <FuncOperator ref="funcOperator" :isExternalUser="isExternalUser" @funcUpdateIconMenu="funcUpdateIconMenu" @sendMessage="sendMessageCall" @setArrow="setArrow"
                    @setPaint="setPaint"></FuncOperator>

    </div>

    <ChatMessageGC ref="chatMessage" :isExternalUser="isExternalUser" :room="roomID" :senderId="senderId" :wsURL="wsURL"
                   @chatIsPlan="chatIsPlan" @clickCloseChat="clickCloseChat" @sendBlobAudioTranslate="sendBlobAudioTranslate"
                   @sendMessage="chatSendMessage" @sendTextTranslate="sendTextTranslate"
                   @soundRecAT="soundRecAT" @stopNoTranslate="stopNoTranslate"></ChatMessageGC>

    <DialogSettingsCall ref="dialogSettCall" :hasCamera="hasCamera" :isExternalUser="isExternalUser" @getValue="valueSett"
                        @onDegradation="onDegradation" @onErrorDevices="onErrorDevices" @onSaveSettingCall="saveSettingCall"
                        @onSaveSettingMain="saveSettingMain" @setBackground="setBackground"></DialogSettingsCall>

    <LinkUserInfo ref="linkUserInfo" :isExternalUser="isExternalUser" :itemsUsersInfo="itemsUsersInfo" :itemsUsersInfoExternal="itemsUsersInfoExternal"
                  :numUsersExternal="numUsersExternal" @addNewOper="addOperDialog" @addUser="addUser"
                  @clickCloseInfoUser="clickCloseInfoUser" @clickCloseUser="clickCloseUser" @clickMic="clickMic"
                  @disUser="disUser"></LinkUserInfo>

    <NoConnectInfoPanel ref="noConnectInfoPanel"></NoConnectInfoPanel>

    <div v-if="isTextInfoError" id="errorDivInfo" class="edi">
      <div class="ediD">
        <p class="ediT">{{ textTitleError }}</p>
        <p class="ediP">{{ textInfoError }}</p>
        <!--        <XrBtn-->
        <!--          class="ediBtn"-->
        <!--          @click="exitLink"-->
        <!--          type="hot"-->
        <!--          :text="$t('gc.exit')"-->
        <!--          :title="$t('gc.exit')"/>-->
      </div>
    </div>

  </div>

</template>

<script>
import av_gif from '@/assets/img/gifLoadGC.gif';
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
import RoomXRCallWinPhone from "@/app/gc/phone/RoomXRCallWinPhone";
import NoConnectInfoPanel from "@/app/gc/func/NoConnectInfoPanel";

var This

let STREAM_VIDEO = null
let STREAM_VIDEO_FON = null;
let STREAM_AUDIO = null;

var URL_GET_GET_GC_URL = '/exgc/getGCURL'

export default {
  name: "GCMainPhone",
  components: {
    NoConnectInfoPanel,
    RoomXRCallWinPhone,
    LinkUserInfo,
    DialogAddOperLink,
    TimerGroupCall,
    FuncOperator,
    Confirm,
    DialogSettingsCall,
    StreamMask,
    ChatMessageGC
  },

  data() {
    return {
      rules: {
        required: value => !!value || '',
      },
      ro: true,
      isOrientland: false,

      textTitleError: '',
      textInfoError: '',
      isTextInfoError: false,
      isCloseWinClick: false,

      ws: null, // основной WebSocket для работы системы
      isActiveLink: false, // доступна ли ссылка, ответ от сервера
      isPreloading: true, // подгрузка страницы
      isAvtoStart: false, // автоматически подключать прользователя
      isAvtoStartNew: false, // автоматически подключать прользователя
      dataLinkInfo: null, // информация по ссылке, получаем от сервера

      isFirefox: false, // определяем браузер, если firefox то false
      numScreenSharinWin: 0, // кол-во активных расшариваний экрана
      isSoundWinFunc: true, // отправляет ли пользователь звук

      nameRoom: 'нет имени', // имя группы
      nameRoomGr: '', // имя группы
      nameRoomBigGr: '', // длиное имя группы

      issenderName: false, // есть ли имя пользователя
      isUserLic: true, // пользователь зарегестрированный

      isCallSessionActive: false, // страница в режиме звонка
      isrep: false, // для дублирования имен

      paramValueLang: this.$route.params.lang, // данные о языке
      roomID: this.$route.params.gcId, // данные о id группы
      paramValueUserId: '',

      senderName: '', // имя клиента
      senderNameReplace: '',//имя клиента без пробелов
      senderId: '', // id клиента, после регистрации
      senderIdold: '',

      usersCallListName: [],
      usersCallList: [],
      isUserCallList: false,

      usersCall: '',
      gcLinkInput: '',
      nameRoomBig: '',

      serverUrl: '',
      wsURL: '',
      isDevUrl: false,
      userId: '',

      infoDataDate: '', // данные о дате
      infoDataLive: '', // данные о времени
      infoDataOU: '', // данные о ОЕ

      isStreamMic: true, // мой вкл.выкл микрофон
      isStreamVideo: true, // мое вкл.выкл видео

      typeMaket: 1, // тип макета 1 - много, 2 - одно большое, 3 - много с моим видео
      isTypeWin: false, // отображение выбора макета

      isShowInfo: false, // отображение подробной информации по ссылке
      numUsers: 0, // кол-во пользователей
      numUsersExternal: 0,
      isDownVideo: true,
      // isDialogSett: false,

      audioInputValue: null, // настройки - выбор микрофона
      audioOutputValue: null, // настройки - выбор динамика
      videoValue: null, // настройки - выбор камеры

      isXrOper: false, //  пока спорная переменная
      isXrOperVid: false, // пока спорная переменная

      isAddFunc: false, // для отображения дополнительных функций
      isScreenSharing: false, // для вкл\выкл расшаривания экрана
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

      hasCamera: false,
      isMirror: true,

      isStartMic: true,
      isStartVideo: true,
      isVideoBackground: true,// превью при запуске ссылки

      isDebug: false,// локальная разработка
      isExternalUser: true,

      isWaitConnection: false,//ожидание подключения от внутреннего пользователя
      isWaitAdd: false,//ожидание подключения
      textTitleWait: '',
      textWait: '',
      isTranslateFunc: false, // доступна ли функция перевода для этой ссылки
      isDevGC: false, // режим разработки ссылки ГЗ
    }
  },

  watch: {
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
    },
  },

  destroyed() {
    window.onbeforeunload = undefined

    this.setLocalStorage("gc.update.error", 'f')

    This.closeCall()
    ////window.close()
  },

  beforeRouteLeave(to, from, next) {
    This.closeCall()
    ////window.close()
  },

  create() {
    this.$root.setLocaleModule(expertRU, expertEN)
    // this.$i18n.locale = this.paramValueLang
  },

  mounted() {
    This = this

    // this.$i18n.locale = this.paramValueLang

    $(window).on('load', () => This.getBrowser().then((is) => {
      if (is) {
        This.setMounted()
        This.getBrowser()
      } else {

      }
    }))

    setTimeout(()=>{
      if(this.isVideoBackground){
        this.isVideoBackground=false
      }
    }, 3000)

    setTimeout(()=>{
      console.log('textTitleWait '+this.textTitleWait+' textWait '+this.textWait)
    },1000)

    this.gcLinkInput = this.$t('gc.link.input')

    $('#idCanvasPrint').hide()

    if (this.paramValueUser == '' || this.paramValueUser == undefined) {
      this.isUserLic = false
    } else {
      this.isUserLic = true
    }

    this.isOrientland = window.screen.orientation.type === "landscape-primary"

    window.addEventListener(
      "orientationchange",
      this.handleOrientationChange
    );

    // alert('mounted')
    // This = this
    // document.title = this.$t('gc.prev.title')
    //
    // this.$root.setLocaleModule(expertRU, expertEN)
    // this.$i18n.locale = this.paramValueLang
    //
    // // $(window).on('load', () => This.getBrowser().then((is) => {
    // //   if(is){
    // //     This.setMounted()
    // //   }else{
    // //     console.log('load false')
    // //     This.waitingLoad()
    // //   }
    // // }))
    //
    // this.gcLinkInput = this.$t('gc.link.input')
    //
    // $('#idCanvasPrint').hide()
    //
    // setTimeout(() => {
    //   This.setMounted()
    // }, 0)
  },

  methods: {

    handleOrientationChange() {
      const orientation = window.screen.orientation.type
      console.log(window.screen.orientation.type)
      if (orientation === "portrait-primary") {
        this.isOrientland = false
        // $('#colMainpos100GClang').hide()
        // $('#colMainpos100GCport').show()
        // console.log("portrait-primary")
      } else if (orientation === "landscape-primary") {
        this.isOrientland = true
        // $('#colMainpos100GClang').show()
        // $('#colMainpos100GCport').hide()
        // console.log("landscape-primary")
      }
    },

    //region Запуск страницы ___________________________________________________________________________________________

    /**region setMounted загрузка страницы*/
    setMounted() {

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

      if(this.getLocalStorage("gc.mirror", 'b')!=null){
        this.isMirror = this.getLocalStorage("gc.mirror", 'b')
      }else{
        this.setLocalStorage("gc.mirror", true)
      }

      if(this.getLocalStorage("gc.senderName")!=null){
        this.senderName = this.getLocalStorage("gc.senderName")
      }

      This.textTitleWait = this.$t('gc.waitConnTitle')
      This.textWait =  this.$t('gc.waitConn')

      // console.log('sig-test-00');
      if (!this.isTextInfoError) {
        setTimeout(() => {
          if (this.isPhone) {
            this.initStream()
          }
          this.initWebSocket()
        }, 100)

        $('#devMiniVideos').hide()
        $('#butDisNone').hide()

        window.onresize = () => {
          This.resizeWindows()
        }
      }
      // console.log('sig-test-03');
    },
    /**endregion*/

    /**region resizeWindows обновление во переворачиванием устройства*/
    resizeWindows() {
      if (this.isPhone) {
        setTimeout(() => {
          $('#divMainPos100').removeClass('posH100')
          $('#colMainPos100').removeClass('posH100')
          $('#divMainPos100').addClass('divMainPos100Phone')
          $('#colMainPos100').addClass('divMainPos100Phone')

          $('.v-application--wrap').addClass('v-app_isPhone')
          $('#vcardId1').removeClass('vCard48')
          $('#vcardId1').removeClass('v1')
          $('#vcardId1').removeClass('v2')
          $('#vcardId2').removeClass('vCard482')
          $('#vcardId2').removeClass('v1')
          $('#vcardId2').removeClass('v3')

          if (window.innerWidth > window.innerHeight) {
            ////console.log('__1_1_')
            $('#vcardId1').addClass('v1')
            $('#vcardId2').addClass('v1')
          } else {
            ////console.log('__1_2_')
            $('#vcardId1').addClass('v2')
            $('#vcardId2').addClass('v3')
          }
        }, 500)
      }
    },
    /**endregion*/

    /**region getBrowser оперделяем вид и версию браузера*/
    async getBrowser() {
      var ua = navigator.userAgent;

      if (ua.search(/Firefox/) > -1) {
        this.noFirefox = false
      }

      var isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
      var isIphone = /iPhone|iPod/i.test(navigator.userAgent);

      if (isMobile || isIphone) {
        this.isPhone = true

        console.log('isPhone = true')

      } else {
        this.isPhone = false
      }

      if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(ua)) {
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

        This.typeBrowser = bName + ' v.' + version;
      }
      return true
    }
    ,
    /**endregion*/

    /**region startSettingsPreview преднастройки для заглавного интерфейса*/
    startSettingsPreview() {
      This.$refs.dialogSettCall.updateDev(This.isMirror, 'startSettingsPreview')
    }
    ,
    /**endregion*/

    /**region rejCallOper Ошибка загрузки страницы*/
    rejCallOper(parsedMessage) {
      ////console.log(parsedMessage)

      if (parsedMessage.timeReject) {
        This.$refs.refConfirm.open(this.$t('reject.call.info'),
          this.$t('operator') + ' ' + parsedMessage.nameOper + ' ' + this.$t('reject.call.operator'),
          {color: 'warning'}, "", 0)
      } else {
        This.$refs.refConfirm.open(this.$t('reject.call.info'),
          this.$t('operator') + ' ' + parsedMessage.nameOper + ' ' + this.$t('reject.call.operator_norime'),
          {color: 'warning'}, "", 0)
      }
    }
    ,
    /**endregion*/
    //endregion

    //region WebSocket _________________________________________________________________________________________________

    init(){

    },

    /**region initWebSocket Запуск webSocket*/
    initWebSocket() {
      console.log('initWebSocket getGCURL '+URL_GET_GET_GC_URL)
      this.$root.restGet(URL_GET_GET_GC_URL+'/'+this.roomID+'/'+this.paramValueLang).then((res) => {
        try {
          console.log('getGCURL res')
          console.log(res)
          console.log(res.wsURL)
          if (res.ErrorGC == 0){
            This.wsURL = res.wsURL
            This.roomID = res.gcID
            This.isTranslateFunc = res.isTranslate
            This.isDevGC = res.isDevGC

            var hostname = window.location.host;
            console.log("host "+hostname)
            if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
              This.wsURL = 'wss://'+window.location.host
              console.log("location.hostname "+hostname)
            }

            this.ws = new WebSocket(This.wsURL+'/ms/groupcall');

            this.ws.onopen = function () {
              console.log('ws.onopen ', This.roomID)

              if (This.roomID) {
                var message = {
                  id: 'isInfoLinkStart',
                  user: This.paramValueUser,
                  room: This.roomID,
                  lang: This.paramValueLang
                }

                try {
                  This.sendMessage(message);
                } catch (e) {
                  console.log('ERROR ws start')
                }

              } else {
                This.infoCloseWS("нет данных о группе")
              }

              if(This.isAvtoStartNew){
                this.isVideoBackground=false
                console.log("isAvtoStartNew ws cool")
                This.startCallGC()
              }
            };

            this.ws.onerror = (evt) => {
              console.log(evt)
              this.isTextInfoError = true
              this.textTitleError = this.$t('gc.textErrorLinkTitle2')
              this.textInfoError = this.$t('gc.errorStartLink.5') + " (ws onerror)"
              this.closeStream()
              This.$root.winClose()
            }

            this.ws.onmessage = function (message) {
              var parsedMessage = JSON.parse(message.data);

              // if (parsedMessage.id != 'isUsLink' && parsedMessage.id != 'live')
              //console.log('Received message: ' + message.data);

              switch (parsedMessage.id) {
                case 'isInfoLinkStart':
                  This.isInfoLinkStart(parsedMessage)
                  break;
                case 'joinRoomExternal':
                  This.joinRoomExternal(parsedMessage)
                  break;
                case 'addUserSend':
                  This.addUser(parsedMessage)
                  break;
                case 'disUserSend':
                  This.disUserSend(parsedMessage)
                  break;
                case 'isUsers' :
                  This.statusUserCall(parsedMessage)
                  break;
                case 'isUsLink' :
                  This.statusUsers(parsedMessage)
                  break;
                case 'yesCallLink':
                  This.yesCallLink(parsedMessage)
                  break;
                case 'timeCall':
                  This.$refs.timerGroupCall.startTimerMain(Number(parsedMessage.startTime), Number(parsedMessage.deltaTime))
                  break;
                case 'stopCall':
                  ////console.log('ERROR SOP CALL '+parsedMessage)
                  if (parsedMessage.userClose) {
                    This.$refs.refConfirm.open(This.$t('reject.call.info.close.title'),
                      parsedMessage.userClose + ' ' + This.$t('reject.call.info.close'), 3,
                      {color: 'warning'}, "", 3)
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
                case
                'isLinkAvailable'
                :

                  This.$refs.roomXRCallWin.isLinkLive(parsedMessage)


                  break;
                case 'existingParticipantsExternal':
                  setTimeout(() => {
                    console.log('existingParticipantsExternal')
                    console.log(parsedMessage)
                  }, 500)

                  break;
                case
                'existingParticipants'
                :
                  setTimeout(() => {

                    This.senderId = parsedMessage.senderId

                    console.log('This.$refs.roomXRCallWin.onExistingParticipants(parsedMessage)', parsedMessage);
                    This.$refs.roomXRCallWin.onExistingParticipants(parsedMessage, This.isStartMic, This.isStartVideo);

                    console.log('GC existing ' + parsedMessage.senderId)

                    This.addMyUserChat(parsedMessage.senderId, This.senderName, '/exgc/avatar/' + This.roomID + '/' + parsedMessage.senderId);
                    // This.addUserChat(parsedMessage.senderId, This.senderName, '/avatar/' + This.roomID + '/null' );
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

                  console.log('onParticipantLeft',parsedMessage)
                  This.$refs.roomXRCallWin.onParticipantLeft(parsedMessage);
                  // }
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
                  console.log(parsedMessage)

                  break;
                case
                'clearCanvas'
                :
                  console.log(parsedMessage)
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
                  if(message.status){

                  }

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
                  // This.$refs.chatMessage.restOneATmessage(parsedMessage)// 13 Релиз
                  break;
                case
                'asrtTranlsateResult'
                :
                  // This.$refs.chatMessage.restATmessage(parsedMessage)// 13 Релиз
                  break;
                case 'asrtTranlsateResultError':
                  This.asrtError(parsedMessage)
                  break
                case
                'asrtTranlsateAudioVosk'
                :
                  // This.$refs.chatMessage.restTranlsateAudioVosk(parsedMessage)// 13 Релиз
                  break;
                case
                'soundRecAT'
                :
                  This.$refs.roomXRCallWin.soundRecAT(parsedMessage)
                  break;
                case
                'asrtTranlsateResultAudio'
                :
                  This.$refs.roomXRCallWin.asrtTranlsateResultAudio(parsedMessage)
                  break;
                case 'setPlanEl':
                  // This.$refs.chatMessage.setPlanEl(parsedMessage)// 13 Релиз
                  break;
                case 'sendMyPlan':
                  This.planUpdateMessage(parsedMessage.plan)
                  break;
                case 'exceededQuantity':
                  This.setInfoNoConnect(parsedMessage, 1)
                  break;
                case 'updateGcFileId':

                  break;
                default:
                  console.error('Unrecognized message', parsedMessage);
              }
            }

            this.ws.addEventListener('close', (evt) => {
              //////console.log('ЗАКРЫТИЕ WebSocket с кодом: ' + evt.code);
              This.isInfoServer = false
              switch (evt.code) {
                case 1000: //CLOSE_NORMAL
                  ////console.log("WS-close Правильное закрытие сокета.");
                  This.infoCloseWS("WS-close Правильное закрытие сокета.", 1000)
                  break;

                case 1001: //LWS_CLOSE_STATUS_GOINGAWAY
                  ////console.log('WS-close сервер отключается или браузер перешел от страницы..');
                  This.infoCloseWS('WS-close сервер отключается или браузер перешел от страницы..', 1001)
                  break;

                case 1002: //LWS_CLOSE_STATUS_PROTOCOL_ERR
                  this.setLocalStorage("gc.update.error", 't')
                  location.reload();
                  console.log('WS-close 1002 завершает соединение из-за ошибки протокола..');
                  break;

                case 1003: //LWS_CLOSE_STATUS_UNACCEPTABLE_OPCODE
                  ////console.log('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +'если она получает двоичное сообщение)..');
                  This.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +
                    'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +
                    'если она получает двоичное сообщение)..', 1003)
                  break;

                case 1006: //CLOSE_ABNORMAL
                  ////console.log('WS-close Сервер временно недоступен. Попробуйте снова позднее.');
                  This.infoCloseWS('WS-close Сервер временно недоступен. Попробуйте снова позднее.', 1006);
                  break;

                case 1009: //LWS_CLOSE_STATUS_MESSAGE_TOO_LARGE
                  ////console.log('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.')
                  This.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.', 1009)
                  break

                case 1011: //LWS_CLOSE_STATUS_UNEXPECTED_CONDITION
                  ////console.log('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.')
                  This.infoCloseWS('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.', 1011)
                  break;

                default:
                  This.infoCloseWS('Нет данных подключения', 300)
                  break;
              }

              try {
                This.$refs.timerGroupCall.stopTimer()
              } catch (e) {
                console.log(e)
              }

              setTimeout(() => {

                This.closeCall()
              }, 1000)
            }, false);
          }else{
            console.log(res.TextError)
            This.$refs.noConnectInfoPanel.showInfoPhone(res.TextError, res.ErrorGC, res.nameLink)
          }


        } catch (e) {
          alert('Ошибка получения данных с сервера '+e)
          // try{
          //   setTimeout(() => {
          //     if (res.ErrorGC == 1) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          //       console.error(This.$t('gc.errorStartLink.1') )
          //       this.textInfoError = This.$t('gc.errorStartLink.1')
          //       this.closeStream()
          //     } else if (res.ErrorGC == 2) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.errorStartLink.2')
          //       // console.log('d1 t', res.TextError.substring(1, '17')+':00Z')
          //       const d1 = new Date(res.TextError.substring(1, '17')+':00Z')
          //       // console.log('d1.toString()',d1.toString())
          //       // console.log('d1.toString()',moment(d1).format('DD.MM.YYYY HH:mm'))
          //       this.textInfoError = This.$t('gc.errorStartLink.2') +' '+ moment(d1).format('DD.MM.YYYY HH:mm')
          //       this.closeStream()
          //     } else if (res.ErrorGC == 3) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          //       console.error(This.$t('gc.errorStartLink.3'))
          //       this.textInfoError = This.$t('gc.errorStartLink.3')
          //       this.closeStream()
          //     } else if (res.ErrorGC == 4) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          //       console.error(This.$t('gc.errorStartLink.4'))
          //       this.textInfoError = This.$t('gc.errorStartLink.4')
          //       this.closeStream()
          //     } else if (res.ErrorGC == 5) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          //       console.error(This.$t('gc.errorStartLink.5'))
          //       this.textInfoError = This.$t('gc.errorStartLink.5')
          //       this.closeStream()
          //     } else if (res.ErrorGC == 6) {
          //       this.isTextInfoError = true
          //       this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          //       console.error(This.$t('gc.errorStartLink.6'))
          //       this.textInfoError = This.$t('gc.errorStartLink.6')
          //       this.closeStream()
          //     } else {
          //       this.isTextInfoError = true
          //       alert('gc.textErrorLinkTitle2')
          //     }
          //     ////window.close();//1
          //   }, 2000)
          // }catch (es){
          //
          //   //window.close();
          // }
        }
      });
    },
    /**endregion*/

    isInfoLinkStart(res) {

      console.log('res ' + res)
      console.log(res)

      try {
        if (res.ErrorGC == 0) {

          this.startSettingsPreview()
          this.addAudioTrack()

          this.roomID = res.gcId
          this.room = res.gcId

          this.isEcpect = res.isEcpect

          var valueUser = '123'

          if (This.paramValueUser == 'null' || This.paramValueUser == undefined) {
            this.issenderName = true
          } else {
            this.issenderName = false
            valueUser = This.paramValueUser.replace(/ /g, '')
          }

          if (!this.issenderName && valueUser.substring(0, 3) == 'av') {
            This.paramValueUser = This.paramValueUser.substring(2, This.paramValueUser.length)

            This.isAvtoStart = false

            if (This.isAvtoStart) {
              setTimeout(() => {
                if (This.isPhone) {
                  This.isXrOper = true
                  $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldevPh').hide()
                }
              }, 500);
            } else {

            }

            if (This.roomID) {
              var message = {
                id: 'isLinkAvailable',
                user: This.paramValueUser,
                room: This.roomID,
                lang: This.paramValueLang
              }
              This.sendMessage(message);
            } else {
              This.infoCloseWS("нет данных о группе")
            }

          } else {


            setTimeout(function run() {

              if (This.roomID && This.ws) {
                var message = {
                  id: 'isUsLink',
                  room: ''+This.roomID,
                }

                This.sendMessage(message);
              }
              setTimeout(run, 1000);
            }, 1000);

            if (This.roomID) {
              var message = {
                id: 'isLinkAvailable',
                user: This.paramValueUser,
                room: This.roomID,
                lang: This.paramValueLang
              }

              try {
                This.sendMessage(message);
              } catch (e) {
                //console.log('ws start')
              }

            } else {
              This.infoCloseWS("нет данных о группе")
            }
          }
        }
        else if (res.ErrorGC == 1) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          console.error(This.$t('gc.errorStartLink.1') + res.TextError)
          this.textInfoError = This.$t('gc.errorStartLink.1')
          this.closeStream()
        } else if (res.ErrorGC == 2) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.errorStartLink.2')
          console.error(This.$t('gc.errorStartLink.2') + res.TextError)

          var strDateError = res.TextError
          // var dateError = new Date(res.TextError)
          var d = strDateError.substring(0, 2)
          var m = strDateError.substring(3, 5)
          var y = strDateError.substring(6, 11)
          var h = strDateError.substring(11, 13)
          var min = strDateError.substring(14, 16)
          var s = strDateError.substring(17, 19)

          var dateEr = new Date(y, m, d, h, min, s, '00')
          dateEr.setMonth(dateEr.getMonth()-1)

          var x = new Date();
          var currentTimeZoneOffsetInHours = x.getTimezoneOffset() / 60;

          dateEr.setHours(dateEr.getHours()-currentTimeZoneOffsetInHours)

          this.textInfoError = This.$t('gc.errorStartLink.2') + moment(dateEr).format('DD.MM.YYYY HH:mm:ss')
          this.closeStream()
        } else if (res.ErrorGC == 3) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          console.error(This.$t('gc.errorStartLink.3') + res.TextError)
          this.textInfoError = This.$t('gc.errorStartLink.3')
          this.closeStream()
        } else if (res.ErrorGC == 4) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          console.error(This.$t('gc.errorStartLink.4') + res.TextError)
          this.textInfoError = This.$t('gc.errorStartLink.4')
          this.closeStream()
        } else if (res.ErrorGC == 5) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          console.error(This.$t('gc.errorStartLink.5') + res.TextError)
          this.textInfoError = This.$t('gc.errorStartLink.5')
          this.closeStream()
        } else if (res.ErrorGC == 6) {
          this.isTextInfoError = true
          this.textTitleError = This.$t('gc.textErrorLinkTitle2')
          console.error(This.$t('gc.errorStartLink.6') + res.TextError)
          this.textInfoError = This.$t('gc.errorStartLink.6')
          this.closeStream()
        } else {
          this.isTextInfoError = true
          alert('gc.textErrorLinkTitle2')

        }
        //window.close();
      } catch (e) {
        alert('Ошибка получения данных с сервера')
        //window.close();
      }
    },

    closeStream() {
      setTimeout(() => {
        this.stopBothVideoAndAudio(STREAM_VIDEO)
        this.stopBothVideoAndAudio(STREAM_VIDEO_FON)
        this.stopBothVideoAndAudio(STREAM_AUDIO)
        // This.$refs.vueStreamRef.stop()
      }, 3000)
    },

    stopBothVideoAndAudio(stream) {
      try {
        stream.getTracks().forEach(function (track) {
          if (track.readyState == 'live') {
            track.stop();
          }
        })
      } catch (e) {
        console.error(e)
      }
    },

    exitLink() {
      This.isCloseWinClick = true
    },

    startCloseClick(f) {
      console.log('startCloseClick ' + f)
      setTimeout(function run() {
        if (!This.isCloseWinClick) {
          console.log('1')
          setTimeout(run, 1000);
        } else {
          console.log('2')
          //window.close();
        }

      }, 1000);
    },

    joinRoomExternal(message) {
      console.log(message)
      this.isWaitConnection = true
      this.isWaitAdd = true
    },

    /**region infoCloseWS Информация о закрытии ws*/
    infoCloseWS(textClose, code) {
      console.log(textClose)
      // This.$refs.roomXRCallWin.closeCall()
      try{
        This.ws.close()
        This.isPreloading = false
        This.isActiveLink = false
      }catch (e) {

      }
      This.$refs.noConnectInfoPanel.showInfoPhoneName(this.$t('gc.title.group.call.error'), this.$t('gc.title.group.call.errortext')+' ('+textClose+')', this.$t('gc.title.group.call')+': '+this.nameRoom)

    }
    ,
    /**endregion*/

    /**region sendMessage Отправка сообщения по ws*/
    sendMessage(message) {
      // console.log('message')
      // console.log(message)
      if (this.ws) {
        var jsonMessage = JSON.stringify(message)
        this.ws.send(jsonMessage)
        return true
      } else {
        this.infoCloseWS('close this.ws null')
        return false
      }
    }
    ,
    /**endregion*/

    //endregion

    //region Данные о ссылке и участниках ______________________________________________________________________________
    /**region statusUsers обновление данных о пользователях*/
    statusUsers(mess) {
      // console.log('statusUsers(mess)', mess)
      try {
        if (This.$refs.roomXRCallWin.getSenderID()) {
          This.senderId = This.$refs.roomXRCallWin.getSenderID()
        } else{
          if (mess.usersActive.length != 0) {
            this.usersCallList = mess.usersActive
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

      if (mess.usersActive != "") {

        //XXX
        // if (This.isCallSessionActive) {
        if (true) {

          var arUsers= [];
          var arUsersActiveSenderID= [];

          this.usersCallList.forEach(item => {
            arUsers.push(item.senderName)
            arUsersActiveSenderID.push(item.senderId)
          })

          this.usersCallListName = arUsers

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

              var avatar = avatars[arUsersActiveSenderID[i]]

              var isMy = arUsersActiveSenderID[i] == This.senderId

              if (oper == 'Оператор_' || oper == 'Operator_') {
                this.itemsUsersInfo[i] = {
                  senderId: arUsersActiveSenderID[i],
                  senderName: arUsers[i],
                  text: arUsers[i],
                  icon: 'fa-user',
                  avatar: '',
                  color: '#43a047',
                  colorText: 'black',
                  isscrS: false,
                  isOper: true,
                  isMy: isMy,
                  isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i])
                }
              } else if (srsc === 'scrsXR') {
                this.itemsUsersInfo[i] = {
                  senderId: arUsersActiveSenderID[i],
                  senderName: arUsers[i],
                  text: arUsers[i].substring(8, arUsers[i].length) + this.$t('gc.scrS'),
                  icon: 'fa-chalkboard-teacher',
                  avatar: '',
                  color: '#43a047',
                  colorText: 'black',
                  isscrS: true,
                  isOper: false,
                  isMy: isMy,
                  isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i])
                }
              } else {
                this.itemsUsersInfo[i] = {
                  senderId: arUsersActiveSenderID[i],
                  senderName: arUsers[i],
                  text: arUsers[i],
                  icon: 'fa-user-tie',
                  avatar: `data:image/jpg;base64,${avatar}`,
                  color: '#43a047',
                  colorText: 'black',
                  isscrS: false,
                  isOper: false,
                  isMy: isMy,
                  isMic: This.$refs.roomXRCallWin.getIsMicUser(arUsersActiveSenderID[i])
                }
              }

            } catch (e) {
              console.log(e)
            }
          }
        }
      }
    }
    ,
    /**endregion*/

    /**region statusUserCall получение данных о текущем пользователе*/
    statusUserCall(mess) {

      console.log('statusUserCall(mess) ', mess)

      if (mess.pw == null || mess.pw == undefined) {
        this.isPw = false
        this.pw = mess.pw
      } else {
        this.isPw = true
        this.pw = mess.pw
      }

      this.isDeveloper = mess.dev
      this.isdevUser = mess.devUser

      console.log('isDeveloper ' + this.isDeveloper)
      console.log('isdevUser ' + this.isdevUser)

      this.avatar = mess.avatar
      console.log('avatar')
      console.log(this.avatar)

      // console.log('this.toUTF8Array(this.avatar)', this.toUTF8Array(this.avatar))

      //изображения работают
      //$(`#imgTEST`).attr('src', `data:image/jpg;base64,${this.avatar}`)


      if (This.isAvtoStart && mess.isLive) {
        This.$refs.dialogSettCall.updateDev(This.isMirror, 'statusUserCall')

        this.nameRoomBig = mess.nameRoom
        if (mess.nameRoom.length > 17) {
          this.nameRoom = mess.nameRoom.substring(0, 17) + '...'
        } else {
          this.nameRoom = mess.nameRoom.substring(0, 17)
        }

        this.dataLinkInfo = mess
        This.senderName = This.paramValueUser
        setTimeout(() => {
          This.startCallGC()
        }, 500);

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
          this.usersCall = `${This.$t('gc.user-online')}: ${mess.usersActive}`
        } else {
          this.usersCall = This.$t('gc.no-members')
        }
        this.usersCall = this.usersCall.replace(/(^\s*,)|(,\s*$)/g, '');
        console.log('this.usersCall', this.usersCall, 'mess.usersActive', mess.usersActive);

        this.nameRoomGr = This.$t('gc.group') + ': ' + this.nameRoom
        this.nameRoomBigGr = This.$t('gc.group') + ': ' + this.nameRoomBig

        this.infoDataDate = `${This.$t('gc.available-from')} ${this.dataLinkInfo.dateStart}`
        this.infoDataLive = `${This.$t('gc.during')}: ${this.dataLinkInfo.typeLive}`
        this.infoDataOU = `${This.$t('ou')}: ${this.dataLinkInfo.ouName}`

      }
      else {
        // This.isTextInfoError = true
        // this.textTitleError = This.$t('gc.textErrorLinkTitle2')
        // console.error(This.$t('gc.errorStartLink.3') + res)
        // This.textInfoError = This.$t('gc.errorStartLink.3')
        // This.closeStream()
        // alert(mess.isLive)
        // // This.infoCloseWS("mess.pw == null || mess.pw == undefined")
        // console.log(mess)
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
          console.log('STREAM_VIDEO true live')
          $(`#videoPre`)[0].srcObject = STREAM_VIDEO;
          This.isDownVideo = false
        } else {
          //$(`#videoPre`)[0].srcObject = this.richard_;
          // console.log('sig-test1b', STREAM_VIDEO, $(`#videoPre`)[0].srcObject);
          This.isDownVideo = true
        }
        // if (This.isDegradation) {
        //   console.log('This.isDegradation   $(`#videoPre`)[0].srcObject')
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
    /**region*/// начало соединения с ГЗ
    startCallGC() {
      console.log('startCallGC - начало', 'this.isDeviceYes = ', this.isDeviceYes, 'this.isOneUser = ', this.isOneUser);

      if (this.isDeviceYes) {
        This.isSpinStart = true
        setTimeout(() => {
          This.isSpinStart = false
        }, 4000)


        if (this.isOneUser) {
          if (This.senderName == '') {
            this.$root.showErr(This.$t('gc.error.noname'))
            This.isSpinStart = false
          } else if (This.isAllowedChar(This.senderName)) {
            // this.$root.showErr(This.$t('gc.error.noname.char'))
            this.$root.showErr('Ошибка имени, используются неправильные знаки. Имя должно состоять из букв и цифр')
            This.isSpinStart = false
          } else if (This.isPw && This.pwUser == '') {
            this.$root.showErr(This.$t('gc.error.nopw'))
            This.isSpinStart = false
          } else if (This.isPw && This.pwUser != This.pw) {
            this.$root.showErr(This.$t('gc.error.errorpw'))
            This.isSpinStart = false
          } else {
            this.isOneUser = false
            this.setLocalStorage('gc.senderName', this.senderName)
            this.senderNameReplace = this.senderName.replace(/ /g, '')
            var message = {}

            this.miniName = this.setNameUC(this.senderName.replace(/ /g, ''))

            if (this.isEcpect) {
              console.log('avatar2_ === ' + this.avatar)
              if (this.avatar.length > 0) {
                message = {
                  id: 'joinRoom',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  hasCamera: this.hasCamera,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar
                }
              }
              else {
                this.avatar = This.$refs.chatMessage.generateAvatar(this.miniName)

                message = {
                  id: 'joinRoom',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  hasCamera: this.hasCamera,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar
                }
              }
            }
            else {
              console.log('avatar3_ === ' + this.avatar)
              if (this.avatar.length > 0) {
                message = {
                  id: 'joinRoomExternal',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  hasCamera: this.hasCamera
                }
              } else {
                this.avatar = This.$refs.chatMessage.generateAvatar(this.miniName)

                message = {
                  id: 'joinRoomExternal',
                  senderName: this.senderName,
                  roomId: this.roomID,
                  bodyPix: this.isDegradation,
                  device: this.typeBrowser,
                  avatar: this.avatar,
                  hasCamera: this.hasCamera
                }
              }

            }
            ////console.log(message)
            This.isrep = true
            this.sendMessage(message);

            setTimeout(() => {
              if (This.isrep) {
                this.isCallSessionActive = true
                // на полный экран
                if (This.isPhone) {
                  document.documentElement.requestFullscreen();

                  STREAM_VIDEO_FON = null
                  console.log('This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO, this.hasCamera)',
                    this.isDeveloper, this.ws, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO, this.hasCamera);
                  This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.senderName, this.hasCamera, this.typeBrowser, this.isTranslateFunc, this.isDevGC, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO )


                  This.isSpinStart = false
                  this.infoOut()
                }
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

    // generateAvatar(text, foregroundColor, backgroundColor) {
    //   const canvas = document.createElement("canvas");
    //   const context = canvas.getContext("2d");
    //
    //   foregroundColor = '#fff'
    //   backgroundColor = '#a26400'
    //
    //   canvas.width = 200;
    //   canvas.height = 200;
    //
    //   // Draw background
    //   context.fillStyle = backgroundColor;
    //   context.fillRect(0, 0, canvas.width, canvas.height);
    //
    //   // context.font = "bold 80px Rooftop-Regular";
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
      This.$refs.dialogSettCall.updateDev(This.isMirror, 'updateSettUser')
    },
    /**endregion*/

    /**region*/
    isAllowedChar(nameStr) {
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

      this.$router.go(0)

      this.isCallSessionActive = false
      document.exitFullscreen();

      var keys = Object.keys(stream.calls);

      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];

        if (participant && participant.peer) {
          $('#video_' + participant.name.replace(/ /g, '')).hide()
          $('#devVideo_' + participant.name.replace(/ /g, '')).hide()
          delete stream.calls['' + keys[i]];
        }
      }

      if (!this.isAvtoStart) {
        this.$router.go(0)
      }

      this.ws.close()
      //window.close();
    }
    ,

    closeCallWh() {
      this.textWait = ''
      this.ws.close()
      //window.close();
    },
    /**endregion*/

    /**region*/
    addUser() {
      this.isWaitConnection = false
      This.userRepetition(true)
    },

    disUserSend() {
      this.isWaitAdd = false
      this.textTitleWait = This.$t('gc.waitConnTitleNo'),
        this.textWait = This.$t('gc.waitConnNo'),
        this.ws.close()
      ////window.close();
    },

    userRepetition() {
      this.isrep = false
      // this.$root.showErr(This.$t('gc.error.secondname'))

      setTimeout(() => {
        if (This.senderNameOldRep == '') {
          This.senderNameOldRep = This.senderName
        }
        This.senderName = This.senderNameOldRep + ' (' + This.numRepetition + ')'
        This.numRepetition++

        this.isOneUser = false
        this.senderNameReplace = this.senderName.replace(/ /g, '')
        var message = {}

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
            hasCamera: this.hasCamera
          }
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
            hasCamera: this.hasCamera
          }
        }

        This.isrep = true
        this.sendMessage(message);

        setTimeout(() => {
          if (This.isrep) {
            this.isCallSessionActive = true
            // на полный экран

            // document.documentElement.requestFullscreen();

            STREAM_VIDEO_FON = null
            This.$refs.roomXRCallWin.init(this.isDeveloper, this.ws, this.senderName, this.hasCamera, this.typeBrowser, this.isTranslateFunc, this.videoValue, this.audioInputValue, this.audioOutputValue, null, STREAM_AUDIO, this.serverUrl, this.isDevUrl, this.isDevGC)
            // }

            This.isSpinStart = false
            this.infoOut()

          }
          this.isOneUser = true
        }, 300)


      }, 500)
    }
    ,
    /**endregion*/
    //endregion __________________________________________________________________________________

    //region Настройки видио и аудио ___________________________________________________________________________________

    /**region saveSettingsCall Сохранить и приминить изменения*/
    saveSettingMain(_audioInputValue, _audioOutputValue, _videoValue, _isMirror, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
      console.log('saveSettingsCall Сохранить и приминить изменения*')
      console.log('_STREAM_VIDEO ',_STREAM_VIDEO)
      this.audioInputValue = _audioInputValue
      this.audioOutputValue = _audioOutputValue
      this.videoValue = _videoValue
      // this.isDegradation = _isDegradationSett
      this.isMirror = _isMirror

      this.setLocalStorage("gc.mirror", _isMirror)

      try {
        if (STREAM_VIDEO) {
          console.log('main 2 STREAM_VIDEO ', STREAM_VIDEO)

          STREAM_VIDEO.getTracks().forEach(function (track) {
            if (track.kind == 'video') {
              track.stop();
              console.log('track.stop()')
            }
          });
          $(`#videoPre`)[0].srcObject.getTracks().forEach(function (track) {
            if (track.kind == 'video') {
              track.stop();
              console.log('vjs track.stop()')
            }
          });


        } else {
          console.log('main 2  else STREAM_VIDEO ', STREAM_VIDEO)
        }
      } catch (e) {
        console.error('e main 2  ', e)
      }
      // var constraints = {
      //   audio: false,
      //   video: {optional: [{sourceId: this.videoValue}]
      // }

      // var constraints = {
      //   video: {
      //     optional: [
      //       {sourceId: this.videoValue},
      //     ]
      //   },
      //   audio: false
      // }

      // if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      //   navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
      //     STREAM_VIDEO = stream
      //   }).then(function () {
      //
      //     console.log('$(`#videoPre`)[0].videoTracks()')
      //
      //     console.log($(`#videoPre`)[0].srcObject)
      //
      //     STREAM_AUDIO = _STREAM_AUDIO
      //     This.previewVideo()
      //   });
      // }
      // if (this.isDegradation) {
      //   if (this.noFirefox && !this.isPhone) {
      //     this.initStream()
      //   }
      //
      //   // $(`#videoPre`).css('width', '1px')
      //   // $(`#videoPre`).css('height', '1px')
      // } else {
      //   // $(`#videoPre`).css('width', '100%')
      //   // $(`#videoPre`).css('height', '100%')
      // }
    }
    ,

    onErrorDevices(text, code) {
      console.log('onErrorDevices text '+text + ' code '+ code)
      this.isDeviceYes = true
      if (code == 0) {
        this.isDeviceYes = true
        This.hasCamera = false
        console.log('ДОСТУП К КАМЕРЕ ЕСТЬ')

      } else if (text == 'NotAllowedError: Permission denied' || 'NotReadableError: Could not start video source' || code == 6) {
        // this.isDeviceYes = false
        //this.$root.showErr(this.$root.showErr(This.$t('ex.error.info.text1')))
        console.log('error (!)', This.$t('ex.error.info.text1'))
        //this.isStreamVideo = false // sig
        This.isDownVideo = false
        This.hasCamera = false

      } else if (text == 'ND') {
        this.isDeviceYes = false
        this.$root.showErr(this.$root.showErr(This.$t('ex.error.info.text2')))
      } else {
        this.isDeviceYes = false
        this.$root.showErr(this.$root.showErr(This.$t('ex.error.info.text3')))
        console.error('Нет доступа к камере и микрофону, возможно их занимает другое приложение. код: ' + code + ' текст ошибки: ' + text)
      }
    }
    ,
    /**endregion*/

    /**region saveSettingCall */
    saveSettingCall(_audioInputValue, _audioOutputValue, _videoValue, _isMirror, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
      console.log('saveSettingCall сохранить изменения если идет звонок')
      console.log('_audioInputValue '+_audioInputValue.text)
      console.log('_audioOutputValue '+_audioOutputValue.text)
      console.log('_videoValue ', _videoValue)
      console.log('_STREAM_AUDIO ',_STREAM_AUDIO)

      this.audioInputValue = _audioInputValue
      this.audioOutputValue = _audioOutputValue
      this.videoValue = _videoValue
      // this.isDegradation = _isDegradationSett
      this.isMirror = _isMirror

      try {
        if (STREAM_VIDEO) {
          STREAM_VIDEO.getTracks().forEach(function (track) {
            if (track.kind == 'video') {
              track.stop();
            }
          });
        }
        if( $(`#videoPre`)[0]){
          $(`#videoPre`)[0].srcObject.getTracks().forEach(function (track) {
            if (track.kind == 'video') {
              track.stop();
            }
          });
        }
      } catch (e) {
        console.error('e main 5  ', e)
      }

      This.$refs.roomXRCallWin.saveSettingsCall(_audioInputValue, _audioOutputValue, _videoValue, null, _STREAM_AUDIO)

      // var constraints = {
      //   audio: false,
      //   video: {optional: [{sourceId: this.videoValue}]},
      // }
      // if (this.isDegradation) {
      //   if (this.noFirefox && !this.isPhone) {
      //     this.replaceStream(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO)
      //   } else {
      //     This.$refs.roomXRCallWin.saveSettingsCall(_audioInputValue, _audioOutputValue, _videoValue, _isMirror, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO)
      //   }
      // } else {
      // }
    }
    ,
    /**endregion*/

    /**region valueSett Получение изначальных настрокек видео и аудио*/
    valueSett(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
      console.log('valueSett ',_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO)

      this.audioInputValue = _audioInputValue
      this.audioOutputValue = _audioOutputValue
      this.videoValue = _videoValue
      // this.isDegradation = _isDegradationSett

      try {
        if (STREAM_VIDEO) {

          console.log('main 1 STREAM_VIDEO ', STREAM_VIDEO)

          STREAM_VIDEO.getTracks().forEach(function (track) {

            console.log('this.videoValue ' + This.videoValue.text)
            console.log('track.label' + track.label)
            console.log(This.videoValue.text == track.label)

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

      // var constraints = {
      //   video: {
      //     optional: [
      //       {sourceId: this.videoValue}
      //     ]
      //   },
      //   audio: false
      // }
      // if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      //   navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
      //     STREAM_VIDEO = stream
      //   }).then(function () {
      //     STREAM_AUDIO = _STREAM_AUDIO
      //     This.previewVideo()
      //   });
      // }
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
    }
    ,
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
    /**endregion*/

    /**region setPaint событие вкл. выкл. рисования*/
    setPaint(isPaint, sender) {
      This.$refs.roomXRCallWin.setPaint(isPaint, sender)
    },
    /**endregion*/


    /**region funcUpdateIconMenu событие для иконок меню*/
    funcUpdateIconMenu(isMain, sender) {
      This.$refs.roomXRCallWin.funcUpdateIconMenu(isMain, sender)
    },
    /**endregion*/

    /**region showFuncOper событие для вкл. функций рисования*/
    showFuncOper(sender) {
      this.$refs.funcOperator.show(sender, this.roomID, this.senderName, This.$refs.roomXRCallWin.getOrient(sender), This.$refs.roomXRCallWin.getDelta(sender))
    },

    setDataPhone() {
      this.$refs.funcOperator.setDataPhone(this.oDraw, this.deltaCanvas)
    },

    /**endregion*/
    //endregion

    //region Функции stream и размытия _________________________________________________________________________________
    /**region*/
    addAudioTrack() {
      // let qqq = stream // выдаст ошибку при логине по ссылке
      console.log('addAudioTrack: STREAM_AUDIO', STREAM_AUDIO)
      // console.log('addAudioTrack: stream, STREAM_AUDIO', stream, STREAM_AUDIO)
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
            console.log("The following error occured: " + err.name)
          });
      } else {
        console.log("getUserMedia not supported");
      }
    }
    ,
    /**endregion*/

    onDegradation(_videoValue) {

    },

    // /**region onDegradation применение размытия в диалоге*/
    // async onDegradation(_videoValue) {
    //   await This.$refs.vueStreamRef.init().then((stream) => {
    //   })
    //   setTimeout(() => {
    //     This.$refs.vueStreamRef.init().then((stream) => {
    //       return stream
    //     })
    //   }, 2000)
    //
    //
    //   This.$refs.vueStreamRef.init().then((stream) => {
    //     console.log('применение размытия в диалоге')
    //     return stream;
    //   })
    // },
    // /**endregion*/

    // /**region replaceStream запуск размытия экрана*/
    // async replaceStream(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
    //   if (This.videoValue) {
    //     This.videoConstraints = {optional: [{sourceId: This.videoValue}]}
    //   } else {
    //     This.videoConstraints = false
    //   }
    //
    //   await This.$refs.vueStreamRef.init().then((stream) => {
    //     console.log('th1')
    //     // STREAM_VIDEO = _STREAM_VIDEO
    //     // STREAM_AUDIO = _STREAM_AUDIO
    //     // STREAM_VIDEO_FON = stream
    //     // console.log(stream)
    //     // console.log(STREAM_VIDEO_FON)
    //     // This.$refs.roomXRCallWin.saveSett(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
    //   })
    //   setTimeout(() => {
    //     This.$refs.vueStreamRef.init().then((stream) => {
    //       console.log('th2')
    //       STREAM_VIDEO = _STREAM_VIDEO
    //       STREAM_AUDIO = _STREAM_AUDIO
    //       STREAM_VIDEO_FON = stream
    //       console.log(stream)
    //       console.log(STREAM_VIDEO_FON)
    //       This.$refs.roomXRCallWin.saveSettingsCall(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
    //     })
    //   }, 500)
    //
    // },
    // /**endregion*/


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

      if (level < 100) {
        $('#idLevelRTT').hide()
      } else if (level < 400) {
        $('#idLevelRTT').show()
        $('#idLevelRTT').css('color', 'orange')
      } else {
        $('#idLevelRTT').show()
        $('#idLevelRTT').css('color', 'red')
      }
      this.levelRTT = `${This.$t('levelRTT')}: ${level}`
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

    infoOver() {
      console.log('__2 over')
      // if (this.noFirefox) {
      //   $('#idRowInfo').css('width', '-webkit-fill-available')
      // } else {
      //   $('#idRowInfo').css('width', '-moz-available')
      // }

      $('#divInfoNameRoom').show()
      // $('#time-node').show()
      this.showMiniInfo = true;
    },

    infoOut() {
      console.log('__2 out')
      this.showMiniInfo = false
      setTimeout(() => {
        if (!this.isShowInfo && !this.showMiniInfo) {
          // if (This.$refs.roomXRCallWin.isMyFullVideo) {
          //   $('#idRowInfo').css('width', '60px')
          // } else {
          //   $('#idRowInfo').css('width', '158px')
          // }

          $('#divInfoNameRoom').hide()
          // $('#time-node').hide()
        }
      }, 500)


    },
    /**endregion*/

    /**region*/
    chatNewMessage(parsedMessage) {
      console.log(parsedMessage.message)
      // This.$refs.chatMessage.newRecvMessage(parsedMessage.message)// 13 Релиз
      // This.$refs.roomXRCallWin.newMessageChat(parsedMessage.message.content, parsedMessage.message.username)// 13 Релиз
    },

    /**region*/
    chatNewMessageOper(parsedMessage){
      var message = JSON.parse(parsedMessage.message);
      console.log('chatNewMessageOper message',message)
      // This.$refs.chatMessage.newRecvMessage(message) // 13 Релиз
      // This.$refs.roomXRCallWin.newMessageChat(message.content, message.username)// 13 Релиз
    },

    setDeviceParam(paramMessage) {
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

      this.setDataPhone()
    },

    planUpdateMessage(messages) {
      // setTimeout(() => {
      //   console.log('plan ', messages)
      //   var planMessage = JSON.parse(messages);
      //   for (var i = 0; i < planMessage.length; i++) {
      //     console.log('m ', planMessage[i])
      //     This.$refs.chatMessage.setPlanEl(planMessage[i])
      //   }
      // }, 2000)
    },

    chatUpdateMessage(messages) {// 13 релиз
      // setTimeout(()=>{
      //   console.log('chat ', messages)
      //   var chatMessage = JSON.parse(messages);
      //   for(var i=0; i<chatMessage.length; i++){
      //     console.log('m ',chatMessage[i])
      //     This.$refs.chatMessage.newRecvMessage(chatMessage[i])
      //   }
      //   var message = {
      //     id: 'sendMyPlan',
      //     roomId: This.roomID,
      //   };
      //   this.sendMessage(message)
      // }, 2000)
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

      console.log('send chat senderId  '+senderId)

      var message = {
        id: 'chatSendMessage',
        user: This.senderName,
        room: This.roomID,
        senderId: senderId,
        message: message
      };

      console.log('chatSendMessage message ', message)

      this.sendMessage(message)
    },

    addMyUserChat(idGH, senderName, avatar) {
      //This.$refs.chatMessage.addMeUserChat(This.roomID, idGH, senderName, avatar, This.wsURL);// 13 Релиз
    },

    addUserChat(idGH, senderName, avatar) {
      //This.$refs.chatMessage.addUserChat(idGH, senderName, avatar);// 13 Релиз
    },

    showChat(){
      //This.$refs.chatMessage.showChat();// 13 Релиз
    },

    clickCloseChat() {
      This.$refs.roomXRCallWin.setChatMessage(false);
    },
    // this.$refs.funcOperator.show(sender, this.roomIDuserName)
    /**endregion*/

    setLocalStorage(name, value) {
      // document.cookie = name + "=" + (value || "") + ";";
      localStorage[name] = value
    },

    getLocalStorage(name, type) {
      return localStorage[name]
      // var nameEQ = name + "=";
      // var ca = document.cookie.split(';');
      //
      // for (var i = 0; i < ca.length; i++) {
      //   var c = ca[i];
      //   while (c.charAt(0) == ' ') c = c.substring(1, c.length);
      //   if (c.indexOf(nameEQ) == 0){
      //     if(type == 'b'){
      //       return Boolean(c.substring(nameEQ.length, c.length))
      //     }else{
      //       return c.substring(nameEQ.length, c.length);
      //     }
      //   }
      // }
      // return null;
    },

    //изменение статуса микрофона при подключении
    setStartMic(){
      this.isStartMic = !this.isStartMic
    },

    //изменение статуса видео при подключении
    setStartVideo(){
      this.isStartVideo = !this.isStartVideo
    },

    clickMic(senderId, userName){
      if (userName.substring(0, 9) == 'Оператор_' || userName.substring(0, 9) == 'Operator_') {
        This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, true)
      }else{
        This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, false)
      }
    },

    disUser(_senderId, senderName){
      var message = {
        id: 'disUser',
        senderId: _senderId,
        room: This.roomID,
      }
      This.sendMessage(message);
    },

    clickCloseInfoUser(){
      This.$refs.roomXRCallWin.setPanelInfoUser(false);
    },

    clickCloseUser(senderId, user){
      This.$refs.roomXRCallWin.clickDeleteUserCall(senderId, user)
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
        SenderName: This.senderName,
        DeviceId: '-1',
        DeviceName: 'Chrome'
      };
      This.sendMessage(message);
    },

    // отправка текста на сервер для перевода
    sendTextTranslate(text, langSrc, langTgt, timestamp, messageId){
      // console.log('room this.paramValueRoom '+this.paramValueRoom)
      // console.log('room This.paramValueRoom '+This.paramValueRoom)
      // console.log('room this.roomID '+this.roomID)
      // console.log('room This.roomID '+This.roomID)
      console.log('text '+text + ' langSrc '+langSrc+ ' langTgt '+langTgt+ ' timestamp '+timestamp)
      var message = {
        id: 'sendTextTranslate',
        room: This.roomID,
        senderId: This.senderId,
        text: text,
        timestamp: timestamp,
        langSrc:langSrc,
        langTgt:langTgt,
        SenderName: This.senderName,
        DeviceId: '-1',
        DeviceName: This.typeBrowser,
        messageId:messageId
      };
      console.log('sendTextTranslate message',message)
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
        SenderName: This.senderName,
        DeviceId: '-1',
        DeviceName: This.typeBrowser
      };
      console.log('sendTextTranslate message', message)
      This.sendMessage(message);
    },

    //отправить событие начала записи
    soundRecAT(isStatus, langSrc, langTgt, wav){
      var message = {
        id: 'soundRecAT',
        room: This.roomID,
        senderId: this.senderId,
        isStatus: isStatus,
        langSrc:langSrc,
        langTgt:langTgt,
        wav: wav
      };
      This.sendMessage(message);
    },

    //отправить событие начала записи
    setSoundRecAT(langSrc, langTgt){

    },

    endedVideoBackground(e) {
      //isPreloading && !isCallSessionActive &&
      This.isVideoBackground = false
    },

    setArrow(isArrow, sender) {

      This.$refs.roomXRCallWin.setArrow(isArrow, sender)
    },
    // /**endregion*/
    //
    //
    // /**region funcUpdateIconMenu событие для иконок меню*/
    // funcUpdateIconMenu(isMain, sender) {
    //   This.$refs.roomXRCallWin.funcUpdateIconMenu(isMain, sender)
    // },
    // /**endregion*/
    //
    // /**region showFuncOper событие для вкл. функций рисования*/
    // showFuncOper(senderIdOper) {
    //   this.$refs.funcOperator.show(senderIdOper, This.roomID, This.senderId, This.$refs.roomXRCallWin.getOrient(senderIdOper), This.$refs.roomXRCallWin.getDelta(senderIdOper))
    // },
    //
    closeFuncOper(senderIdOper) {
      this.$refs.funcOperator.closePanel()
    },
    //
    // setDataPhone() {
    //   this.$refs.funcOperator.setDataPhone(this.oDraw, this.deltaCanvas)
    // },
    //
    // /**endregion*/
    // //endregion
    //
    // //region Функции stream и размытия _________________________________________________________________________________
    // /**region*/
    // addAudioTrack() {
    //   navigator.getUserMedia = navigator.getUserMedia ||
    //     navigator.webkitGetUserMedia ||
    //     navigator.mozGetUserMedia;
    //   if (navigator.getUserMedia) {
    //     navigator.getUserMedia({
    //         audio: true,
    //         video: false
    //       },
    //       function (stream) {
    //         STREAM_AUDIO = stream
    //       },
    //       function (err) {
    //         console.error("The following error occured: " + err.name)
    //       });
    //   } else {
    //     console.log("getUserMedia not supported");
    //   }
    // }
    // ,
    // /**endregion*/
    //
    // /**region onDegradation применение размытия в диалоге*/
    // onDegradation(_videoValue) {
    //
    // },
    // // async onDegradation(_videoValue){
    // //   // await This.$refs.vueStreamRef.init().then((stream) => {
    // //   // })
    // //   // setTimeout(()=>{
    // //   //   This.$refs.vueStreamRef.init().then((stream) => {
    // //   //     return stream
    // //   //   })
    // //   // },2000)
    // //   //
    // //   //
    // //   //
    // //   //
    // //   //
    // //   //
    // //   //
    // //   //
    // //   // This.$refs.vueStreamRef.init().then((stream) => {
    // //   //   return stream;
    // //   // })
    // // },
    // /**endregion*/
    //
    // /**region replaceStream запуск размытия экрана*/
    // // async replaceStream(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, _STREAM_VIDEO, _STREAM_AUDIO) {
    // //   // if(This.videoValue){
    // //   //   This.videoConstraints = {optional: [{sourceId: This.videoValue}]}
    // //   // }else{
    // //   //   This.videoConstraints = false
    // //   // }
    // //   //
    // //   // await This.$refs.vueStreamRef.init().then((stream) => {
    // //   //   // STREAM_VIDEO = _STREAM_VIDEO
    // //   //   // STREAM_AUDIO = _STREAM_AUDIO
    // //   //   // STREAM_VIDEO_FON = stream
    // //   //   // This.$refs.roomXRCallWin.saveSett(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
    // //   // })
    // //   // setTimeout(()=>{
    // //   //   This.$refs.vueStreamRef.init().then((stream) => {
    // //   //     STREAM_VIDEO = _STREAM_VIDEO
    // //   //     STREAM_AUDIO = _STREAM_AUDIO
    // //   //     STREAM_VIDEO_FON = stream
    // //   //     This.$refs.roomXRCallWin.saveSettingsCall(_audioInputValue, _audioOutputValue, _videoValue, _isDegradationSett, STREAM_VIDEO_FON, _STREAM_AUDIO)
    // //   //   })
    // //   // },500)
    // //
    // // },
    // /**endregion*/
    //
    //
    // /**region*/
    // async initStream() {
    //   // console.log('sig-test initStream - start')
    //   // console.log('initStream', This.videoValue)
    //   //if (This.videoValue) {
    //   if (This.videoValue != null) {
    //     This.videoConstraints = {optional: [{sourceId: This.videoValue}]}
    //   } else {
    //     This.videoConstraints = false
    //   }
    //   // console.log('initStream', This.videoValue, This.videoConstraints)
    //
    //   // if (This.videoValue != null) {
    //   //   await This.$refs.vueStreamRef.init().then((stream) => {
    //   //     STREAM_VIDEO_FON = stream
    //   //     console.log('sig-test initStream - stream', stream)
    //   //   })
    //   // }
    //   // console.log('sig-test initStream - end')
    // },
    // /**endregion*/
    // //endregion
    //
    // //region Доп функуии ###############################################################################################
    //
    // /**region режим разработчика, нагрузка сети отправки пакетов*/
    // onLevelRTT(isDev, level) {
    //
    //   if (level < 100) {
    //     $('#idLevelRTT').hide()
    //   } else if (level < 400) {
    //     $('#idLevelRTT').show()
    //     $('#idLevelRTT').css('color', 'orange')
    //   } else {
    //     $('#idLevelRTT').show()
    //     $('#idLevelRTT').css('color', 'red')
    //   }
    //   this.levelRTT = `${This.$t('levelRTT')}: ${level}`
    // },
    //
    // /**region confirmAgree ответ диалога*/
    // confirmAgree(type, mess) {
    //   if (type == 1) {
    //     This.sendMessage(mess);
    //   } else if (type == 2) {
    //     This.sendMessage(mess);
    //   }
    // }
    // ,
    // /**endregion*/
    //
    // /**region confirmCancel ответ диалога*/
    // confirmCancel() {
    //
    // }
    // ,
    // /**endregion*/
    //
    // /**region clickInfoShow Открытие окна с информацией*/
    // clickInfoShow() {
    //   this.isShowInfo = !this.isShowInfo
    // }
    // ,
    //
    // setBackground(url) {
    //   this.urlBackgroundFon = url
    // },
    //
    // // infoOver(){
    // //   if(!this.isFirefox){
    // //     $('#idRowInfo').css('width', '-webkit-fill-available')
    // //   }else{
    // //     $('#idRowInfo').css('width', '-moz-available')
    // //   }
    // //
    // //   $('#divInfoNameRoom').show()
    // //   $('#time-node').show()
    // //   this.showMiniInfo=true;
    // // },
    //
    // infoOut() {
    //   // this.showMiniInfo = false
    //   // setTimeout(()=>{
    //   //   if(!this.isShowInfo && !this.showMiniInfo){
    //   //     if(This.$refs.roomXRCallWin.isMyFullVideo){
    //   //       $('#idRowInfo').css('width', '60px')
    //   //     }else{
    //   //       $('#idRowInfo').css('width', '158px')
    //   //     }
    //   //
    //   //     $('#divInfoNameRoom').hide()
    //   //     $('#time-node').hide()
    //   //   }
    //   // },500)
    //
    //
    // },
    // /**endregion*/
    //
    // /**region*/
    // chatNewMessage(parsedMessage) {
    //   console.log('chatNewMessage message', parsedMessage.message)
    //   This.$refs.chatMessage.newRecvMessage(parsedMessage.message)
    //   This.$refs.roomXRCallWin.newMessageChat(parsedMessage.message.content, parsedMessage.message.username)
    // },
    //
    // /**region*/
    // chatNewMessageOper(parsedMessage) {
    //   var message = JSON.parse(parsedMessage.message);
    //   console.log('chatNewMessageOper message', message)
    //   This.$refs.chatMessage.newRecvMessage(message)
    //   This.$refs.roomXRCallWin.newMessageChat(message.content, message.username)
    // },
    //
    // setDeviceParam(paramMessage) {
    //
    //   console.log('setDeviceParam paramMessage', paramMessage)
    //
    //   This.wDraw = paramMessage.width
    //   This.hDraw = paramMessage.height
    //   This.oDraw = paramMessage.orient
    //
    //   if (This.oDraw == 'L') {
    //     This.deltaCanvas = This.wDraw / This.hDraw
    //     console.log('delta L ' + This.deltaCanvas + ' w ' + paramMessage.width + ' h ' + paramMessage.height)
    //   } else {
    //     This.deltaCanvas = This.hDraw / This.wDraw
    //     console.log('delta P ' + This.deltaCanvas + ' w ' + paramMessage.width + ' h ' + paramMessage.height)
    //   }
    //
    //   This.$refs.roomXRCallWin.setDelta(paramMessage.idTo, This.deltaCanvas)
    //
    //   this.setDataPhone()
    // },
    //
    // chatUpdateMessage(messages) {
    //   setTimeout(() => {
    //     console.log('chat ', messages)
    //     var chatMessage = JSON.parse(messages);
    //     for (var i = 0; i < chatMessage.length; i++) {
    //       console.log('m ', chatMessage[i])
    //       This.$refs.chatMessage.newRecvMessage(chatMessage[i])
    //     }
    //   }, 3000)
    //   // This.$refs.chatMessage.updateMessages(messages)
    // },
    //
    // chatSendMessage(senderId, message) {
    //
    //   console.log('send chat senderId  ' + senderId)
    //
    //   var message = {
    //     id: 'chatSendMessage',
    //     senderName: This.senderName,
    //     room: This.roomID,
    //     senderId: senderId,
    //     message: message
    //   };
    //
    //   console.log('chatSendMessage message ', message)
    //
    //   this.sendMessage(message)
    // },
    //
    // addMyUserChat(senderId, senderName, avatar) {
    //   This.$refs.chatMessage.addMeUserChat(This.roomID, senderId, This.senderName, avatar);
    // },
    //
    // addUserChat(senderId, senderName, avatar) {
    //   console.log('addUserChat(senderId, senderName, avatar)', senderId, senderName, avatar)
    //   This.$refs.chatMessage.addUserChat(senderId, senderName, avatar);
    // },
    //
    // showChat() {
    //   This.$refs.chatMessage.showChat();
    // },
    //
    setTranslation(isTranslate) {
      This.$refs.chatMessage.setTranslation(isTranslate);//// 13 Релиз
    },
    //
    // clickCloseChat() {
    //   This.$refs.roomXRCallWin.setChatMessage(false);
    // },
    //
    // clickCloseInfoUser() {
    //   This.$refs.roomXRCallWin.setPanelInfoUser(false);
    // },
    //
    // addNewOper() {
    //   this.isDlgContactOper = true
    //   return this.$root.restGet(`/webcall/listGroupCallUs`)
    //     .then(r => {
    //       // this.$refs.contactOperatorFormVue.initD(r)
    //       This.$refs.contactOperatorFormVue.show(r)
    //     })
    //   // this.isDialogListOper = !this.isDialogListOper
    //   // if( this.isDialogListOper){
    //   //
    //   // }else{
    //   //   this.$refs.contactOperatorFormVue.onClear()
    //   // }
    // },
    // // this.$refs.funcOperator.show(sender, this.roomIDuserName)
    // /**endregion*/
    //
    // emitAddUser(id, operSenderName) {
    //   this.$root.showInfo(This.$t('gc.addOperMessInfo'))
    //
    //   var jsonex = {'dv_id': id, 'nameOper': operSenderName, 'gcID': This.roomID}
    //
    //   console.log('ADD USER OPER json ', jsonex)
    //
    //   this.$root.restPost('webcall/postGCOperLink', jsonex)
    //     .then((res) => {
    //     })
    // },
    //
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
      if(type == 1)
        if(parsedMessage.isMy){
          This.$refs.noConnectInfoPanel.showInfoPhoneName(this.$t('gc.title.group.call.error'), this.$t('gc.no.connect.info.1.panel.text'), this.$t('gc.title.group.call')+': '+this.nameRoom)
        }else{
          This.$root.showErr(parsedMessage.nameUser + this.$t('gc.no.connect.info.1.message'))
        }
    },

    //
    // clickCloseUser(senderId, user) {
    //   This.$refs.roomXRCallWin.clickDeleteUserCall(senderId, user)
    // },
    //
    // clickMic(senderId, userName) {
    //   if (userName.substring(0, 9) == 'Оператор_' || userName.substring(0, 9) == 'Operator_') {
    //     This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, true)
    //   } else {
    //     This.$refs.roomXRCallWin.clickMuteUserCall(senderId, userName, false)
    //   }
    // },
    //
    // addUser(_senderId, senderName) {
    //   var message = {
    //     id: 'addUser',
    //     senderId: _senderId,
    //     room: This.roomID,
    //   }
    //   This.sendMessage(message);
    // },
    //
    // disUser(_senderId, senderName) {
    //   var message = {
    //     id: 'disUser',
    //     senderId: _senderId,
    //     room: This.roomID,
    //   }
    //   This.sendMessage(message);
    // },
    //
    // // this.$refs.funcOperator.show(sender, this.roomIDuserName)
    // /**endregion*/
    //
    // setLocalStorage(name, value) {
    //   // document.cookie = name + "=" + (value || "") + ";";
    //   localStorage[name] = value
    // },
    //
    // getLocalStorage(name, type) {
    //   return localStorage[name];
    //   // var nameEQ = name + "=";
    //   // var ca = document.cookie.split(';');
    //   //
    //   // for (var i = 0; i < ca.length; i++) {
    //   //   var c = ca[i];
    //   //   while (c.charAt(0) == ' ') c = c.substring(1, c.length);
    //   //   if (c.indexOf(nameEQ) == 0){
    //   //     if(type == 'b'){
    //   //       return Boolean(c.substring(nameEQ.length, c.length))
    //   //     }else{
    //   //       return c.substring(nameEQ.length, c.length);
    //   //     }
    //   //   }
    //   // }
    //   // return null;
    // },
    //
    // // eraseCookie(name) {
    // //   document.cookie = name + '=;';
    // // },
    //
    // //изменение статуса микрофона при подключении
    // setStartMic() {
    //   this.isStartMic = !this.isStartMic
    // },
    //
    // //изменение статуса видео при подключении
    // setStartVideo() {
    //   this.isStartVideo = !this.isStartVideo
    // },
    // //endregion
    //
    // getTimeTranslate() {
    //
    // },
    //
    // // отправка аудио на сервер
    // sendBlobAudioTranslate(blob, langSrc, langTgt, timestamp) {
    //   console.log('blob ' + blob)
    //   var message = {
    //     id: 'sendBlobTranslate',
    //     room: This.roomID,
    //     senderId: this.senderId,
    //     blob: blob,
    //     timestamp: timestamp,
    //     langSrc: langSrc,
    //     langTgt: langTgt,
    //     SenderName: This.senderName,
    //     DeviceId: '-1',
    //     DeviceName: 'Chrome'
    //   };
    //   This.sendMessage(message);
    // },
    //
    // // отправка текста на сервер для перевода
    // sendTextTranslate(text, langSrc, langTgt, timestamp) {
    //   // console.log('room this.paramValueRoom '+this.paramValueRoom)
    //   // console.log('room This.paramValueRoom '+This.paramValueRoom)
    //   // console.log('room this.roomID '+this.roomID)
    //   // console.log('room This.roomID '+This.roomID)
    //   console.log('text ' + text + ' langSrc ' + langSrc + ' langTgt ' + langTgt + ' timestamp ' + timestamp)
    //   var message = {
    //     id: 'sendTextTranslate',
    //     room: This.roomID,
    //     senderId: This.senderId,
    //     text: text,
    //     timestamp: timestamp,
    //     langSrc: langSrc,
    //     langTgt: langTgt,
    //     SenderName: This.senderName,
    //     DeviceId: '-1',
    //     DeviceName: 'Chrome'
    //   };
    //   console.log('sendTextTranslate message', message)
    //   This.sendMessage(message);
    // },
    //
    // //отправить событие начала записи
    // soundRecAT(isStatus, langSrc, langTgt, wav) {
    //   var message = {
    //     id: 'soundRecAT',
    //     room: This.roomID,
    //     senderId: this.senderId,
    //     isStatus: isStatus,
    //     langSrc: langSrc,
    //     langTgt: langTgt,
    //     wav: wav
    //   };
    //   This.sendMessage(message);
    // },
    //
    // //отправить событие начала записи
    // setSoundRecAT(langSrc, langTgt) {
    //
    // },
    //
    // endedVideoBackground(e) {
    //   //isPreloading && !isCallSessionActive &&
    //   This.isVideoBackground = false
    // },
    //
    // // Расчет height для региона отображения списка активных пользователей
    // setSizeUserList() {
    //   let z = $('.userListPh');
    //   if (z && z.length != 0) {
    //     let t = $('#userNameHeader')
    //     let b = $('.right-footer')
    //     let h = b.offset().top - t.offset().top - t.height()
    //     z.height(h)
    //   }
    // },

  }
}

</script>

<style scoped>
/* ============================================================== */
/* PHONE */
/* ============================================================== */
.gcMainPhoneCss{
  height: 100%;
  width: 100%;
  top: 0;
  position: fixed;
  z-index: 200;
}

.colMainGCPh{
  display: flex;
  flex-direction: column;
  height: calc(100% - 99px)!important;
}
@media screen and (orientation:landscape) {
  .colMainGCPh{
    display: flex;
    flex-direction: column;
    height: calc(100% - 99px)!important;
  }
}


.dImgLabelGC{
  background: var(--v-xr2D3-darken4);
  width: 100%;
  padding-top: 8px;
  padding-left: 8px;
}

.vCard-2PhP{
  width: 100%;
  background: var(--v-xr2L4-base);
  border-color: var(--v-xr2D2-base);
}

.xr2gcPh .vCard-2 {
  height: 100%;
  width: 100%;
  background: var(--v-xr2L4-base);
  border-width: 3px;
  border-color: var(--v-xr2D2-base);
}

.xr2gcPh .vCard-1 {
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

.xr2gcPh .vCard-2 .v-card__title {
  border-top-right-radius: 6px !important;
}

.xr2gcPh .videoPrevDiv {
  object-fit: contain;
  /*border-radius: 10px !important;*/
  border-top-left-radius: 10px !important;
  border-bottom-left-radius: 10px !important;
}

.xr2gcPh .divBtnPreGC {
  position: absolute;
  bottom: 12px;
  left: calc(50% - 50px);
}

.xr2gcPh .btnFooterMic {
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

.xr2gcPh .btnFooterVid {
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

.xr2gcPh .btnSetting {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-radius: 5px !important;

  position: absolute;
  bottom: 12px;
  right: 12px;
}

.vCard-1PhP{
  width: 100%;
  flex: auto;
  background: var(--v-xr2L4-base);
  border-color: var(--v-xr2D2-base);
}

.userListPh {
  overflow: auto;
  height: 60vh!important;
}
@media screen and (orientation:landscape) {
  .userListPh {
    overflow: auto;
  }
}

.xr2gcPh .right-footer {
  position: absolute;
  bottom: 8px;
  width: 100%;
  /*background: var(--v-xr2L4-base);*/
  background: var(--xr-gc-pane-background-color);
  /*background: red;*/
  padding-left: 12px;
  padding-right: 12px;
}

.xr2gcPh .right-footer >>> label {
  background: var(--v-xr2L4-base);
  font-size: 16px !important;
}

.xr2gcPh .btnCallPrev {
  margin-top: 6px;
}

/* ============================================================== */
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

.infoTooldevPh {
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

.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldevPh {
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
p.containerGNanePH {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-weight: 400;
  text-align: start;
  margin: 0;
  text-shadow: -1px 0 rgb(0 0 0 / 68%), 0 1px rgb(0 0 0 / 68%), 1px 0 rgb(0 0 0 / 0%), 0 -1px rgb(0 0 0 / 0%);
  color: var(--v-xr2L5-base);
  top: 28px;
  margin-right: 8px;
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

.devMinivideoPhs {
  width: 15%;
  height: calc(80% - 33px);
  position: absolute;
  right: 0;
  top: calc(20% + 33px);
  overflow-y: auto;
  overflow-x: hidden;
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
  display: -ms-flexbox;
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

.row.pa-6.text-center.infoTooldevPh {
  margin: 0;
  padding: 0 !important;
  height: 30px;
  right: 0;
  width: auto;
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

.posH100Pgcmain {
  height: calc(100% - 101px);
}

/*------------------------------------------------------------------*/


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

.divWaitConnPh {
  position: fixed;
  top: 0;
  width: 100%;
  height: 100%;
  background: var(--v-xr2L1-base);
  z-index: 1000;
}

.divWaitConnBlockIPh {
  position: fixed;
  top: calc(50% - 130px);
  width: 100%;
  margin-left: 0px!important;
  height: 220px;
  background: white;
  border-radius: 4px !important;
}
@media screen and (orientation:landscape) {
  .divWaitConnBlockIPh {
    position: fixed;
    top: calc(50% - 110px);
    left: calc(50% - 200px);
    width: 400px;
    height: 220px;
    background: white;
    border-radius: 4px !important;
  }
}

.vctWaitt {
  margin-top: 30px;
  font-size: 18px;
}

.vpspanWait {
  text-align: center
}

.v-progress-circular.mainCircularStartWt.v-progress-circular--indeterminate.primary--text {
  margin-left: calc(50% - 25px);
  margin-top: 20px;
}


.edi {
  width: 100%;
  height: 100%;
  position: fixed;
  background: #f1f6fa;
  z-index: 99999999;
  top: 0;
  padding-left: 25%;
}

.ediD {
  position: absolute;
  width: auto;
  min-width: 50%;
  border: 1px solid #a5a5a5;
  border-radius: 5px;
  margin-top: 260px;
  background: white;
}

.ediT {
  width: 100%;
  margin: 0 !important;
  padding-left: 20px !important;
  padding: 8px;
  font-size: 26px;
  border-bottom: 1px solid #b7b7b7;
}

.ediP {
  margin: 0px !important;
  padding: 8px;
  font-size: 22px;
  margin-left: 12px !important;
  margin-top: 12px !important;
  margin-bottom: 12px !important;
}

.ediBtn {
  bottom: 6px;
  right: 6px;
  position: absolute;
}

.flexParticipantsCss{
  background: rgb(60, 60, 61);
  width: 100%;
}

.partPH{
  width: 100%;
  position: absolute;
  overflow-y: auto;
  overflow-x: hidden;
}

.xr2gc .toolbar-accent {
  background: var(--xr-gc-pane-accent-background-color);
  color: var(--xr-gc-pane-accent-color);
}

</style>
