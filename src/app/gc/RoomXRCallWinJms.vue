<!--suppress CssUnresolvedCustomProperty, CssInvalidFunction, CssRedundantUnit -->
<template>
  <div id="tt1000">
    <div v-if="mainWin>0" class="div-mainwin">
      <p class="p-mainwin">{{ textFuncOther }}</p>
    </div>

    <div v-if="!isStartCall && isCallSessionActive" id="idconnectionCalldiv" class="connectionCalldiv">
      <p class="connectionCalldivP">
        <v-progress-circular
            :size="18"
            class="pr-4"
            color="v-xr2L5-base"
            indeterminate
        ></v-progress-circular>
        {{$t('gc.link.input')}}</p>
    </div>

    <!-- Новый код jitsi-------------------------------------------------->
    <!---------------------------------------------------------------->

    <!-- Компонент управления конференцией -->

    <!---------------------------------------------------------------->


    <canvas id="background-canvas" style="position:absolute; top:-99999999px; left:-9999999999px;"></canvas>

    <div v-if="isMoreWin" class="div-moreWinMAX" @click="isMoreWin=false">
      <div v-if="isMoreWin" v-bind:class="[isTranslationf ? 'div-moreWin-AT' : 'div-moreWin']">
        <v-card
            class="mx-auto"
            max-width="500"
            tile
        >
          <v-list flat>
            <!--          <v-subheader>REPORTS</v-subheader>-->
            <v-list-item-group>
              <v-list-item
                  v-for="(item, i) in itemsMoreWin"
                  :key="i"
                  class="itemList-more"
                  @click="clickItemMore(item)"
              >
                <v-list-item-icon class="itemList-more-itemicon">
                  <v-icon class="itemList-more-icon" v-text="item.icon"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title class="itemList-more-title" v-text="item.text"></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card>
      </div>
    </div>

    <!--Режим разработчика-->
    <div v-if="devGC">
      <p style="
      z-index: 1000;
    position: absolute;
    top: 0px;
    padding: 4px;
    left: 5px;
    font-size: 20px;
    font-weight: 500;
    color: rgb(193, 52, 52);">Режим разработчика</p>

      <v-row style="
        z-index: 1000;
        position: absolute;
        top: 0px;
        padding: 4px;
        right: 50%;
        border-radius: 5px;
        border: 2px solid rgb(238 97 97);">
        <XrBtn
            class=""
            @click="clickEditBitrate"
            type="cancel"
            :text="isDevBitrate ? '+ Bitrate' : '- Bitrate'"/>
        <XrBtn
            :text="'скриншот'"
            class=""
            type="cancel"
            @click="testScreenshot"/>
      </v-row>
    </div>
    <!---->

    <!--Запись экрана -->
    <div v-if="isSaveCall">
      <v-row class="rowSaveCall">
        <!--        <v-icon class="iconSvaeCall">fas fa-record-vinyl</v-icon>-->
        <p style="
 padding: 7px;
    right: 0;
    font-size: 14px;
    font-weight: 500;
    color: rgb(193, 52, 52);">Идет запись сессии</p>
      </v-row>
    </div>
    <!---->

    <!--    TODO доделать футер -->
    <!--    <v-footer>-->
    <!--      <v-card-->
    <!--        flat-->
    <!--        tile-->
    <!--        width="10%"-->
    <!--        class="lighten-1 text-center hideButtonsPanel"-->
    <!--        style="background: rgba(0,0,0,0); padding: 5px; position: absolute; z-index: 5000; pointer-events: all"-->
    <!--      >-->
    <!--        <v-card-text>-->
    <!--          <v-btn v-if="isFullScreen"  color="white"-->
    <!--                 min-width="40"-->
    <!--                 min-height="40"-->
    <!--                 class="ml-1 btnFooter1"-->
    <!--                 :title="$tc('chik', 2)"-->
    <!--                 @click="showHideButtonsPanel">-->
    <!--            <v-icon-->
    <!--              color="black"-->
    <!--              size="24"-->
    <!--              dark-->
    <!--              v-if="isPanelShow"-->
    <!--            >-->
    <!--              mdi-24px mdi-eye-->
    <!--            </v-icon>-->

    <!--            <v-icon-->
    <!--              color="black"-->
    <!--              size="24"-->
    <!--              dark-->
    <!--              v-if="!isPanelShow"-->
    <!--            >-->
    <!--              mdi-eye-off-->
    <!--            </v-icon>-->
    <!--          </v-btn>-->
    <!--        </v-card-text>-->
    <!--      </v-card>-->
    <!--    </v-footer>-->


    <v-footer
        style="pointer-events: none;"
        v-if="isCallSessionActive"
        :padless=true :absolute=true
        class="vFooterRoom">


      <!--      <v-dialog v-model="isMicroWithSpaceBar" v-if="isMicroWithSpaceBar && showTip">-->
      <!--        <div v-if="isMicroWithSpaceBar"-->
      <!--             style="left: 9%; bottom: 5%; position: fixed; width: 470px; height: 30px; background-color: #dedede;">-->
      <!--          &lt;!&ndash;          <v-toolbar&ndash;&gt;-->
      <!--          &lt;!&ndash;            density="compact"&ndash;&gt;-->
      <!--          &lt;!&ndash;            class="ml-auto"&ndash;&gt;-->
      <!--          &lt;!&ndash;            style="z-index: 5000; right: 0px; top:0px; position: absolute;&ndash;&gt;-->
      <!--          &lt;!&ndash;          justify-content: center">&ndash;&gt;-->
      <!--          &lt;!&ndash;            <v-btn elevation="6"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   density="compact"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   max-width="12"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   max-height="20"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   class="mr-2"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   fab&ndash;&gt;-->
      <!--          &lt;!&ndash;                   color="grey-lighten-1"&ndash;&gt;-->
      <!--          &lt;!&ndash;                   @click="showTip=false"&ndash;&gt;-->
      <!--          &lt;!&ndash;            style="top: 0px; right: 0px; position: absolute">&ndash;&gt;-->
      <!--          &lt;!&ndash;              <v-icon size="15" color="grey-darken-1">mdi-close</v-icon>&ndash;&gt;-->
      <!--          &lt;!&ndash;            </v-btn>&ndash;&gt;-->
      <!--          &lt;!&ndash;          </v-toolbar>&ndash;&gt;-->
      <!--&lt;!&ndash;          <div style="font-family: Rooftop; font-size: 14px; top: 5px; left: 12px; position: absolute">&ndash;&gt;-->
      <!--&lt;!&ndash;            Чтобы включить микрофон, нажмите и удерживайте пробел&ndash;&gt;-->
      <!--&lt;!&ndash;          </div>&ndash;&gt;-->
      <!--        </div>-->
      <!--      </v-dialog>-->

      <v-card
          flat
          tile
          width="100%"
          class="lighten-1 text-center"
          style="background: rgba(0,0,0,0); padding: 10px"
      >
        <v-card-text>
          <div style="position: absolute; z-index: 5000; pointer-events: all" >
            <v-btn v-if="isFullScreen"  color="white"
                   style="border-radius: 0 !important;"
                   min-width="44"
                   min-height="44"
                   class="ml-1 btnFooter1"
                   :title="isPanelShow ? $t('chat.show-buttons') : $t('chat.hide-buttons')"
                   @click="showHideButtonsPanel">
              <v-icon
                  color="black"
                  size="23"
                  dark
                  v-if="isPanelShow"
              >
                mdi-24px mdi-eye
              </v-icon>

              <v-icon
                  color="black"
                  size="23"
                  dark
                  v-if="!isPanelShow"
              >
                mdi-eye-off
              </v-icon>
            </v-btn>
          </div>


          <div class="lowButtonsPanel">
            <v-btn color="white" fab
                   class="mr-1 btnFooter1"
                   :title="isStreamMic ? $t('gc.func.disable-mic') : $t('gc.func.play-mic')"
                   @click="setIsMic">
              <v-icon v-if="isStreamMic" style="font-size: 16px;">fas
                fa-microphone-alt
              </v-icon>
              <v-icon v-if="!isStreamMic" style="font-size: 16px;">fas
                fa-microphone-alt-slash
              </v-icon>
            </v-btn>

            <v-btn color="white" fab
                   class="mr-1 btnFooter2"
                   :title="hasCamera ? (isStreamVideo ? $t('gc.func.disable-camera') : $t('gc.func.play-camera')) : $t('ex.error.video')"
                   @click="setIsVideoStreamClick">
              <v-icon v-if="isStreamVideo && hasCamera" style="font-size: 16px;">fas fa-video</v-icon>
              <v-icon v-else style="font-size: 16px;">fas fa-video-slash</v-icon>
            </v-btn>

            <v-btn v-if="!isPhone" color="white" fab
                   class="mr-1 btnFooter2"
                   :title="$t('gc.more')"
                   @click="setIsMoreWin">
              <v-icon style="font-size: 16px;">fas fa-ellipsis-v</v-icon>
            </v-btn>

            <v-btn v-if="!isPhone" color="white" fab
                   class="mr-1 btnFooter2"
                   @click="setAddFuncScreenSharing"
                   @mouseover="setMouseoverSS"
                   @mouseout="isTextBtnSS = false"
                   :title="isScreenSharing ? $t('gc.func.disable-winscreen') : $t('gc.func.play-winscreen')">
              <v-icon v-if="isScreenSharing" style="font-size: 20px;">far fa-window-close</v-icon>
              <!--            <v-icon v-else style="font-size: 16px;">fas fa-chalkboard-teacher</v-icon>-->
              <v-icon v-else style="font-size: 16px;">mdi mdi-24px mdi-monitor-share</v-icon>
            </v-btn>
            <!--            :text="!isTextBtnSS ? isScreenSharing  ?  $t('gc.demoScrS') : $t('gc.demoScrS.close') : ''"-->


            <v-btn v-if="!isXrOper" color="red" fab
                   class="mr-1 btnFooter3"
                   :title="$t('gc.func.close-call')"
                   @click="closeCall">
              <v-icon style="font-size: 16px; color: white">fas fa-phone-slash</v-icon>
            </v-btn>

            <!-- Меню правых нижних кнопок -->
            <v-row class="vrow-menu-right">

              <!--            <XrBtn-->
              <!--              v-if="isSaveCallMy"-->
              <!--              class="fabAddSet save"-->
              <!--              @click="stopSaveCall"-->
              <!--              :text="!isMouseMoveSave ? '': 'Остановить'"-->
              <!--&lt;!&ndash;              icon="fas fa-record-vinyl"&ndash;&gt;-->
              <!--              @mouseover="isMouseMoveSave = true"-->
              <!--              @mouseout="isMouseMoveSave = false">-->
              <!--              <v-icon style="font-size: 16px; color: #e5e5e5!important;"-->
              <!--                      @mouseover="isMouseMoveSave = true">fas fa-record-vinyl</v-icon>-->
              <!--&lt;!&ndash;              <v-icon v-if="!isMouseMoveSave" style="font-size: 16px; color: #e5e5e5!important;"&ndash;&gt;-->
              <!--&lt;!&ndash;                      @mouseover="isMouseMoveSave = true">fas fa-record-vinyl</v-icon>&ndash;&gt;-->
              <!--            </XrBtn>-->

              <!------------------------------------------------------------->
              <!--  -->
              <XrBtn
                  v-if="isSaveCallMy"
                  class="fabAddSet save"
                  @click="stopSaveCall"
                  @mouseover="isMouseMoveSave = true"
                  @mouseout="isMouseMoveSave = false">
                <v-icon style="font-size: 16px; color: #e5e5e5!important;"
                        @mouseover="isMouseMoveSave = true">fas fa-record-vinyl
                </v-icon>
                <p v-if="isMouseMoveSave"
                   style="color: white; top: 28px; margin-bottom: 2px; margin-left: 4px; pointer-events: none;">
                  Остановить
                </p>
              </XrBtn>


              <!--            <v-btn v-if="!isPhone && numUsers>1" color="white"-->
              <!--                   class="fabAddSet" isScreenSharing-->
              <!--                   :title="isScreenSharing ? $t('gc.func.disable-winscreen') : $t('gc.func.play-winscreen')"-->
              <!--                   :text="'Демонстрация'"-->
              <!--                   @click="setAddFuncScreenSharing">-->
              <!--              <v-icon v-if="isScreenSharing" style="font-size: 18px;">fas fa-ban</v-icon>-->
              <!--              <v-icon v-if="!isScreenSharing" style="font-size: 18px;">fas fa-chalkboard-teacher</v-icon>-->
              <!--            </v-btn>-->


              <v-btn v-if="devGC" color="white" fab
                     class="mr-1 btnFooter2"
                     @click="setNewMicADd">
                <v-icon style="font-size: 16px;">fas fa-assistive-listening-systems</v-icon>
              </v-btn>

              <!------------------------------------------------------------->
              <!-- XXX Открыть список участников -->
              <v-btn style="width: auto!important" v-if="!isPhone" :color="numUsersExternal == 0 ? whiteBtn : 'xr2L1'" fab
                     class="mr-1 btnFooter2 gc-users-btn"
                     :title="numUsersExternal == 0 ? $t('gc.addOperDialog') : ($t('gc.externalUsers') + numUsersExternal)"
                     @click="setPanelInfoUser">
                <template v-if="!isPanelInfoUser && numUsersExternal == 0">
                  <span class="gc-users-span">{{ numUsers }}</span>
                  <v-icon v-if="numUsersExternal != 0" class="gc-users-icon-ext xr-attention-blinker">mdi-24px
                    mdi-account
                  </v-icon>
                  <v-icon v-else class="gc-users-icon">far fa-user</v-icon>
                </template>
                <template v-if="!isPanelInfoUser && numUsersExternal>0">
                  <span class="gc-users-span">{{ $t('gc.externalUsers') + numUsersExternal }}</span></template>
                <v-icon v-if="isPanelInfoUser">mdi-24px mdi-account</v-icon>
              </v-btn>

              <!--            <div v-if="numUsers>0" class="divEctranlUsers"><p class="pEctranlUsers">{{numUsersExternal}}{{numUsersExternal}}</p></div>-->

              <!------------------------------------------------------------->
              <!-- Чек-листы -->
              <v-btn v-if="!isPhone && isChikAccess" color="white" fab
                     class="mr-1 btnFooter2"
                     :title="$tc('chik', 2)"
                     @click="setPanelChik">
                <v-icon>mdi-24px mdi-clipboard-list-outline</v-icon>
              </v-btn>

              <!------------------------------------------------------------->
              <!-- Текстовые сообщения -->
              <v-btn v-if="!isPhone" color="white" fab
                     class="mr-1 btnFooter2"
                     :title="$t('gc.chat')"
                     @click="setChatMessage">
                <v-icon v-if="!isChatShow" style="font-size: 16px;">far fa-comment-alt</v-icon>
                <v-icon v-if="isChatShow" style="font-size: 16px;">fas fa-comment-alt</v-icon>
              </v-btn>

              <!------------------------------------------------------------->

              <!-- Панель разработчика -->
              <v-btn v-if="isDevGC" :title="$t('gc.dev.btn')" class="mr-1 btnFooter2"
                     color="white"
                     fab
                     @click="setDevPanel">
                <v-icon style="font-size: 24px;">fab fa-dev</v-icon>
              </v-btn>


              <v-btn v-if="!isPhone && numUsers>1" color="white" fab
                     class="mr-1 btnFooter2"
                     :title="isStreamVideo ? $t('gc.func.btn.maketvideo') : $t('gc.func.btn.maketvideo') "
                     @click="setIsTypeWin">
                <img v-if="typeMaket == 2" :src="svgLayoutV1_" alt="" style="width: 34px; height: 22px;">
                <img v-else-if="typeMaket == 1 " :src="svgLayoutV2_" alt="" style="width: 34px; height: 22px;">
                <img v-else :src="svgLayoutV3_" alt="" style="width: 34px; height: 22px;">

                <v-row v-if="isTypeWin" class="rowIconLayout">
                  <v-btn v-if="!isPhone && numUsers>1 && typeMaket != 3" color="white" fab
                         class="mr-1 btnFooter2"
                         :title="maketvideo3"
                         @click="setTypeWin(3, false, true, true)">
                    <img :src="svgLayoutV3_" alt="" style="width: 34px; height: 22px;">
                  </v-btn>
                  <v-btn v-if="!isPhone && numUsers>1 && typeMaket != 2" color="white" fab
                         class="mr-1 btnFooter2"
                         :title="maketvideo2"
                         @click="setTypeWin(2, false, true, true)">
                    <img :src="svgLayoutV1_" alt="" style="width: 34px; height: 22px;">
                  </v-btn>
                  <v-btn v-if="!isPhone && numUsers>1  && typeMaket != 1" color="white" fab
                         class="mr-1 btnFooter2"
                         :title="maketvideo1"
                         @click="setTypeWin(1, false, true, true)">
                    <img :src="svgLayoutV2_" alt="" style="width: 34px; height: 22px;">
                  </v-btn>

                </v-row>
              </v-btn>

            </v-row>
          </div>

          <!--          <v-btn v-if="!isPhone && numUsers>1" color="white" fab-->
          <!--                 class="fabAddSet"-->
          <!--                 :title="$t('gc.func.btn')"-->
          <!--                 @click="setAddFunc">-->
          <!--            <v-icon style="font-size: 18px;">fas fa-ellipsis-v</v-icon>-->
          <!--          </v-btn>-->

          <!-- ======================================================= -->

          <!-- ======================================================= -->

        </v-card-text>
      </v-card>
    </v-footer>

    <!--    <v-btn style="z-index: 100000; position: absolute; top: 0" color="white" fab-->
    <!--           class="ml-6 fabIconLayout2"-->
    <!--           @click="stopSaveVideo()">-->
    <!--      <img :src="svgLayoutV2_" alt="" style="width: 34px; height: 22px;">-->
    <!--    </v-btn>-->

    <RoomXRScreenSharing ref="resRoomXR" :lang="paramValueLang" :wsURL="wsURL"
                         @screenSharingClose="screenSharingClose"></RoomXRScreenSharing>

    <SaveVideoSession ref="refSaveVideo" :lang="paramValueLang" @startSave="startSave" :wsURL="wsURL"
                      :audioOutputValue="audioOutputValue" :hasCamera="hasCamera"
                      @saveClose="saveClose" @saveSecondClose="saveSecondClose"></SaveVideoSession>

    <DevPanel ref="refDevPanel" :itemsUsersInfo="itemsUsersInfo" @clickCloseDevPanel="setDevPanel"></DevPanel>

    <Confirm ref="refConfirm" @agree="confirmAgree" @cancel="confirmCancel" @agreeSS="closeSSAndScrS"></Confirm>

    <div id="idDivCanvasOther">
      <canvas id="idCanvasPrintOther" class="canvasPrintOther"
              :width="wCanvas" :height="hCanvas"></canvas>
    </div>

    <!--Предпросмотр скриншотов -->
    <XrPreviewScreenshots ref="previewScreenshots" :typeCall="1" @deleteScreenshot="deleteScreenshot"
                          @sendToChat="sendToChat"
                          @sendToChekList="sendToChekList" @sendToOperator="sendToOperator"></XrPreviewScreenshots>
    <!---->

    <!-- Кнопка для сокрытия панелеей нижних кнопок   -->
    <!--<div style="position: absolute; ">-->
    <!--  <v-btn v-if="isFullScreen"  color="white" fab-->
    <!--         class="ml-1 btnFooter2"-->
    <!--         :title="$tc('chik', 2)"-->
    <!--         @click="showHideButtonsPanel">-->
    <!--    <v-icon-->
    <!--      color="white"-->
    <!--      size="24"-->
    <!--      left-->
    <!--      dark-->
    <!--      v-if="isPanelShow"-->
    <!--    >-->
    <!--      mdi-eye-->
    <!--    </v-icon>-->

    <!--    <v-icon-->
    <!--      color="white"-->
    <!--      size="24"-->
    <!--      left-->
    <!--      dark-->
    <!--      v-if="!isPanelShow"-->
    <!--    >-->
    <!--      mdi-eye-off-->
    <!--    </v-icon>-->
    <!--  </v-btn>-->
    <!--</div>-->
  </div>
</template>

<script>
import IKSAR_WEBRTC from '@/assets/ap_kurento/js/iksarGCConnectionWebrtc'
import $ from 'jquery'
import Vue from 'vue'
import RoomXRScreenSharing from "@/app/gc/func/RoomXRScreenSharing";
import Confirm from "@/app/gc/func/Confirm";

import svgLayoutV1 from '@/assets/img/layout_v3_sidebar.svg';
import svgLayoutV2 from '@/assets/img/layout_v3_tiled.svg';
import svgLayoutV3 from '@/assets/img/layout_v3_tiled_my.svg';
import SaveVideoSession from "@/app/gc/func/SaveVideoSession";
import AudioTranslation from "@/app/gc/func/translation/AudioTranslation";
import cst from "@/const";
import DevPanel from "@/app/gc/func/right/DevPanel";
import XrPreviewScreenshots from "../global/media/XrPreviewScreenshots";


var This

var recorderTest

var URL = ''

var DEFAULT_SAVED_VIDEO_FORMAT = 'webm'

let x = 0;
let y = 0;

var isOneAdd = false;

var stream = stream || {}

var this_module = {}

let STREAM_VIDEO = null
let S_OLD_TREAM_VIDEO_FON = null;
let STREAM_AUDIO = null;
let STREAM_AUDIO_SAVE = null

function isValidURL(string) {
  for (let i = 0; i < string.length; i++) {
    var res = string[i].match(/(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/g);
    if (res !== null)
      return i;
  }
  return -1;
};

export default {
  name: "RoomXRCallWinJms",
  components: {
    WebRTCManager,
    XrPreviewScreenshots, DevPanel, AudioTranslation, SaveVideoSession, RoomXRScreenSharing, Confirm},
  props: {
    isCallSessionActive: false, // страница в режиме звонка
    isPhone: false,
    numUsers: 0, // кол-во пользователей
    numUsersExternal: 0, // кол-во ожидающих
    isDegradation: false,
    isFirefox: false,
    paramValueRoom: '',
    isUserLic: false,
    avatar: '',
    devUser: false,
    wsURL: '',
    isMirror: true,
    hasCamera: true,
    hasMic: true,
    isExternalUser: false,
    itemsUsersInfo: null,
    isStartCall: false,
    /**
     * ИЗМЕНЕНИЕ БЫЛО ЗДЕСЬ **/
    isMicroWithSpaceBar: {
      type: Boolean,
      default: false
    },
  },

  isPanelShowOptions: false,

  data() {
    return {
      isFullScreen: false,
      isPanelShow: false,

      showTip: true,
      isSpaceDown: false,
      wasKeyUp: true, //отжали ли клавишу
      /**
       * КОНЕЦ ИЗМЕНЕНИЙ **/

      ws: null,

      iksarWebrtc: IKSAR_WEBRTC,
      room: this.paramValueRoom,

      isActiveLink: false, // доступна ли ссылка, ответ от сервера
      isPreloading: true, // подгрузка страницы
      isAvtoStart: false, // автоматически подключать прользователя
      dataLinkInfo: null, // информация по ссылке, получаем от сервера

      numScreenSharinWin: 0, // кол-во активных расшариваний экрана
      isSoundWinFunc: true, // работа функции отображения говорящего пользователя
      mainWin: 0, // включена ли функция отображения одного окна, 0 - не отображать

      nameRoom: 'нет имени', // имя группы
      nameRoomGr: this.$t('gc.group'), // имя группы
      nameRoomBigGr: this.$t('gc.group'), // длиное имя группы

      issenderName: true, // есть ли имя пользователя
      senderId: null, // senderId - сгенерированный id в истории звонка

      paramValueLang: this.$route.params.lang, // данные о языке
      paramValueUser: this.$route.params.userId, // данные о id пользователя

      senderName: '', // имя пользователя
      senderNameReplace: '',

      usersCall: this.$t('gc.no-members'),
      gcLinkInput: this.$t('gc.link.input'),
      nameRoomBig: '',

      infoDataDate: '', // данные о дате
      infoDataLive: '', // данные о времени
      infoDataOU: '', // данные о ОЕ

      isStreamMic: true, // мой вкл.выкл микрофон
      isStreamVideo: true, // мое вкл.выкл видео
      isStreamVideoOld: true,
      isMaxOperVideo: false,

      typeMaket: 1, // тип макета 1 - много, 2 - одно большое, 3 - много с моим видео
      typeMaketOld: 1,
      isTypeWin: false, // отображение выбора макета

      audioNewCall: new Audio('/mdb/audio/newCall.mp3'),
      audioDeleteCall: new Audio('/mdb/audio/deleteCall.mp3'),

      isShowInfo: false, // отображение подробной информации по ссылке

      // isDialogSett: false,

      audioInputValue: null, // настройки - выбор микрофона
      audioOutputValue: null, // настройки - выбор динамика
      videoValue: null, // настройки - выбор камеры

      isXrOper: false, //  пока спорная переменная
      isXrOperVid: false, // пока спорная переменная

      isAddFunc: false, // для отображения дополнительных функций
      isScreenSharing: false, // для вкл\выкл расшаривания экрана
      wsScreen: null, // веб сокет для расшаривания экрана

      isMyFullVideo: false, // показывать мое видео вместе со всеми
      // isDegradation: false, // размытие видео
      isDegradationSett: false, // размытие видео

      isJobFuncOper: false,//включение функции, окно оператора будет на главном экране для всех пользователей
      isDrawLine: false,// функция рисования

      isSound: false, // статус что я говорю, не отправлять запрос
      backgroundBlurAmount: 1, // размытие экрана, props размытия

      isAddToAll: false, // показывать меня со всеми вместе

      show2: true,
      isPw: false, // нужен ли пароль
      pw: '',
      pwUser: '',
      teamSound: true, // переменная для дельты времени говорящего

      isVS_SS: true, // костыль, что бы убирать видео, если не нужно
      isStartCallT: false,

      svgLayoutV1_: svgLayoutV1,
      svgLayoutV2_: svgLayoutV2,
      svgLayoutV3_: svgLayoutV3,

      maketvideo1: '',
      maketvideo2: '',
      maketvideo3: '',

      typessNo: 'ssNo',
      typessYes: 'ssYes',

      isTextBtnSS: false, // для названия кнопки во время активного расшаривания
      isMoreWin: false, // панель ЕЩЕ

      itemsMoreWin: [
        {text: this.$t('gc.group.menu.1'), icon: 'fa-expand-arrows-alt', type: 1},
        {text: this.$t('gc.group.menu.2'), icon: 'fa-sliders-h', type: 2},
        {text: this.$t('gc.group.menu.3'), icon: 'fa-microphone-slash', type: 3}
      ],

      isPaint: false, // включено рисование у оператора, запрет на изменение окон
      isShowFunc: false, // включены доп функции у оператора, запрет на изменение размера окон
      isShowFuncOther: false, // вкючена функция управления у другого пользователя
      isSenderFuncOther: null, // кто управляет оператором
      textFuncOther: '',

      devTestSession: false, // режим разработки
      testUserWin: 0,
      numHighNet: 0, // статус высокай нагрузки сети
      isVideoHardNet: false, // статус отключения видео

      otherCTX: null, //рисование от другого пользоватлея
      otherX: -100, //рисование от другого пользоватлея
      otherY: -100, //рисование от другого пользоватлея
      wCanvas: 0, //рисование от другого пользоватлея
      hCanvas: 0, //рисование от другого пользоватлея

      devGC: false, // режим разработчика на странице
      isTranslation: false, // режим переводчика
      isTranslationf: false, // режим переводчика
      oldsetTypeWin: 0, //This.setTypeWin(This.oldsetTypeWin, false, true)
      minBitisVideoStream: true,// статус совего потока во время функции низкого битрейта

      isDevBitrate: false,
      isSaveCall: false,
      isSaveCallMy: false,

      isMouseMoveSave: true,
      isMouseMoveInfoUser: false,
      isChatShow: false,
      isDeveloper: false,

      whiteBtn: '#ffffff',
      orangBtn: '#cb5400',

      isPanelInfoUser: false,
      dataUserRecall: [], // список треков подключеных webrtc
      serverUrl: '',
      isDevUrl: false,
      isAudioPlayATSender: 0,
      auPlayMenuText: '',
      auPlayMenuIcon: '',
      auPlayMenuType: '',
      typeBrowser: '',

      //--------------------------------
      isPanelChik: false, // Отображать ли панель чек-листов
      //--------------------------------

      spanLang: {
        'RU': `<span style="height: 11px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-ru"></span>`,
        'RU_22': `<span style="height: 22px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-ru"></span>`,
        'EN': `<span style="height: 11px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-gb"></span>`,
        'EN_22': `<span style="height: 22px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-gb"></span>`,
      },

      isStartMic: true, //статус микрофона в начале звонка
      isStartVideo: true, //статус видео в начале звонка
      mediaRecorder: null, // проигрователь полученого аудио перевода
      voiceBlob: null, // блоб аудио для перевода
      userLang: 'RU',
      isTranslateFunc: true,
      soundAudioATplay: null,
      senderIdStopATaudio: '',
      isChikAccess: false, // Доступ к интерфейсу чек-листов
      isDevGC: false,
      isDevGCPanel: false,
      isRecordingStarted: false,
      isStoppedRecording: false,
      numUsersMaxMic: 0,
    }
  },

  watch: {
    // isMicroWithSpaceBar:{
    //   handler: function () {
    //     if(this.isMicroWithSpaceBar){
    //       console.log('RoomXrCallWin isMicroWithSpaceBar true')
    //       console.log($('.btnFooter1'), 'RoomXR')
    //     }
    //     else {
    //       console.log('RoomXrCallWin isMicroWithSpaceBar false')
    //       console.log($('.btnFooter1'), 'RoomXR')
    //     }
    //   }
    // },
    isMirror: {
      handler: function () {
        if (this.isMirror) {
          $('#video_' + this.senderId).addClass('clVideoMirror')
        } else {
          $('#video_' + this.senderId).removeClass('clVideoMirror')
        }
      },
    },
    hasCamera: {
      handler: function () {
        This.setIsVideoStreamHasCamera(This.hasCamera)
      },
    },
    isTranslation: {
      handler: function () {
        // if(This.isTranslation){
        //   $('#mainRoom').css('height', 'calc(100% - 200px)')
        // }else{
        //   $('#mainRoom').css('height', '100%')
        // }
      },
    },
  },

  created() {
    this.isChikAccess = (this.$root.hasPriv(cst.AUTH_CHIK_MODULE) && this.$root.hasPriv(cst.AUTH_CHIK_EDIT))
  },

  mounted() {
    This = this
    This = this
    stream.calls = {}

    This.isAudioPlayATSender = localStorage['gc.isAudioPlayATSender']

    this.isPanelShow = this.getStorage();
    console.log(this.isPanelShow, 'this.isPanelShow in mounted')

    setTimeout(() => {
      console.log('зашел в таймаут')
      document.addEventListener("keydown", this.handleKeyMicro)
      document.addEventListener('keyup', this.handleKeyUpMicro)
      document.addEventListener('fullscreenchange', this.fullScreenChange)

      // document.addEventListener("keydown", function (e) {
      //   if(e.code == 'Escape'){
      //     console.log('нажали эскейп')
      //     This.isFullScreen = false
      //     This.$emit('setFullScreen', This.isFullScreen);
      //   }

      // })
    }, 1000)
  },

  methods: {
    //region Начало подключения-------------------------------------------------------------------------------------
    // Иницинализация окна
    init(_dev, _ws, _senderName, _hasCamera, _typeBrowser, _isTranslateFunc, _isDevGC, _videoValue, _audioInputValue, _audioOutputValue, _S_OLD_TREAM_VIDEO_FON, _STREAM_AUDIO, serverUrl, isDevUrl) {
      console.log('RoomXRCallWin: init(_dev, _senderName, _hasCamera, serverUrl, isDevUrl, _typeBrowser)',
          _dev, _senderName, _hasCamera, serverUrl, isDevUrl, _typeBrowser)

      if (this.getLocalStorage('gc.isTranslation') == '1') {
        This.isTranslation = true
      } else {
        This.isTranslation = false
      }
      this.typeBrowser = _typeBrowser
      this.isDeveloper = _dev
      this.isDevGC = _isDevGC

      console.log('roomXR 1 This.isAudioPlayATSender ', This.isAudioPlayATSender, This.auPlayMenuText, This.auPlayMenuType)
      if (this.isAudioPlayATSender == 0) {
        this.auPlayMenuText = this.$t('gc.group.menu.10')
        this.auPlayMenuIcon = 'fas fa-volume-mute'
        this.auPlayMenuType = 10
      } else {
        this.auPlayMenuText = this.$t('gc.group.menu.11')
        this.auPlayMenuIcon = 'fas fa-volume-down'
        this.auPlayMenuType = 11
      }
      console.log('roomXR 2 This.isAudioPlayATSender ', This.isAudioPlayATSender, This.auPlayMenuText, This.auPlayMenuType)

      this.hasCamera = _hasCamera;

      This.serverUrl = serverUrl
      This.isDevUrl = isDevUrl

      if (this.isFirefox) {
        this.itemsMoreWin = [
          {text: this.$t('gc.group.menu.1'), icon: 'fa-expand-arrows-alt', type: 1},
          // { text: 'Настройки', icon: 'fa-sliders-h', type: 2 },
          {text: this.$t('gc.group.menu.3'), icon: 'fa-microphone-slash', type: 3},

        ]
      } else if (this.isUserLic) {
        this.itemsMoreWin = [
          {text: this.$t('gc.group.menu.1'), icon: 'fa-expand-arrows-alt', type: 1},
          {text: this.$t('gc.group.menu.2'), icon: 'fa-sliders-h', type: 2},
          {text: this.$t('gc.group.menu.3'), icon: 'fa-microphone-slash', type: 3},
          {text: this.$t('gc.group.menu.4'), icon: 'fas fa-record-vinyl', type: 4}
        ]
      } else {
        this.itemsMoreWin = [
          {text: this.$t('gc.group.menu.1'), icon: 'fa-expand-arrows-alt', type: 1},
          {text: this.$t('gc.group.menu.2'), icon: 'fa-sliders-h', type: 2},
          {text: this.$t('gc.group.menu.3'), icon: 'fa-microphone-slash', type: 3},
        ]
      }

      This.isTranslateFunc = _isTranslateFunc

      if (This.isTranslateFunc) {
        if (this.getLocalStorage('gc.isTranslation') == '1') {
          This.isTranslation = true
        } else {
          This.isTranslation = false
        }
        this.$emit('setTranslation', this.isTranslation)
        if (This.isTranslation) {
          this.itemsMoreWin = [...this.itemsMoreWin, {
            text: this.$t('gc.group.menu.72'),
            icon: 'fas fa-language',
            type: 7
          }]
          if (this.isDeveloper) {
            this.itemsMoreWin = [...this.itemsMoreWin, {text: this.$t('gc.group.menu.5'), icon: 'fab fa-dev', type: 5}]
          }
          this.itemsMoreWin = [...this.itemsMoreWin, {
            text: This.auPlayMenuText,
            icon: This.auPlayMenuIcon,
            type: This.auPlayMenuType
          }]
        } else {
          this.itemsMoreWin = [...this.itemsMoreWin, {
            text: this.$t('gc.group.menu.7'),
            icon: 'fas fa-language',
            type: 7
          }]
          if (this.isDeveloper) {
            this.itemsMoreWin = [...this.itemsMoreWin, {text: this.$t('gc.group.menu.5'), icon: 'fab fa-dev', type: 5}]
          }
        }
      } else {
        if (this.isDeveloper) {
          this.itemsMoreWin = [...this.itemsMoreWin, {text: this.$t('gc.group.menu.5'), icon: 'fab fa-dev', type: 5}]
        }
      }

      this.maketvideo1 = this.$t('gc.func.btn.maketvideo1')
      this.maketvideo2 = this.$t('gc.func.btn.maketvideo2')
      this.maketvideo3 = this.$t('gc.func.btn.maketvideo3')
      this.senderName = _senderName
      this.senderNameReplace = _senderName.replace(/ /g, '')
      this.ws = _ws
      //console.log(_S_OLD_TREAM_VIDEO_FON)

      console.log('AUDIO init this.audioInputValue = ',_audioInputValue)
      this.audioInputValue = _audioInputValue
      this.audioOutputValue = _audioOutputValue
      this.videoValue = _videoValue

      S_OLD_TREAM_VIDEO_FON = _S_OLD_TREAM_VIDEO_FON
      STREAM_AUDIO = _STREAM_AUDIO

      if (!STREAM_AUDIO) {
        var constraints = {
          audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
          video: false,
        }
        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
            STREAM_AUDIO = stream
            console.log('STREAM_AUDIO')
            console.log(STREAM_AUDIO)

            // tmp
            console.log('navigator.mediaDevices', navigator.mediaDevices, navigator.mediaDevices.getUserMedia({audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]}}))
            navigator.mediaDevices.enumerateDevices()
                .then(function (devices) {
                  devices.forEach(function (device) {
                    console.log('___ device (kind, label, devicedId): ', device.kind, device.label, device.deviceId)
                  });
                });

          })
        }
      }

      var oldData = []

      Vue.directive('click-outside', {
        bind: function (el, binding, vnode) {
          this.event = function (event) {
            if (!(el == event.target || el.contains(event.target))) {
              vnode.context[binding.expression](event);
            }
          };
          document.body.addEventListener('click', this.event)
        },
        unbind: function (el) {
          document.body.removeEventListener('click', this.event)
        },
      });
    },

    // событие, подключение к ГЗ
    onNewParticipant(request) {
      console.log('prId onNewParticipant request', request)
      let __hasCamera;
      __hasCamera = request.hasCamera;

      This.dataUserRecall[request.senderId] = request
      this.receiveVideo(request.senderName, request.bodyPix, request.isMic,
          request.isVideo,
          false, false, __hasCamera, request.avatar,
          request.senderId, request.orient, request.delta, request.prId, request.resultId, request.stepId, request.fileId);
    },

    // событие, получение данных для соединения
    receiveVideoResponse(result) {

      console.log('Получили данные для соединения result = ', result)

      stream.calls[result.senderId].peer.processAnswer(result.sdpAnswer, function (error) {
        if (error) return console.error(error);
      });
    },

    // сбоытие, есть такой пользователь или ошибка при подключении
    callResponse(message) {
      if (message.response != 'accepted') {
        console.info('Call not accepted by peer. Closing call');
        stop();
      } else {
        webRtcPeer.processAnswer(message.sdpAnswer, function (error) {
          if (error) return console.error(error);
        });
      }
    },

    reconnectUser(name) {
      // console.log('Алгоритм переподключения пользователя '+name)
      // var letName = null
      // console.log('dataUserRecall ', this.dataUserRecall)
      //
      // // This.dataUserRecall.forEach(function (item, i, arr) {
      // //   if(item.name === name){
      // //     letName = item
      // //     console.log('letName ', letName)
      // //   }
      // // });
      // console.log('Отключили подключение')
      // This.onParticipantLeftName(name)
      //
      // setTimeout(()=>{
      //   console.log('Пробуем подключиться повторно')
      //   var letName = This.dataUserRecall[name]
      //   console.log('letName ', letName)
      //   //console.log('del Пробуем подключиться повторно ',letName)
      //   This.receiveVideo(letName.senderName, letName.isBodyPix, letName.isMIc, letName.isVideo, letName.isFuncOper, letName.isOperFuncOper, letName.avatar, letName.senderId, letName.orient, letName.delta)
      // }, 500)


      // var participant = stream.calls[''+name];
      // participant.peer.peerConnection.close()
      // participant.peer.dispose();
      // try {
      //   participant.dispose();
      // } catch (e) {
      // }
      // console.log('Отключили подключение')
      // var senderId = name.replace(/ /g, '')
      //
      // setTimeout(() => {
      //   console.log('Пробуем подключиться повторно')
      //
      //   var options = {
      //     remoteVideo: $('#video_' + senderId)[0],
      //     name: name,
      //     onicecandidate: This.onIceCandidate
      //   }
      //
      //   This.attachSinkId($('#video_' + senderId)[0], this.audioOutputValue)
      //
      //   let self = this
      //
      //   participant.peer = new this.ku.WebRtcPeer.WebRtcPeerRecvonly(options,
      //     function (error) {
      //       if (error) {
      //         participant.new = false
      //
      //         return console.error(error);
      //       }
      //
      //       console.log('Переподключение к webrtc от '+name)
      //
      //       participant.ofTEST = self.offerToRecconnect;
      //       participant.ofOPTION = options;
      //
      //       this.generateOffer(participant.ofTEST, name);
      //
      //     });
      //   ;
      // }, 500)
    },

    setStorage(val){
      localStorage.setItem('isPanelShow', val)
      console.log('localStorage.getItem(\'isPanelShow\')', localStorage.getItem('isPanelShow'))
    },

    getStorage(){
      return JSON.parse(localStorage.getItem('isPanelShow'))
    },

    updateStorage(){
      let storedShowPanel = this.getStorage()
      console.log('storedShowPanel', storedShowPanel)

      console.log(this.isPanelShow, 'this.isPanelShow from updateStorage')

      storedShowPanel = JSON.parse(JSON.stringify(this.isPanelShow))
      this.setStorage(storedShowPanel)
    },

    fullScreenChange(event){
      if(document.fullscreenElement){

        This.isFullScreen = true
        this.$emit('isFullScreen', This.isFullScreen);

        this.showHideButtonsPanel()

        console.log('document.fullscreenElement', true)
      } else {

        This.isFullScreen = false

        document.querySelector('.lowButtonsPanel').style.visibility = 'visible';
        This.isPanelShow = false

        this.$emit('isFullScreen', This.isFullScreen);

        console.log('document.fullscreenElement', false)
      }
    },

    handleKeyMicro(event) {
      if(event.code === 'F11'){
        event.preventDefault()
        document.documentElement.requestFullscreen();
        console.log('document.fullscreenElement', document.fullscreenElement)
      }

      if(document.activeElement.id !== 'idTextAreaMess'
          && document.activeElement.tagName.toLowerCase() !== 'textarea'
          && document.activeElement.tagName.toLowerCase() !== 'input'
          && document.activeElement.type !== 'text'
          && document.activeElement.type !== 'password'
          && !document.activeElement.classList.contains('textareaForText')) {
        //блок когда не выбрано по пробелу
        if (event.ctrlKey && event.code === "KeyM") {
          if (!this.wasKeyUp)
            return
          this.wasKeyUp = false
          console.log('микрофон при нажатии ctrlM', this.isStreamMic)
          this.setIsMic()
        } else if (event.code === "KeyM" && !event.ctrlKey) {
          if (!this.wasKeyUp)
            return
          console.log('нажали просто m')
          console.log('this.wasKeyUp', this.wasKeyUp)
          this.wasKeyUp = false
          this.setIsMic()
        }

        if (this.isMicroWithSpaceBar) {
          if (event.key === " ") {
            console.log('this.isStreamMic from main else', this.isStreamMic)
            console.log('this.isSpaceDown from main else', this.isSpaceDown)
            if (this.isStreamMic || this.isSpaceDown)
              return;

            this.isSpaceDown = true
            console.log('_нажали пробел_')
            document.activeElement.blur()
            this.setIsMic()
          }
        }
      }
    },

    handleKeyUpMicro(event) {
      if(document.activeElement.id !== 'idTextAreaMess'
          && document.activeElement.tagName.toLowerCase() !== 'textarea'
          && document.activeElement.type !== 'text'
          && document.activeElement.type !== 'password'
          && document.activeElement.tagName.toLowerCase() !== 'input'
          && !document.activeElement.classList.contains('textareaForText')) {

        if (document.activeElement.id !== 'idTextAreaMess')
          document.activeElement.blur()

        if (event.code === "KeyM" && !event.ctrlKey) {
          this.wasKeyUp = true
          // this.setIsMic()
          console.log('_отпустили клавишу ь_')
        }
        if (event.ctrlKey && event.code === "KeyM") {
          this.wasKeyUp = true
          // this.setIsMic()
          console.log('_отпустили клавишу ь + ctrlKey_')
        }
        if (this.isMicroWithSpaceBar) {
          if (event.key === " ") {
            if (this.isSpaceDown) {
              console.log('_отпустили пробел_')
              this.isSpaceDown = false
              this.setIsMic()
            }
          }
        }
      }


    },
    /**
     * КОНЕЦ ИЗМЕНЕНИЙ **/
    //endregion

    //region OnOffer пользователя-------------------------------------------------------------------------------------
    offerToRecconnect(context,
                      error,
                      offerSdp,
                      name) {

      stream.calls[name].new = false

      if (error) {
        return console.error("sdp offer error")
      }

      var msg = {
        id: "receiveVideoFrom",
        sender: name,
        sdpOffer: offerSdp
      };
      this.sendMessage(msg);

      // логика добавления аудио дорожек для сохранения ГЗ
      if (STREAM_AUDIO_SAVE == null) {
        STREAM_AUDIO_SAVE = STREAM_AUDIO;
      }

      setTimeout(() => {
        let senders = stream.calls[name].peer.peerConnection.getReceivers();

        console.log('Переподключение к webrtc на getReceivers ' + name)
        let d = new Date;
        stream.calls[name].dateConnect = d.getTime();

        this.$refs.refDevPanel.getStats(name, stream.calls[name].peer.peerConnection)

        stream.calls[name].peer.peerConnection.getStats().then(function (reports) {

          setTimeout(() => {
            console.log('__Подключение__________________________________________________________________')
            reports.forEach(function (stat) {
              if (stat.id === 'RTCMediaStream_default') {
                console.log('STREAM полученный от сервера ', stat)
                stat.trackIds.forEach(function (trackId) {
                  reports.forEach(function (statTrack) {
                    if (statTrack.id === trackId) {
                      // console.log('track def ',statTrack )
                      if (statTrack.kind === 'audio') {
                        var jus = statTrack.jitterBufferDelay / statTrack.jitterBufferEmittedCount
                        console.log('Полученный track audio, id = ' + statTrack.id + ' ended ' + statTrack.ended + ', потеряно пакетов ' + statTrack.delayedPacketOutageSamples + ', задержка пакетов ' + jus)
                        stream.calls[name].idTrackAudio = statTrack.id
                      } else {
                        var jus = statTrack.jitterBufferDelay / statTrack.jitterBufferEmittedCount
                        console.log('Полученный track video, id = ' + statTrack.id + ' ended ' + statTrack.ended + ' framesReceived = ' + statTrack.framesReceived + ' frameWidth = ' + statTrack.frameWidth + ', задержка пакетов ' + jus)
                        stream.calls[name].idTrackVideo = statTrack.id
                      }
                    }
                  })
                })
              }
            })
          }, 0)
        })

        senders.forEach((sender) => {
          try {
            if (sender.track.kind == 'audio') {

              var OutgoingAudioMediaStream = new MediaStream();
              OutgoingAudioMediaStream.addTrack(STREAM_AUDIO_SAVE.getAudioTracks()[0]);

              var IncomingAudioMediaStream = new MediaStream();
              IncomingAudioMediaStream.addTrack(sender.track);

              const audioContext = new AudioContext();

              var audioIn_01 = audioContext.createMediaStreamSource(OutgoingAudioMediaStream);
              var audioIn_02 = audioContext.createMediaStreamSource(IncomingAudioMediaStream);

              var dest = audioContext.createMediaStreamDestination();

              audioIn_01.connect(dest);
              audioIn_02.connect(dest);

              STREAM_AUDIO_SAVE = dest.stream;

              if (This.isSaveCallMy) {
                this.$refs.refSaveVideo.addNewAudioTrack(sender.track)
              }
            }
          } catch (e) {
            console.error('error all audio = ' + e)
            console.error(sender)
          }

        });
      }, 500)
    },

    //endregion-------------------------------------------------------------------------------------

    //region Подключение к сессии и создание своего окна-------------------------------------------------------------------------------------
    onExistingParticipants(msg, _isStartMic, _isStartVideo) {

      console.log('onExistingParticipants Подключение к сессии и создание своего окна ', msg, _isStartMic, _isStartVideo)

      this.senderId = msg.senderId
      This.isStartMic = _isStartMic
      This.isStartVideo = _isStartVideo

      This.numUsersMaxMic = msg.data.length;

      //region Получение аудио и видео
      var message = {
        id: 'setTimeCall',
        room: this.paramValueRoom,
        timeDelta: new Date().getTime()
      }
      this.sendMessage(message);

      var audioSource = This.audioInputValue;
      var videoSource = This.videoValue;

      // this.attachSinkId(videoSource, this.audioOutputValue)

      var constraints = {
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
        video: (videoSource != null) ? {optional: [{sourceId: videoSource}]} : true // false
      }

      //endregion

      // region Создание своего окна
      stream.calls[this.senderId] = this.newCallData()
      console.log(stream.calls)

      var participant = stream.calls[this.senderId];

      participant.senderId = this.senderId
      participant.senderName = this.senderName
      participant.avatar = this.avatar

      stream.calls[this.senderId] = participant;

      var video12 = `<video
                  id="${'video_' + this.senderId}"
                  class="winVideoLocal"
                  autoplay=""  width="320" height="240">`
      var audio12 = `<audio
                  id="${'audio_' + This.senderId}"
                  autoplay controls muted  class="isHideWidth">`
      // по умолчанию - кнопка громкости звука у элемента выключена.
      // Чтобы включить, надо убрать слово muted, но там интересный эффект возникает:
      // при отсутствии muted звук идёт сразу (это нормально), НО он идёт тогда и если выключить, причём в лучшем качестве (?!)

      var divAudioTranslate = `<div id="${'devAudioTranslate1_' + this.senderId}" class="divAudioTranslate">
                                <svg enable-background="new 0 0 512 512" height="120px" version="1.1" viewBox="0 0 512 512" width="120px" xml:space="preserve" ><g>
                                <path d="    M113,170H51.803C43.074,170,36,177.236,36,185.965v137.962C36,332.645,43.074,340,51.803,340H113" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="119" x2="119" y1="186" y2="323"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="172.543" y2="49.319"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="338.936" y2="462.16"/><path d="    M265.58,446.629" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M265.58,65.382" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M253.715,49.311c21.258,0,38.285,17.24,38.285,38.511v336.346c0,21.28-17.146,38.521-38.403,38.521" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="252" x2="252" y1="71" y2="442"/><path d="    M323.125,183.086c39.704,0,71.861,32.167,71.861,71.859s-32.157,71.87-71.861,71.87" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M359.066,138.013c64.574,0,116.934,52.346,116.934,116.933c0,64.585-52.359,116.944-116.934,116.944" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
                                <ellipse ry="250" rx="250" cy="250" cx="250" stroke="#000" fill="#c07819" class="blinkAudoiAT"/>
                                </g></svg>
                            </div>`

      var spanTextATInfo = `<div id="${'spanStatusTranslate_' + this.senderId}" class="spanSTra">
                                <span id="${'spanStatusTranslateText_' + this.senderId}" class="spanSTraText"></span>
                            </div>`

      $('#idDivLocalVideo').append(video12);
      $('#idDivLocalVideo').append(audio12);
      $('#idDivLocalVideo').append(divAudioTranslate);
      $('#idDivLocalVideo').append(spanTextATInfo);

      $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
      $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
      $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
      $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

      $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
      $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
      $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
      $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')

      var nameUC = this.setNameUC(this.senderName)

      var videoAdd = `<div id="${'devVideo_' + this.senderId}" class="winVideoRoom1">
                            <div id="${'devDevIconNoVideoMain'}" class="devDevIconNoVideo">
                               <div id="${'devAudioTranslate_' + this.senderId}" class="divAudioTranslateLoc">
                                <svg enable-background="new 0 0 512 512" height="120px" version="1.1" viewBox="0 0 512 512" width="120px" xml:space="preserve" ><g>
                                <path d="    M113,170H51.803C43.074,170,36,177.236,36,185.965v137.962C36,332.645,43.074,340,51.803,340H113" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="119" x2="119" y1="186" y2="323"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="172.543" y2="49.319"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="338.936" y2="462.16"/><path d="    M265.58,446.629" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M265.58,65.382" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M253.715,49.311c21.258,0,38.285,17.24,38.285,38.511v336.346c0,21.28-17.146,38.521-38.403,38.521" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="252" x2="252" y1="71" y2="442"/><path d="    M323.125,183.086c39.704,0,71.861,32.167,71.861,71.859s-32.157,71.87-71.861,71.87" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M359.066,138.013c64.574,0,116.934,52.346,116.934,116.933c0,64.585-52.359,116.944-116.934,116.944" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
                                <ellipse ry="250" rx="250" cy="250" cx="250" stroke="#000" fill="#c07819" class="blinkAudoiAT"/>
                                </g></svg>
                            </div>
                              <img id="${'imgDevIconNoVideoMain'}" class="imgDevIconNoVideoTextDev">
                              <div id="${'noImgDevIconNoVideoMain'}" class="devDevIconNoVideoTextDev">${nameUC}</div>
                          </div>`
      $('#participants').append(videoAdd);
      $('#devVideo_' + this.senderId).hide()

      participant.video = video12
      participant.audio = audio12

      $('#devAudioTranslate1_' + this.senderId).hide()
      $('#spanStatusTranslate_' + this.senderId).hide()
      $('#devAudioTranslate_' + this.senderId).hide()
      //endregion

      //region События для элементов окна

      if (this.isMirror) {
        $('#video_' + this.senderId).addClass('clVideoMirror')
      } else {
        $('#video_' + this.senderId).removeClass('clVideoMirror')
      }

      $('#idDivLocalVideo').on('click', function () {
        if (!this.isAddToAll) {

        } else if (isOneAdd) {
          isOneAdd = false
        } else {
          This.isMyFullVideo = !This.isMyFullVideo

          if (This.isMyFullVideo) {

            // $('#idDivLocalVideo').remove(video12);
            $('#idDivLocalVideo').removeClass('iddivlocalvideo')
            $('#idDivLocalVideo').addClass('iddivlocalvideo2')
            console.log('is100 3')
            $('#idnewMyVideoClass').css('width', '100%')
            $('#idnewMyVideoClass').append($('#idDivLocalVideo'));

          } else {

            $('#idDivLocalVideo').removeClass('iddivlocalvideo2')
            $('#idDivLocalVideo').addClass('iddivlocalvideo')
            console.log('is100 4')
            $('#idnewMyVideoClass').css('width', '100%')
            $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
            // $('#participants').remove($('#devVideo_Opermain'));
          }

          this.$emit('updateSettUser')

          setTimeout(() => {
            This.resizeWin(2)
          }, 1000)
        }
      });

      // у своего элемента audio включить динамик и установить звук, после чего скрыть его (через установку свойства hidden)
      setTimeout(() => {
        var el = document.getElementById('audio_' + This.senderId);
        setTimeout(() => {
          el.volume = 1.0;
          el.muted = false;
          el.setAttribute("hidden", "hidden");
          console.log('el__', audio12, $('#audio_' + This.senderId), el);
        }, 50);
      }, 100);

      //endregion

      //region Подключение к webRtc
      var options = null

      // if (This.isDegradation) {
      //
      //   //console.log('_ _STREAM_AUDIO')
      //   //console.log(STREAM_AUDIO)
      //
      //   $(`#video_` + This.senderId)[0].srcObject = S_OLD_TREAM_VIDEO_FON;
      //
      //   if (!STREAM_AUDIO) {
      //     var constraints = {
      //       audio: {optional: [{sourceId: this.audioInputValue}]},
      //       video: false,
      //     }
      //     if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      //       navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
      //         STREAM_AUDIO = stream
      //         let audioTrack = null;
      //         for (const track of STREAM_AUDIO.getTracks()) {
      //           audioTrack = track
      //         }
      //         if (S_OLD_TREAM_VIDEO_FON) {
      //           //console.log('S_OLD_TREAM_VIDEO_FON')
      //           S_OLD_TREAM_VIDEO_FON.addTrack(audioTrack)
      //           options = {
      //             localVideo: $('#video_' + This.senderId)[0],
      //             // videoStream: screenStream,
      //             videoStream: S_OLD_TREAM_VIDEO_FON,
      //             senderId: This.senderId,
      //             onicecandidate: This.onIceCandidate
      //           }
      //         } else {
      //           options = {
      //             localVideo: $('#video_' + This.senderId)[0],
      //             senderId: This.senderId,
      //             mediaConstraints: constraints,
      //             onicecandidate: This.onIceCandidate
      //           }
      //         }
      //       })
      //     }
      //   }
      //   else {
      //     let audioTrack = null;
      //     for (const track of STREAM_AUDIO.getTracks()) {
      //       audioTrack = track
      //     }
      //     if (S_OLD_TREAM_VIDEO_FON) {
      //       //console.log('S_OLD_TREAM_VIDEO_FON')
      //       S_OLD_TREAM_VIDEO_FON.addTrack(audioTrack)
      //       options = {
      //         localVideo: $('#video_' + This.senderId)[0],
      //         // videoStream: screenStream,
      //         videoStream: S_OLD_TREAM_VIDEO_FON,
      //         senderId: This.senderId,
      //         onicecandidate: This.onIceCandidate
      //       }
      //     } else {
      //       options = {
      //         localVideo: $('#video_' + This.senderId)[0],
      //         senderId: This.senderId,
      //         mediaConstraints: constraints,
      //         onicecandidate: This.onIceCandidate
      //       }
      //     }
      //   }
      //
      //
      // }
      // else {
      //   options = {
      //     localVideo: $('#video_' + this.senderId)[0],
      //     senderName: this.senderName,
      //     senderId: this.senderId,
      //     mediaConstraints: constraints,
      //     onicecandidate: this.onIceCandidate,
      //     dataChannels: true
      //   }
      // }

      console.log('video: msg.hasCamera ',msg.hasCamera)
      console.log('This.hasCamera ',This.hasCamera)
      console.log('This.hasMic ',This.hasMic)
      console.log('This.audioInputValue ',This.audioInputValue)

      var constraintsNoVideo
      if(This.hasMic){
        constraintsNoVideo  = {
          audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
          video: false,
        }
      }else{
        constraintsNoVideo  = {
          audio: false,
          video: true,
        }
      }

      console.log('constraintsNoVideo',constraintsNoVideo)

      navigator.mediaDevices.getUserMedia(constraintsNoVideo)
          .then(screenStream => {
            // console.log('TA $(\'#video_\' + This.senderId)[0]', $('#video_' + This.senderId)[0]);
            // console.log('TA screenStream', screenStream);
            // console.log('TA $(\'#audio_\' + This.senderId)[0]', $('#audio_' + This.senderId)[0]);
            // console.log('This.hasCamera', This.hasCamera);

            if (This.hasCamera && This.hasMic) {
              console.log("iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly есть камера и микрофон")
              options = {
                localVideo: $('#video_' + This.senderId)[0],
                name: This.senderId,
                mediaConstraints: constraints,
                onicecandidate: this.onIceCandidate
              }
            } else if(!This.hasCamera && This.hasMic) {
              console.log("iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly нет камеры и есть микрофон")
              options = {
                videoStream: screenStream,
                name: This.senderId,
                mediaConstraints: constraintsNoVideo,
                onicecandidate: this.onIceCandidate
              }
            } else if(This.hasCamera && !This.hasMic){
              console.log("iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly есть камера и нет микрофона")
              var constraintsNoAudio = {
                audio: false,
                video: (videoSource != null) ? {optional: [{sourceId: videoSource}]} : true // false
              }
              options = {
                localVideo: $('#video_' + This.senderId)[0],
                name: This.senderId,
                mediaConstraints: constraintsNoAudio,
                onicecandidate: this.onIceCandidate
              }
            } else {
              console.log("iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly нет камеры и микрофона")
              var constraintsNoAudioVideo = {
                audio: false,
                video: false
              }
              options = {
                videoStream: screenStream,
                name: This.senderId,
                mediaConstraints: constraintsNoAudioVideo,
                onicecandidate: this.onIceCandidate
              }
            }

            console.log('SS options ', options)
            console.log('SS senderId ' + This.senderId + ' This.paramValueRoom ' + This.paramValueRoom)
            console.log('iksarWebrtc.WebRtcPeer WebRtcPeerSendonly')

            let self = this
            participant.peer = new this.iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly(options,
                function (error) {
                  if (error) {
                    return console.error(error);
                  }
                  participant.ofTEST = self.onOfferCall;
                  participant.ofOPTION = options;
                  participant.stopvideo = true
                  // this.generateOffer(self.onOfferCall);
                  this.generateOffer(participant.ofTEST, self.senderId);

                });
          })

      // console.log("HAS_CAMERA This.hasCamera= "+This.hasCamera)
      // if(This.hasCamera){
      //   options = {
      //     localVideo: $('#video_' + this.senderId)[0],
      //     senderName: this.senderName,
      //     senderId: this.senderId,
      //     mediaConstraints: constraints,
      //     onicecandidate: this.onIceCandidate,
      //     dataChannels: true
      //   }
      //   let self = this
      //   participant.peer = new this.ku.WebRtcPeer.WebRtcPeerSendonly(options,
      //     function (error) {
      //       if (error) {
      //         return console.error('Ошибка подключения к webrtc e = '+error);
      //       }
      //
      //       participant.ofTEST = self.onOfferCall;
      //       participant.ofOPTION = options;
      //       participant.stopvideo = true
      //       // this.generateOffer(self.onOfferCall);
      //       this.generateOffer(participant.ofTEST, self.senderId);
      //
      //     });
      // }else{
      //   // нет камеры
      //   navigator.mediaDevices.getUserMedia({
      //     video: msg.hasCamera,
      //     audio: true
      //   })
      //     .then(screenStream => {
      //       console.log('$(\'#video_\' + This.senderId)[0]', $('#video_' + This.senderId)[0], $('#video_' + This.senderId));
      //       console.log('screenStream', screenStream);
      //       console.log('$(\'#audio_\' + This.senderId)[0]', $('#audio_' + This.senderId)[0], $('#audio_' + This.senderId));
      //       console.log('msg.hasCamera', msg.hasCamera, msg);
      //       console.log('This.hasCamera', This.hasCamera);
      //
      //         options = {
      //           videoStream: screenStream,
      //           localVideo: $('#audio_' + This.senderId)[0],
      //           name: This.senderId,
      //           mediaConstraints: constraints,
      //           onicecandidate: this.onIceCandidate
      //         }
      //
      //       let self = this
      //       participant.peer = new this.ku.WebRtcPeer.WebRtcPeerSendonly(options,
      //         function (error) {
      //           if (error) {
      //             return console.error('Ошибка подключения к webrtc e = '+error);
      //           }
      //
      //           participant.ofTEST = self.onOfferCall;
      //           participant.ofOPTION = options;
      //           participant.stopvideo = true
      //           // this.generateOffer(self.onOfferCall);
      //           this.generateOffer(participant.ofTEST, self.senderId);
      //
      //         });
      //     })
      // }

      // navigator.mediaDevices.getUserMedia({
      //   video: msg.hasCamera, //(msg.userType.substring(0, 6) == 'userON'), //true, /// ??? почему имя пользователя в userType ?
      //   // video: (msg.senderName.substring(0, 6) == 'userON'), //true,
      //   //video: (this.videoValue != null), //false, //(this.videoValue != null),
      //   audio: true
      // })
      //   .then(screenStream => {
      //   console.log('$(\'#video_\' + This.senderId)[0]', $('#video_' + This.senderId)[0], $('#video_' + This.senderId));
      //   console.log('screenStream', screenStream);
      //   console.log('$(\'#audio_\' + This.senderId)[0]', $('#audio_' + This.senderId)[0], $('#audio_' + This.senderId));
      //   console.log('msg.hasCamera', msg.hasCamera, msg);
      //   console.log('This.hasCamera', This.hasCamera);
      //
      //   if (This.hasCamera) {
      //     // if (msg.hasCamera) { // это точно неправильно
      //     // if (This.senderName.substring(0, 6) == 'userON') {
      //     // if (This.hasCamera) {
      //     // if (this.videoValue != null) {
      //     // есть видео
      //     options = {
      //       //videoStream: screenStream,
      //       localVideo: $('#video_' + This.senderId)[0],
      //       name: This.senderId,
      //       mediaConstraints: constraints,
      //       onicecandidate: this.onIceCandidate
      //     }
      //   } else {
      //     // нет видео
      //     options = {
      //       videoStream: screenStream,
      //       localVideo: $('#audio_' + This.senderId)[0], // audio12 + senderId
      //       // videoStream: $('#audioOutput'),
      //       name: This.senderId,
      //       mediaConstraints: constraints,
      //       onicecandidate: this.onIceCandidate
      //     }
      //   }
      //
      //   console.log('SS options ', options)
      //   console.log('SS senderId '+ This.senderId +' roomID ' + This.roomID)
      //
      //   let self = this
      //   participant.peer = new this.ku.WebRtcPeer.WebRtcPeerSendonly(options,
      //     function (error) {
      //       if (error) {
      //         return console.error('Ошибка подключения к webrtc e = ' + error);
      //       }
      //
      //       participant.ofTEST = self.onOfferCall;
      //       participant.ofOPTION = options;
      //       participant.stopvideo = true
      //       // this.generateOffer(self.onOfferCall);
      //       this.generateOffer(participant.ofTEST, self.senderId);
      //
      //     });
      //
      // })

      setTimeout(() => {
        // //console.log(participant.peer)
        // //console.log(participant.peer.peerConnection)

        try {
          const senderPar = participant.peer.peerConnection.getSenders()[0];
          // //console.log(senderPar)
          const parameters = senderPar.getParameters();//participant.peerConnection.getLocalStreams();
          if (!parameters.encodings) {
            parameters.encodings = [{}];
          }

          // parameters.encodings[0].maxBitrate = 500 * 1000;
          console.log("изменили параметр maxBitrate на 10000")
          parameters.encodings[0].maxBitrate = 1000;

          senderPar.setParameters(parameters).catch(e => console.error(e));
        } catch (e) {
        }
      }, 3000)

      //endregion

      // region Подключение остальных участников
      //console.log('123')
      //console.log(msg.data)
      var urlRest = This.wsURL.replace('wss://', '').replace('wss//', '').replace('ws://', '').replace('ws//', '')

      var hostname = window.location.host;
      console.log("host "+hostname)
      if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
        urlRest = window.location.protocol+'//'+window.location.host
        console.log("location.hostname "+hostname)
      }else if (location.host == 'localhost:8080') {
        urlRest = ''
      }else{
        urlRest = 'https://' + urlRest
      }

      msg.data.forEach(function (item, i, arr) {
        console.log('This.receiveVideo', item)

        This.dataUserRecall[item.senderId] = item

        let __hasCamera
        __hasCamera = item.hasCamera;

        console.log('item.avatar ' + urlRest + item.avatar)

        This.$root.restGet(urlRest + item.avatar)
            .then((res) => {
              console.log('getAvatarGC res', res)
              This.receiveVideo(item.senderName, item.isBodyPix, item.isMIc, item.isVideo, item.isFuncOper,
                  item.isOperFuncOper, __hasCamera, res.avatar, item.senderId, item.orient, item.delta,
                  item.prId, item.resultId, item.stepId, item.fileId)
            }).catch((error) => {
          console.log('ошибка аватар getAvatarGC res', error)
          This.receiveVideo(item.senderName, item.isBodyPix, item.isMIc, item.isVideo, item.isFuncOper,
              item.isOperFuncOper, __hasCamera, null, item.senderId, item.orient, item.delta,
              item.prId, item.resultId, item.stepId, item.fileId)
        });
      });
      This.resizeWin(3)
      // endregion
    },
    //endregion-------------------------------------------------------------------------------------

    //region OnOffer и IceCandidate своего подключения-------------------------------------------------------------------------------------

    onOfferCall(context, error, offerSdp, senderId) {
      if (error) {
        return
      }

      console.log('Подключаемся к группе onOfferCall ')

      offerSdp = this.setCodec(offerSdp);

      try {
        console.log('offerSdp 1 ', offerSdp)
      } catch (e) {

      }

      if (offerSdp) {
        var arr = offerSdp.split('\r\n');
        // arr.forEach((str, i) => {
        //   if (/^a=fmtp:\d*/.test(str)) {
        //     arr[i] = str + ';x-google-max-bitrate=500;x-google-min-bitrate=0;x-google-start-bitrate=500';
        //   } else if (/^a=mid:(1|video)/.test(str)) {
        //     arr[i] += '\r\nb=AS:10000';
        //   }
        // });

        offerSdp = arr.join('\r\n'),
            this.sendMessage(
                {
                  id: 'receiveVideoFrom',
                  senderId: senderId,
                  senderName: senderId,
                  //id: this.call_message,
                  from: context.call_from,
                  to: context.call_to,
                  sdpOffer: offerSdp,
                  dev: context.dev,
                  // nik 24.10.2018
                  save_video: context.call_save_video,
                  send_video: context.call_send_video,
                  saved_video_format: context.call_saved_video_format
                }
            );
      }

      setTimeout(() => {
        This.startAudioVolume()
        This.isStartCallT = true
      }, 500)

      var participant = stream.calls[This.senderId].peer
      var media_streams = participant.peerConnection.getLocalStreams();
      var video_tracks = media_streams[0].getVideoTracks();
      if (video_tracks[0]) {
        video_tracks[0].applyConstraints({
          width: 320,
          height: 240,
          frameRate: 10
        });
      }

      if (!This.isStartMic) {
        console.log('mimimic Микрофон убрать в начале')
        This.isStreamMic = true
        this.setIsMic()
      } else if (This.numUsersMaxMic > 7){
        console.log('участников более 8, подключаю без звука')
        This.isStreamMic = true
        this.setIsMic()
      } else {
        console.log('mimimic Микрофон неубрать в начале')
      }

      if (!This.isStartVideo) {
        this.isStreamVideoOld = false
        this.setIsVideoStream()
      }

      if (!This.isFirefox) {
        window.setInterval(() => {
          This.fetatsChrome(participant)
        }, 1000);
      } else {
        This.fetchStatsFirefox(participant)
      }

      this.$refs.refDevPanel.getStatsSend(senderId, participant.peerConnection)
      // setTimeout(run, 1000);

      // This.initStream()
    },

    setCodec(sdp_offer) {
      return sdp_offer;

      var video_start = sdp_offer.indexOf('m=video');

      var video_config = sdp_offer.substring(video_start, sdp_offer.indexOf('\r\n', video_start));
      var split_start = (video_config.indexOf('SAVPF') + 6);

      var video_base = video_config.substring(0, split_start);

      var browser_name = stream.utility.getBrowserName();

      switch (browser_name) {
        case 'chrome':
          video_base += '100 101 102 123 127 122 125 107 108 109 124';

          break;

        case 'firefox':
          video_base += '126 97';

          break;

        default:
          return sdp_offer;

          break;
      }

      sdp_offer = sdp_offer.replace(video_config, video_base);

      return sdp_offer;
    },

    onIceCandidate(candidate, c2, c3, c4) {

      if (candidate) {
        var message = {
          id: 'onIceCandidate',
          candidate: candidate,
          name: c2,
        };

        this.sendMessage(message);
      }
    },

    startAudioVolume() {

      var constraints = {
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
        video: false,
      }

      navigator.getUserMedia = navigator.getUserMedia ||
          navigator.webkitGetUserMedia ||
          navigator.mozGetUserMedia;
      if (navigator.getUserMedia) {
        navigator.getUserMedia(constraints,
            function (stream) {

              console.log('AEUDIO startAudioVolume', stream)
              stream.getAudioTracks()[0].addEventListener("unmute", event => {
                console.log('AEUDIO 2 устройство микрофон вкл.')
              }, false);
              stream.getAudioTracks()[0].addEventListener("mute", event => {
                console.log('AEUDIO 2 устройство микрофон выкл')
              }, false);
              stream.getAudioTracks()[0].addEventListener("ended", event => {
                console.log('AEUDIO 2 устройство звука удалили')
                This.newAudioEnded()
              }, false);
              stream.getAudioTracks()[0].addEventListener("isolationchange", event => {
                console.log('AEUDIO 2 521')
              }, false);
              // sender.track.onended = () => navigator.mediaDevices.ondevicechange = tryAgain;

              var audioContext = new AudioContext();
              var analyser = audioContext.createAnalyser();
              var microphone = audioContext.createMediaStreamSource(stream);
              var javascriptNode = audioContext.createScriptProcessor(2048, 1, 1);

              analyser.smoothingTimeConstant = 0.8;
              analyser.fftSize = 1024;

              microphone.connect(analyser);
              analyser.connect(javascriptNode);
              javascriptNode.connect(audioContext.destination);

              javascriptNode.onaudioprocess = function () {
                var array = new Uint8Array(analyser.frequencyBinCount);
                analyser.getByteFrequencyData(array);
                var values = 0;

                var length = array.length;
                for (var i = 0; i < length; i++) {
                  values += (array[i]);
                }

                var average = values / length;
                This.sendVoiseAverage(average)

              } // end fn stream
            },
            function (err) {
              console.error(err)
            });
      } else {
        console.error('error audio')
      }
    },
    //endregion-------------------------------------------------------------------------------------

    //region Добавление нового видео----------------------------------------------------------------------------------
    //Новый код jitsi

    //endregion

    //region old
    receiveVideo(senderName, isBodyPix, isMic, isVideo, isFuncOper, isOperFuncOper, hasCamera, avatar,
                 senderId, orient, delta, prId, resultId, stepId, fileId) {

      console.log('GC receiveVideo ' + senderId, 'hasCamera=', hasCamera)

      this.$emit('addUserChat', senderId, senderName, '/exgc/avatar/' + this.paramValueRoom + '/' + senderId)

      // region Обработка данных и создание participant
      var constraints = {
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
        video: {
          mandatory: {
            maxWidth: 320,
            maxFrameRate: 15,
            minFrameRate: 15
          }
        }
      };

      //console.log('del Добавление нового видео senderName = '+senderName+' senderId '+senderId +' isMic '+isMic)
      var nameUC = this.setNameUC(senderName)

      stream.calls[senderId] = this.newCallData()
      var participant = stream.calls[senderId];

      participant.senderName = senderName
      participant.senderId = senderId
      participant.new = true
      participant.avatar = avatar
      participant.isMic = isMic
      participant.isFuncOper = isFuncOper
      participant.update =0

      participant.orient = orient
      participant.delta = 1.7

      participant.prId = prId
      participant.resultId = resultId
      participant.stepId = stepId
      participant.fileId = fileId

      if (isFuncOper) {
        participant.isFuncOper = isFuncOper

        setTimeout(() => {
          This.setTextMainWin(4, senderName)
        }, 2000)
      }

      participant.isRealFuncOper = isOperFuncOper

      if (isOperFuncOper) {
        setTimeout(() => {
          participant.isFuncOper = isFuncOper

          This.startFuncOperOther(senderId)
        }, 1000)
      }
      participant.full = false
      participant.nameReplase = senderId
      participant.isMainWin = false
      participant.OperFunc = false

      var nameSender = senderName
      var connectText = This.$t('grp-call.connecnt.user')
      participant.scrs = false

      if (senderName.substring(0, 9) == 'Оператор_' || senderName.substring(0, 9) == 'Operator_') {
        participant.OperFunc = true
      }
      else if (senderName.substring(0, 7) == 'scrsXR_') {

        this.typeMaketOld = this.typeMaket
        var keys = Object.keys(stream.calls);
        for (var i = 0; i < keys.length; ++i) {
          var participant_obj = stream.calls['' + keys[i]];
          participant_obj.full = false // на весь экран это окно
          participant_obj.isMainWin = false // это окно закрепить на весь экран
        }

        participant.scrs = true
        nameSender = senderName.substring(7, senderName.length) + this.$t('gc.scrS')

        if (This.isAddToAll) {
          This.isAddToAll = false

          $('#devDevBlockVideo_' + This.senderId).addClass('winVideoRoomDevMy')

          $('#idDivLocalVideo').append($('#devDevBlockVideo_' + This.senderId))
          $('#idDivLocalVideo').append($('#video_' + This.senderId))

          $('#devVideo_' + This.senderId).hide()
          $('#iconUserAddAll_' + This.senderId).removeClass('mdi mdi-grid v-btn_ot_ex_sp v-span-video')
          $('#iconUserAddAll_' + This.senderId).addClass('mdi mdi-grid-off v-btn_ot_ex_sp v-span-video')


          $('#idDivLocalVideo').removeClass('iddivlocalvideo2')
          $('#idDivLocalVideo').addClass('iddivlocalvideo')

          $('#idnewMyVideoClass').css('width', '100%')
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
        }

        This.typeMaket = 2
        This.numScreenSharinWin++
      } else {
        participant.SSFunc = false
        participant.OperFunc = false
      }

      participant.ws = this.ws
      participant.bodyPix = isBodyPix
      participant.isPaint = false

      //endregion

      //region Создание нового окна
      //console.log('del Создание нового окна sessionID = '+senderId)

      var video12 = `<div id="${'devVideo_' + senderId}" class="winVideoRoom1">
                          <div id="${'devDevIconNoVideo_' + senderId}" class="devDevIconNoVideo">

                            <img id="${'imgDevIconNoVideo_' + senderId}" class="imgDevIconNoVideoTextDev">
                            <div id="${'noImgDevIconNoVideo_' + senderId}" class="devDevIconNoVideoTextDev">${nameUC}

                          </div>

<!--                          <span <div id="${'spanNoVideo' + senderId}" class="fa fas fa-video-slash v-btn_ot_stats_sp ma-2" style="color: white; font-size: 60px" aria-hidden="true"></span>-->
</div>

                        <div id="${'devDevBlockVideo_' + senderId}" class="divDevBlockOper">
                            <div id="${'devAudioTranslate_' + senderId}" class="divAudioTranslate">
                                <svg enable-background="new 0 0 512 512" height="120px" version="1.1" viewBox="0 0 512 512" width="120px" xml:space="preserve" ><g>
                                <path d="    M113,170H51.803C43.074,170,36,177.236,36,185.965v137.962C36,332.645,43.074,340,51.803,340H113" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="119" x2="119" y1="186" y2="323"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="172.543" y2="49.319"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="129.206" x2="252.419" y1="338.936" y2="462.16"/><path d="    M265.58,446.629" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M265.58,65.382" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M253.715,49.311c21.258,0,38.285,17.24,38.285,38.511v336.346c0,21.28-17.146,38.521-38.403,38.521" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><line fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8" x1="252" x2="252" y1="71" y2="442"/><path d="    M323.125,183.086c39.704,0,71.861,32.167,71.861,71.859s-32.157,71.87-71.861,71.87" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/><path d="    M359.066,138.013c64.574,0,116.934,52.346,116.934,116.933c0,64.585-52.359,116.944-116.934,116.944" fill="none" stroke="#000000" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" stroke-width="8"/>
                                <ellipse ry="250" rx="250" cy="250" cx="250" stroke="#000" fill="#c07819" class="blinkAudoiAT"/>
                                </g></svg>
                            </div>
                            <div id="${'spanStatusTranslate_' + senderId}" class="spanSTra">
                                <span id="${'spanStatusTranslateText_' + senderId}" class="spanSTraText"></span>
                            </div>
                           <div id="${'devOperFunc_' + senderId}" class="devVideoBlockFunc">
                             <button
                               id="${'devBtnOperFunc_' + senderId}"
                                     title="${This.$t('gc.butWin4')}"
                               class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type5">
                               <i id="${'iconOperFunc_' + senderId}" class="fas fa-ellipsis-v" style="font-size: 14px; color: #636363;"></i>
                               <i id="${'iconOperFunc1_' + senderId}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: #dee1e5; margin-top: 5px;"></i>
                               <i id="${'iconOperFunc2_' + senderId}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: red;"></i>
                             </button>


      </div>
                          <div id="${'devBlockVideo_' + senderId}" class="devVideoBlock1">
                            <button
                             id="${'devBlockVideoBtn1_' + senderId}"
                              title="${This.$t('gc.butWin1')}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type1">
                             <span id="${'iconUserMicVideo_' + senderId}" style="font-size: 14px; color: rgba(68,68,68,0.07);" class="fa fa-microphone v-btn_ot_ex_sp v-span-video"  aria-hidden="true"></span>
                           </button>
                           <button
                             id="${'devBlockVideoBtn3_' + senderId}"
                        title="${This.$t('gc.butWin3')}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type3">

                             <i id="${'iconMainVideo_' + senderId}" class="fa fa-thumbtack" style="font-size: 14px; color: #636363;"></i>
                             <i id="${'iconMainVideo1_' + senderId}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: #dee1e5; margin-top: 5px;"></i>
                             <i id="${'iconMainVideo2_' + senderId}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: red;"></i>
                           </button>
                           <button
                             id="${'devBlockVideoBtn4_' + senderId}"
                             title="${This.$t('gc.butWin5')}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type3">
                             <i id="${'iconMainVideo4_' + senderId}" class="fa fa-expand-arrows-alt" style="font-size: 14px; color: #636363;"></i>
                           </button>
                           <button
                             id="${'devBlockVideoBtn2_' + senderId}"
                             title="${This.$t('gc.butWin2')}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type2">
                             <span  style="font-size: 14px; color: #636363;" class="fa fa-times v-btn_ot_ex_sp v-span-video"  aria-hidden="true"></span>
                           </button>
                          </div>
                        </div>
                        <div id="${'devDevBlockVideoInfo_' + senderId}">
                          <div id="${'devDevBlockConnect_' + senderId}" style="display: none" class="devDevBlockConnectcss">
                           <p class="pdivVideoConndectcss">${connectText}</p>
                          </div>
                          <div class="winVideoRoomInfo row">
                                 <div id="${'micVoiseSaveATflag_' + senderId}" class="multi-rippleDiv"></div>
                                 <div id="${'micVoiseSaveAT_' + senderId}" class="multi-ripple">
                                    <div class="cAT"></div>
                                    <div class="cAT"></div>
                                  </div>

                             <div id="${'micVoiseActive_' + senderId}"  class="cssSoundBars">
                               <div id="${'micVoise1_' + senderId}" class="barCssSound"></div>
                               <div id="${'micVoise2_' + senderId}" class="barCssSound"></div>
                               <div id="${'micVoise3_' + senderId}" class="barCssSound"></div>
                             </div>

                             <span id="${'micVideo_' + senderId}" class="fa fas fa-microphone-alt-slash v-btn_ot_stats_sp ma-2 infoSpanWin1" style="color: red" aria-hidden="true"></span>
                             <span id="${'minBitrateVideo_' + senderId}" class="mdi mdi-signal-cellular-1 minBitrateVideo" style="color: red" aria-hidden="true"></span>


                             <p class="pdivVideoRow">${nameSender}</p>


                          </v-div>
                        </div>`
      var audio12 = `<audio
                  id="${'audio_' + senderId}"
                  autoplay controls muted  class="isHideWidth">`

      if (participant.OperFunc && !This.isExternalUser) {
        $('#devOperFunc_' + senderId).show()

        video12 = video12 + `<canvas id="${'canvas_' + senderId}" autoplay="" class="winVideovideoCanvas p1video" style="z-index:15; position: absolute; background: transparent"></canvas>
                        <video id="${'video_' + senderId}" autoplay="" class="winVideovideo" width="320" height="240"></video>
                       </div>`
      } else if (participant.scrs) {
        video12 = video12 + `<canvas id="${'canvas_' + senderId}" autoplay="" class="winVideovideoCanvas p1video" style="z-index:15; position: absolute; background: transparent"></canvas>
                        <video id="${'video_' + senderId}" style="object-fit: contain;" autoplay="" class="winVideovideo" width="320" height="240"></video>
                       </div>`
      } else {
        $('#devOperFunc_' + senderId).hide()
        video12 = video12 + `<canvas id="${'canvas_' + senderId}" autoplay="" class="winVideovideoCanvas p1video"></canvas>
                        <video id="${'video_' + senderId}" autoplay="" class="winVideovideo" width="320" height="240"></video>
                       </div>`
      }

      //endregion

      //region Добавление и скрытие доп. элементов
      This.isMicUser(senderId, true)

      $('#participants').append(video12);
      $('#participants').append(audio12);

      if (this.isMirror) {
        $('#video_' + this.senderId).addClass('clVideoMirror')
      } else {
        $('#video_' + this.senderId).removeClass('clVideoMirror')
      }

      $('#micVideo_' + senderId).hide()
      $('#micVoiseSaveAT_' + senderId).hide()
      $('#micVoiseSaveATflag_' + senderId).hide()
      $('#minBitrateVideo_' + senderId).hide()
      $('#micVoiseActive_' + senderId).hide()
      $('#devDevIconNoVideo_' + senderId).hide()
      $('#iconMainVideo1_' + senderId).hide()
      $('#iconMainVideo2_' + senderId).hide()
      $('#iconOperFunc1_' + senderId).hide()
      $('#iconOperFunc2_' + senderId).hide()
      $('#devBlockVideoBtn4_' + senderId).hide()
      $('#devAudioTranslate_' + senderId).hide()
      $('#spanStatusTranslate_' + senderId).hide()

      $('#iconOperFuncAT2_' + senderId).hide()
      $('#iconOperFuncAT3_' + senderId).hide()

      if (This.isExternalUser) {
        console.log('isExternalUser = true 2')
        $('#devOperFunc_' + senderId).hide()
        $('#devBlockVideoBtn1_' + senderId).hide()
        $('#devBlockVideoBtn2_' + senderId).hide()
        $('#devOperFunc_' + senderId).hide()

        $('#devBlockVideo_' + senderId).removeClass('devVideoBlock1')
        $('#devBlockVideo_' + senderId).addClass('devVideoBlock2')

        console.log('$(#devBlockVideoBtn2_ + senderId).hide()', $('#devBlockVideoBtn2_' + senderId).hide())
      }

      if (participant.scrs) {
        This.mainVideoPosition(senderId)
      }else if (participant.OperFunc){
        This.mainVideoPosition(senderId)
      }

      this.isSoundUser(senderId, false)

      $('#devBlockVideo_' + senderId).hide()
      $('#devOperFunc_' + senderId).hide()

      $('#canvas_' + senderId)[0].style.display = 'none'

      // object-fit: revert;

      try {
        if (participant.scrs) {
          $('#video_' + senderId).addClass('screenVideoN')
          $('#devBlockVideoBtn1_' + senderId).hide()
          // $('#devBlockVideo_' + senderId).addClass('noBackgroundSrcS')
          $('#devBlockVideoBtn4_' + senderId).show()
        }
        if (senderName.substring(0, 9) == 'Оператор_' || senderName.substring(0, 9) == 'Operator_') {
          $('#video_' + senderId).addClass('screenVideoN')
        }
      } catch (e) {
      }
      //endregion

      //region События для элементов управления
      $('#micVoiseActive_' + senderId).on('click', function () {
        console.log('click micVoiseActive_')
        This.stopTranlsateResultAudio()
      });

      $('#video_' + senderId).on('click', function () {
        if (!stream.calls[senderId].full && This.mainWin == 0) {

          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];
            participant.full = false
          }

          $('#video_' + senderId).addClass("btnVideo")

          setTimeout(() => {
            This.isSoundWinFunc = false
            stream.calls[senderId].full = true
            This.setTypeWin(2, true)//нажатие на окно оператора
            console.log('setTypeWin 2 нажатие на окно оператора')
            setTimeout(() => {
              if (This.numScreenSharinWin < 1) {
                This.isSoundWinFunc = true
              }
            }, 5000)

          }, 100)
        }
      });

      $('#devDevBlockVideoInfo_' + senderId).on('click', function () {
        if (!stream.calls[senderId].full && This.mainWin == 0) {

          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];
            participant.full = false
          }

          $('#video_' + senderId).addClass("btnVideo")

          setTimeout(() => {
            This.isSoundWinFunc = false
            stream.calls[senderId].full = true
            This.setTypeWin(2, true)//devDevBlockVideoInfo_
            setTimeout(() => {
              if (This.numScreenSharinWin < 1) {
                This.isSoundWinFunc = true
              }
            }, 5000)

          }, 100)
        }
      });

      // появление только при наведении
      if (!this.isFirefox) {
        var videoDops = document.getElementById('devDevBlockVideoInfo_' + senderId);

        videoDops.addEventListener('mousemove', e => {
          var delHMax = e.srcElement.clientHeight - e.srcElement.clientHeight / 4;
          var delHMin = e.srcElement.clientHeight / 4;
          var delWMax = e.srcElement.clientWidth - e.srcElement.clientWidth / 4;
          var delWMin = e.srcElement.clientWidth / 4;
          x = e.offsetX;
          y = e.offsetY;
          if (delHMin < y && y < delHMax && delWMin < x && x < delWMax) {
            This.onIn(senderId, participant.OperFunc)
          } else {
            This.onOut(senderId)
          }
        });

        $('#video_' + senderId).mouseout(e => {
          This.onOut(senderId)
        });

        var btnBlock1 = $('button#devBlockVideoBtn1_' + senderId)
        btnBlock1.mouseover(e => {

          This.onIn(senderId, participant.OperFunc)
        });
        btnBlock1.on('click', function () {
          if (participant.isMic == true) {
            This.clickMuteUserCall(participant.senderId, participant.senderName, participant.OperFunc)
          } else if (participant.OperFunc == true) {
            This.clickMuteUserCall(participant.senderId, participant.senderName, participant.OperFunc)
          }
        });


        var btnBlock2 = $('button#devBlockVideoBtn2_' + senderId)
        btnBlock2.mouseover(e => {
          This.onIn(senderId, participant.OperFunc)
        });
        btnBlock2.on('click', function () {
          This.clickDeleteUserCall(senderId, senderName)
        });

        var btnBlock3 = $('button#devBlockVideoBtn3_' + senderId)
        btnBlock3.mouseover(e => {
          This.onIn(senderId, participant.OperFunc)
        });
        btnBlock3.on('click', function () {
          This.mainVideoPosition(senderId)
        });

        var btnBlock4 = $('button#devBlockVideoBtn4_' + senderId)
        btnBlock4.mouseover(e => {
          This.onIn(senderId, participant.OperFunc)
        });
        btnBlock4.on('click', function () {
          This.fullVideo(senderId)
        });

        var btnBlockOper = $('button#devBtnOperFunc_' + senderId)
        btnBlockOper.mouseover(e => {
          This.onIn(senderId, participant.OperFunc)
        });
        btnBlockOper.on('click', function () {
          This.showFuncOper(senderId)
        });
      }
      else {
      }

      //endregion

      //region Подключение к webRTC

      participant.video = video12
      participant.audio = audio12

      setTimeout(() => {

        if (hasCamera) {

          // есть видео
          var options = {
            //localVideo:  $('#audio_' + senderId)[0],
            remoteVideo: $('#video_' + senderId)[0],
            name: senderId,
            onicecandidate: This.onIceCandidate
          }
        } else {
          // нет видео
          var options = {
            //localVideo:  $('#audio_' + senderId)[0], //screenStream, //$('#audioInput'),//this.audioInputValue, //{optional: [{sourceId: this.audioInputValue}]}, //$('#video_' + senderId)[0], //this.audioInputValue, //{optional: [{sourceId: this.audioInputValue}]}
            remoteVideo: $('#audio_' + senderId)[0], //$('#audioOutput'),// this.audioOutputValue, //{optional: [{sourceId: this.audioOutputValue}]}, //this.audioOutputValue,
            name: senderId,
            onicecandidate: This.onIceCandidate
          }
        }

        let self = this

        This.devTestSession = false

        console.log('iksarWebrtc.WebRtcPeer WebRtcPeerRecvonly')

        participant.peer = new this.iksarWebrtc.WebRtcPeer.WebRtcPeerRecvonly(options,
            function (error) {
              if (error) {
                participant.new = false

                return console.error(error);
              }

              console.log('Подключение к webrtc на получение видео от ' + senderName)

              participant.ofTEST = self.offerToReceiveVideo;
              participant.ofOPTION = options;

              this.generateOffer(participant.ofTEST, senderId);

            });
        ;
      }, 500)

      setTimeout(() => {
        This.isMicUser(senderId, isMic)
        console.log('This.isVideoUser(senderId, isVideo, hasCamera)', senderId, isVideo, hasCamera)
        This.isVideoUser(senderId, isVideo, hasCamera)

        this.resizeWin(4)
      }, 600)

      if (senderName.length > 10) {
        if (senderName.substring(senderName.length - 13, senderName.length) == '_расшаривание') {
          $('#video_' + senderId).addClass('screenSharingCss')
          ////console.log($('#video_' + senderId))
        }
      }

      // у стороннего элемента audio включить динамик и установить звук, после чего скрыть его (через установку свойства hidden)
      setTimeout(() => {
        This.updateDataOper(11)
        var vid = document.getElementById('audio_' + senderId);
        setTimeout(() => {
          vid.volume = 1.0;
          vid.muted = false;
          vid.setAttribute("hidden", "hidden");
          console.log('vid__', audio12, $('#audio_' + senderId), vid);
        }, 50);
      }, 100);
      //endregion
    },
    //endregion-------------------------------------------------------------------------------------

    //region OnOffer пользователя-------------------------------------------------------------------------------------
    offerToReceiveVideo(context,
                        error,
                        offerSdp,
                        senderId,
                        isError) {

      try{
        stream.calls[senderId].new = false
      }catch (e) {

      }

      if (error) {
        return console.error("sdp offer error")
      }

      var msg = {
        id: "receiveVideoFrom",
        senderId: senderId,
        senderName: '',
        sdpOffer: offerSdp
      };
      this.sendMessage(msg);

      // логика добавления аудио дорожек для сохранения ГЗ
      if (STREAM_AUDIO_SAVE == null) {
        STREAM_AUDIO_SAVE = STREAM_AUDIO;
      }

      setTimeout(() => {
        let senders = stream.calls[senderId].peer.peerConnection.getReceivers();

        console.log('Подключение к webrtc на getReceivers ' + senderId)
        let d = new Date;
        stream.calls[senderId].dateConnect = d.getTime();

        this.$refs.refDevPanel.getStats(senderId, stream.calls[senderId].peer.peerConnection)

        stream.calls[senderId].peer.peerConnection.getStats().then(function (reports) {

          setTimeout(() => {
            console.log('__Подключение__________________________________________________________________')
            reports.forEach(function (stat) {
              if (stat.id === 'RTCMediaStream_default') {
                console.log('STREAM полученный от сервера ', stat)
                stat.trackIds.forEach(function (trackId) {
                  reports.forEach(function (statTrack) {
                    if (statTrack.id === trackId) {
                      // console.log('track def ',statTrack )
                      if (statTrack.kind === 'audio') {
                        var jus = statTrack.jitterBufferDelay / statTrack.jitterBufferEmittedCount
                        console.log('Полученный track audio, id = ' + statTrack.id + ' ended ' + statTrack.ended + ', потеряно пакетов ' + statTrack.delayedPacketOutageSamples + ', задержка пакетов ' + jus)
                        stream.calls[senderId].idTrackAudio = statTrack.id
                      } else {
                        var jus = statTrack.jitterBufferDelay / statTrack.jitterBufferEmittedCount
                        console.log('Полученный track video, id = ' + statTrack.id + ' ended ' + statTrack.ended + ' framesReceived = ' + statTrack.framesReceived + ' frameWidth = ' + statTrack.frameWidth + ', задержка пакетов ' + jus)
                        stream.calls[senderId].idTrackVideo = statTrack.id
                      }
                    }
                  })
                })
              }
            })
          }, 0)
        })

        senders.forEach((sender) => {
          try {
            if (sender.track.kind == 'audio') {

              var OutgoingAudioMediaStream = new MediaStream();
              OutgoingAudioMediaStream.addTrack(STREAM_AUDIO_SAVE.getAudioTracks()[0]);

              var IncomingAudioMediaStream = new MediaStream();
              IncomingAudioMediaStream.addTrack(sender.track);

              const audioContext = new AudioContext();

              var audioIn_01 = audioContext.createMediaStreamSource(OutgoingAudioMediaStream);
              var audioIn_02 = audioContext.createMediaStreamSource(IncomingAudioMediaStream);

              var dest = audioContext.createMediaStreamDestination();

              audioIn_01.connect(dest);
              audioIn_02.connect(dest);

              STREAM_AUDIO_SAVE = dest.stream;

              if (This.isSaveCallMy) {
                this.$refs.refSaveVideo.addNewAudioTrack(sender.track)
              }
            }
          } catch (e) {
            console.error('error all audio = ' + e)
            console.error(sender)
          }

        });
      }, 500)

      // снижение качества совего видео от кол-ва участников
      This.upgradeVideo()
    },
    //endregion-------------------------------------------------------------------------------------

    //region Закрытия окон и соединения-------------------------------------------------------------------------------------
    updatePeerConnection(user){
      console.log('закрытие и вост. пользователя ',user)
      This.onParticipantLeft(user)
      user.update = 0;

      setTimeout(()=>{
        var message = {
          id: 'receiveVideoFromErrorJS',
          room: this.paramValueRoom,
          senderName: user.senderName,
          senderId: user.senderId
        };

        this.sendMessage(message);
        console.log('отправка на сервер для восстановления пользователя ',message)
        //   This.receiveVideo(user.senderName, user.isBodyPix, user.isMic, user.isVideo, user.isFuncOper, user.isOperFuncOper, user.hasCamera, user.avatar,
        //     user.senderId, user.orient, user.delta, user.prId, user.resultId, user.stepId, user.fileId)
      }, 1000)

    },

    onParticipantLeft(request) {
      This.devTestSession = false
      console.log("onParticipantLeft ", request)
      //this.audioDeleteCall.play()

      if (request.senderName.substring(0, 7) == 'scrsXR_') {
        This.numScreenSharinWin = 0
        This.isSoundWinFunc = false

        var keys = Object.keys(stream.calls);
        var numSession = keys.length
        if (numSession == 1) {
          this.typeMaket = 1
          this.setTypeWin(this.typeMaket, false, true)//если одна сессия  закрытие окна с расшариванием
          setTimeout(() => {
            this.resizeWin(103)
          }, 400)
        } else {
          this.setTypeWin(-1, false, true)// закрытие окна с расшариванием
        }

        // this.typeMaket = this.typeMaketOld
        // if (this.typeMaket == 3) {
        //   this.setTypeWin(this.typeMaket, false, true)
        // } else if (this.typeMaket == 2) (
        //   this.setTypeWin(this.typeMaket, false, true)
        // )
      }

      var participant_obj = stream.calls[request.senderId]
      console.log('onParticipantLeft participant_obj', participant_obj)
      if (participant_obj) {
        console.log("isRealFuncOper " + participant_obj.isRealFuncOper)
        if (request.senderName.substring(0, 9) == 'Оператор_' || request.senderName.substring(0, 9) == 'Operator_') {
          if (participant_obj.isRealFuncOper) {
            this.isPaint = false
            this.mainWin = 0
            This.funcOperOther()
            This.isShowFuncOther = false
            This.setTextMainWin(0)
            This.isSenderFuncOther = null
          }
        }

        $('#video_' + request.senderId).remove()
        $('#devVideo_' + request.senderId).remove()
        $('#audio_' + request.senderId).remove()


        console.log('FuncOper OperFunc ' + participant_obj.OperFunc)
        if (participant_obj.OperFunc) {
          console.log('FuncOper close& ')
        }

        if (participant_obj.isMainWin) {
          This.closeMainVideo()
        }

        if (participant_obj.isFuncOper) {

        }

        if (participant_obj.isPaint) {
          console.log('participant_obj.isPaint', participant_obj.isPaint)
          this.$emit('showFuncOper', request.senderId, true)
        }

        participant_obj.peer.peerConnection.close()
        participant_obj.peer.dispose();
        try {
          participant_obj.dispose();
        } catch (e) {
        }


        // participant.dispose();
        delete stream.calls[request.senderId]
        This.updateDataOper(10)

        if (this.typeMaket == 2 && !this.isMyFullVideo || this.isAddToAll) {

          var keys = Object.keys(stream.calls);
          var numSession = keys.length
          var isFull = false

          if (numSession == 2) {
            This.setTypeWin(1)//если всего 2 сессии
          } else {
            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];

              if (participant.full && participant.senderName != This.senderName) {
                isFull = true;
              }
              if (i == keys.length - 1) {
                if (isFull) {

                  this.resizeWin(8)
                } else {

                  if (participant.senderId != This.senderId) {

                    participant.full = true
                  } else {

                    var pr = stream.calls['' + keys[i - 1]]
                    pr.full = true
                  }

                  this.resizeWin(9)
                }
              }
            }
          }

        }
        else {
          var keys = Object.keys(stream.calls);
          var numSession = keys.length
          if (numSession == 1) {
            this.typeMaket = 1
            this.setTypeWin(this.typeMaket, false, true)//если одна сессия
            setTimeout(() => {
              this.resizeWin(101)
            }, 400)
          } else {
            this.resizeWin(10)
          }
        }
      }
      else {
        console.log('Рыцарь уже мертв')
      }

      This.upgradeVideo()

      delete stream.calls[request.senderId];
    },

    onParticipantLeftName(senderId, senderName) {

      console.log('закрыть окно ' + senderId + ' name ' + senderName)

      This.devTestSession = false
      var participant_obj = stream.calls[senderId]

      $('#video_' + senderId).remove()
      $('#devVideo_' + senderId).remove()

      try {
        if (senderId != undefined) {

          console.log('FuncOper OperFunc ' + participant_obj.OperFunc)
          if (participant_obj.OperFunc) {

          }

          // this.audioDeleteCall.play()
          if (senderName.substring(0, 7) == 'scrsXR_') {
            This.numScreenSharinWin = 0
            This.isSoundWinFunc = false
          }

          if (senderName.substring(0, 9) == 'Оператор_' || senderName.substring(0, 9) == 'Operator_') {
            console.log("isRealFuncOper " + participant_obj.isRealFuncOper)
            if (participant_obj.isRealFuncOper) {
              This.isPaint = false
              This.mainWin = 0
              console.log('FuncOper close& ')
              This.setTextMainWin(0)
              This.isShowFuncOther = false
              This.isSenderFuncOther = null
              This.funcOperOther()
            }

          }

          if (participant_obj.isMainWin) {
            This.closeMainVideo()
          }

          if (participant_obj.isPaint) {
            console.log('participant_obj.isPaint', participant_obj.isPaint)
            this.$emit('showFuncOper', senderId, true)
          }

          $('#video_' + senderId).remove()
          $('#devVideo_' + senderId).remove()

          participant_obj.peer.peerConnection.close()
          participant_obj.peer.dispose();
          participant_obj.dispose();

          delete stream.calls[senderId]
          This.updateDataOper(9)

          if (this.typeMaket == 2 && !this.isMyFullVideo || this.isAddToAll) {
            var keys = Object.keys(stream.calls);
            var numSession = keys.length
            var isFull = false

            if (numSession == 2) {
              This.setTypeWin(1)//если 2 сессии
            } else {
              for (var i = 0; i < keys.length; ++i) {
                var participant = stream.calls['' + keys[i]];

                if (participant.full && participant.senderId != This.senderId) {
                  isFull = true;
                }
                if (i == keys.length - 1) {
                  if (isFull) {
                    this.resizeWin(11)
                  } else {
                    if (participant.senderId != This.senderId) {
                      participant.full = true
                    } else {
                      var pr = stream.calls['' + keys[i - 1]]
                      pr.full = true
                    }

                    this.resizeWin(12)
                  }
                }
              }
            }

          } else {
            var keys = Object.keys(stream.calls);
            var numSession = keys.length
            if (numSession == 1) {
              this.typeMaket = 1
              this.setTypeWin(this.typeMaket, false, true)//если 1 сессия
              setTimeout(() => {
                this.resizeWin(131)
              }, 400)
            } else {
              this.resizeWin(13)
            }
          }
        } else {
          delete stream.calls[senderId]
          This.updateDataOper(8)
        }
      } catch (e) {
        // participant.dispose();
        delete stream.calls[senderId]
        This.updateDataOper(7)

        var keys = Object.keys(stream.calls);
        var numSession = keys.length
        if (numSession == 1) {
          this.typeMaket = 1
          this.setTypeWin(this.typeMaket, false, true)//если 1 сессия
          setTimeout(() => {
            this.resizeWin(141)
          }, 400)
        } else {
          this.resizeWin(14)
        }
      }
      This.upgradeVideo()
    },

    upgradeVideo() {
      console.log('upgradeVideo '+numSession)
      var keys = Object.keys(stream.calls);
      var numSession = keys.length

      var isOper = false
      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];
        if (participant.OperFunc == true) {
          isOper = true
        }
      }

      if (numSession > 7 && isOper && This.hasCamera) {
        // if(numSession > 7 && isOper && This.hasCamera && This.isStreamVideo){

        if (!This.isMaxOperVideo) {
          This.isMaxOperVideo = true

          if (this.isStreamVideo) {
            this.isStreamVideoOld = true
            this.setIsVideoStream()
          } else {
            this.isStreamVideoOld = false
          }
          console.log('7 отключение видео есть оператора')
        }
      }
      else if (numSession < 8 && isOper && This.hasCamera) {
        // }else if(numSession < 8 && isOper && This.hasCamera && This.isStreamVideo){
        if (This.isMaxOperVideo) {
          This.isMaxOperVideo = false

          if (this.isStreamVideoOld) {
            this.isStreamVideo = false
            this.setIsVideoStream()
          }
          console.log('7 включение видео есть оператора')
        }
      } else if (!isOper) {
        if (This.isMaxOperVideo) {
          This.isMaxOperVideo = false
          if (this.isStreamVideoOld && !this.isStreamVideo) {
            this.isStreamVideo = false
            this.setIsVideoStream()
          } else {
            console.log('7 видео до этого было выключено')
          }
        }
      }

      if (numSession > 8) {
        console.log('качество видео до 250')
        try{
          var participant = stream.calls[This.senderId].peer
          var media_streams = participant.peerConnection.getLocalStreams();
          var video_tracks = media_streams[0].getVideoTracks();
          video_tracks[0].applyConstraints({
            width: 640,
            height: 360,
            frameRate: 10
          });
        }catch(e){
          console.log('нет peer ',e)
          console.log('нет peer ',stream.calls[This.senderId].peer)
        }

      } else if (numSession > 5) {
        console.log('качество видео до 320')
        try{
          var participant = stream.calls[This.senderId].peer
          var media_streams = participant.peerConnection.getLocalStreams();
          var video_tracks = media_streams[0].getVideoTracks();
          video_tracks[0].applyConstraints({
            width: 640,
            height: 320,
            frameRate: 15
          });
        }catch(e){
          console.log('нет peer ',e)
          console.log('нет peer ',stream.calls[This.senderId].peer)
        }
      } else {
        console.log('качество видео до 640')
        try{
          var participant = stream.calls[This.senderId].peer
          var media_streams = participant.peerConnection.getLocalStreams();
          var video_tracks = media_streams[0].getVideoTracks();
          video_tracks[0].applyConstraints({
            width: 1240,
            height: 720,
            frameRate: 10
          });
        }catch(e){
          console.log('нет peer ',e)
          console.log('нет peer ',stream.calls[This.senderId].peer)
        }
      }
    },

    closeCall() {
      //this.isCallSessionActive = false

      var keys = Object.keys(stream.calls);

      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];

        if (participant && participant.peer) {
          $('#video_' + participant.senderId).hide()
          $('#devVideo_' + participant.senderId).hide()
          delete stream.calls['' + keys[i]];
          This.updateDataOper(6)
        }
      }

      if (!this.isAvtoStart) {
        this.$router.go(0)
      }

      this.ws.close()
      window.close();
    },
    //endregion-------------------------------------------------------------------------------------

    // region Получение данных статистики подключения-------------------------------------------------------------------------------------
    fetatsChrome(peer, name) {

      if (peer) {
        var pc = peer.peerConnection

        var roundTripTimeA, jitterA, packetsLostA, roundTripTimeV, jitterV, packetsLostV

        pc.getStats(null).then((stats) => {
          stats.forEach((report) => {
            if (report.type === 'remote-inbound-rtp') {
              if(report.mediaType === 'video'){
                roundTripTimeV = report.roundTripTime
                jitterV = report.jitter
                packetsLostV = report.packetsLost
              }else if(report.mediaType === 'audio'){
                roundTripTimeA = report.roundTripTime
                jitterA = report.jitter
                packetsLostA = report.packetsLost
              }
            }
          });
          This.powerSignal(Math.trunc(roundTripTimeA * 1000), Math.trunc(jitterA * 1000), packetsLostA, Math.trunc(roundTripTimeV * 1000), Math.trunc(jitterV * 1000), packetsLostV)
        });

        // pc.getStats(function callback(connStats) {
        //   var reports = connStats.result()
        //
        //   reports.forEach(function (report) {
        //     if (report) {
        //
        //       var report_name = report.id
        //       var report_type = report.type
        //       var report_side = report_name.slice(-4)
        //
        //       var report_names = report.names()
        //
        //
        //     }
        //     if ((report_side === 'send') && (report.stat('mediaType') === 'video')) {
        //       This.powerSignal(report.stat('googRtt'))
        //
        //     }
        //   })
        // })
      }
    },

    fetchStatsFirefox(peer) {
      if (peer) {

        var pc = peer.peerConnection

        pc.getStats().then(function (reports) {
          var startsWith
          var isStringEmpty

          reports.forEach(function (stat) {
            if (startsWith(stat.id, 'outbound_rtp_video')) { // expert
            } else if (startsWith(stat.id, 'inbound_rtp_video')) { // operator
            }
          })
        })
      }
    },

    // 0.01 - плохо
    // 0.001 - хорошо
    powerSignal(roundTripTimeA, jitterA, packetsLostA, roundTripTimeV, jitterV, packetsLostV) {
      var roundTripTime = jitterA + jitterV;

      //console.log('powerSignal '+ 'video: ',roundTripTimeV, jitterV, packetsLostV)
      //console.log('powerSignal '+ 'audio: ',roundTripTimeA, jitterA, packetsLostA)
      //console.log('powerSignal '+ 'roundTripTime: ',roundTripTime)

      if (this.isDevBitrate) {
        roundTripTime = roundTripTime + 100
      }

      this.$emit('onLevelRTT', true, roundTripTime)

      if (roundTripTime > 40) {
        if (this.numHighNet < 5) {
          this.numHighNet++
        } else if (this.numHighNet == 5) {
          this.numHighNet++
          if (!this.isVideoHardNet) {

            this.$root.showInfo(this.$t('gc.size.maxBit.error') +'\n'
                +'\n'+this.$t('video')+': '+this.$t('gc.size.maxBit.error.info1')+ ' ' +roundTripTimeV +', '+this.$t('gc.size.maxBit.error.info2')+ ' '+ jitterV+', '+this.$t('gc.size.maxBit.error.info3')+ ' '+ packetsLostV
                +'\n'
                +'\n'+this.$t('audio')+': '+this.$t('gc.size.maxBit.error.info1')+ ' ' +roundTripTimeA +', '+this.$t('gc.size.maxBit.error.info2')+ ' '+ jitterA+', '+this.$t('gc.size.maxBit.error.info3')+ ' '+ packetsLostA)

            this.isVideoHardNet = true
            this.minBitisVideoStream = this.isStreamVideo

            var message = {
              id: 'minBit',
              room: this.paramValueRoom,
              senderName: this.senderName,
              senderId: this.senderId,
              status: true,
            };
            this.sendMessage(message);
          }
        } else if (this.numHighNet >= 6) {
          if (!this.isVideoHardNet) {
            //console.log('Режим : высокая задержка сети')
            this.isVideoHardNet = true
            this.minBitisVideoStream = this.isStreamVideo
          }
        }
      }
      else {
        if (this.numHighNet > 1) {
          this.numHighNet--
        } else if (this.numHighNet == 1) {
          this.numHighNet--
          if (this.isVideoHardNet) {
            // this.$root.showInfo('Режим : нормальная задержка')
            //console.log('Режим : нормальная задержка')
            this.isVideoHardNet = false
            if (this.minBitisVideoStream) {
              this.isStreamVideo = false
              this.setIsVideoStream()
            }

            var message = {
              id: 'minBit',
              room: this.paramValueRoom,
              senderName: this.senderName,
              senderId: this.senderId,
              status: false,
            };
            ////console.log('messs ' + message)
            this.sendMessage(message);
          }
        } else if (this.numHighNet <= 0) {
          if (this.isVideoHardNet) {
            // this.$root.showInfo('Режим : нормальная задержка')
            //console.log('Режим : нормальная задержка')
            this.isVideoHardNet = false
            if (this.minBitisVideoStream) {
              this.isStreamVideo = false
              this.setIsVideoStream()
            }
          }
        }
      }
    },
    //endregion  Получение данных статистики подключения-------------------------------------------------------------------------------------

    //region Функции восстановлениея соединения
    restartWinCall(){
      var keys = Object.keys(stream.calls);

      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];

        if (participant && participant.peer) {
          // Останавливаем подключение
          participant.peer.peerConnection.close()
          // Дополнительно можно установить peerConnection равным null
          //participant.peer.peerConnection = null

          $('#video_' + participant.senderId).remove();
          $('#devVideo_' + participant.senderId).remove();
          $('#audio_' + participant.senderId).remove();
          delete stream.calls['' + keys[i]];
          This.updateDataOper(6)
        } else {
          $('#video_' + participant.senderId).hide()
          $('#devVideo_' + participant.senderId).hide()
          delete stream.calls['' + keys[i]];
          This.updateDataOper(6)
        }
      }

      if(this.isScreenSharing){
        this.isScreenSharing = false
        this.numScreenSharinWin = 0
        this.$refs.resRoomXR.closeCall()
      }

      this.$root.showInfo('Обрыв соединения с сервером, повторное подключение к серверу')
    },
    //endregion

    //region Функции для удобства интерфейса-------------------------------------------------------------------------------------

    // region функция для определения заглавных букв для отключения видео
    setNameUC(name) {

      //console.log('del setNameUC name = '+ name)
      //console.log('del name[0] = '+ name[0])

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
    // endregion

    //region убрать все закрепления окон
    closeMainVideo() {
      var keys = Object.keys(stream.calls);
      for (var i = 0; i < keys.length; ++i) {
        var participant_obj = stream.calls['' + keys[i]]
        participant_obj.full = false
        participant_obj.isMainWin = false

        var spanMains = $('#iconMainVideo_' + participant_obj.nameReplase)
        var spanMains1 = $('#iconMainVideo1_' + participant_obj.nameReplase)
        var spanMains2 = $('#iconMainVideo2_' + participant_obj.nameReplase)

        spanMains.css('color', '#636363')
        spanMains1.hide()
        spanMains2.hide()
      }
      this.mainWin = 0
      this.setTextMainWin(this.mainWin)
    },
    //endregion

    //region click удалить окно и отключить пользователя
    clickDeleteUserCall(senderId, senderName) {
      var message = {
        id: 'leaveRoomPr',
        room: This.paramValueRoom,
        senderId: senderId,
        senderName: senderName,
        userClose: This.senderName
      };

      if (senderName.substring(0, 7) == 'scrsXR_') {
        This.$refs.refConfirm.open(this.$t('gc.deleteuser.dialog'),
            this.$t('gc.deleteuser.dialog.mess.ss') + '?',
            6, {color: 'warning'}, message)
      } else {
        This.$refs.refConfirm.open(this.$t('gc.deleteuser.dialog'),
            this.$t('gc.deleteuser.dialog.mess') + ' ' + senderName + ' ' + this.$t('gc.deleteuser.dialog.mess2'),
            6, {color: 'warning'}, message)
      }
    },
    //endregion

    //region click отключить звук оператора
    clickMuteUserCall(senderId, senderName, idOper) {
      var message = {
        id: 'isMicToUser',
        room: This.paramValueRoom,
        senderId: senderId,
        senderName: senderName,
        userClose: This.senderName
      };

      // var participant = stream.calls['' + userName]
      // //console.log('send1')
      // participant.peer.send(message)
      // //console.log('send2')
      // participant.peer.send(message)
      // //console.log('send3')

      if (idOper) {
        this.sendMessage(message)
      } else {
        This.$refs.refConfirm.open(this.$t('gc.isMicUser.dialog'),
            this.$t('gc.isMicUser.dialog.mess') + ' ' + senderName + ' ' + this.$t('gc.isMicUser.dialog.mess2'),
            2, {color: 'warning'}, message)
      }
    },
    //endregion

    //region Анализ звука от других пользователей для интерфейса
    audoiColor(audioContext, source) {

      var analyser = audioContext.createAnalyser();

      analyser.smoothingTimeConstant = 0.8;
      analyser.fftSize = 2048;

//  this.analyser.connect(this.context.destination);
      analyser.minDecibels = -140;
      analyser.maxDecibels = 0;
      this.freqs = new Uint8Array(analyser.frequencyBinCount);
      this.times = new Uint8Array(analyser.frequencyBinCount);

      source.connect(analyser);
      ////console.log(analyser.fftSize)


      ////console.log(audioContext)
      var instant = 0.0;
      var slow = 0.0;
      var clip = 0.0;
      var script = audioContext.createScriptProcessor(2048, 1, 1);
      ////console.log(script)


      script.onaudioprocess = function (event) {
        const input = event.inputBuffer.getChannelData(0);
        let i;
        let sum = 0.0;
        let clipcount = 0;
        for (i = 0; i < input.length; ++i) {
          sum += input[i] * input[i];
          if (Math.abs(input[i]) > 0.99) {
            clipcount += 1;
          }
        }
        instant = Math.sqrt(sum / input.length);
        slow = 0.95 * slow + 0.05 * instant;
        clip = clipcount / input.length;

        ////console.log(instant)
      };
    },
    //endregion

    //region Строка сверху для оповещения о статусе закрепления окно
    setTextMainWin(mainWin, userName) {

      if (mainWin == 0) {
        this.textFuncOther = ''
      } else if (mainWin == 1) {
        this.textFuncOther = this.$t('gc.win.docked')
      } else if (mainWin == 2) {
        this.textFuncOther = this.$t('gc.win.docked2')
      } else if (mainWin == 3) {
        this.textFuncOther = this.$t('gc.win.docked3')
      } else if (mainWin == 4) {
        this.textFuncOther = `${This.$t('gc.win.docked')} (${userName} ${This.$t('info.funcOper')})`
      } else {
        this.textFuncOther = ' '
      }
    },
    //endregion

    //region на весь экран видео
    fullVideo(senderId) {
      document.getElementById('video_' + senderId).requestFullscreen()
    },
    //endregion

    // region Сделать окно главным относительно остальных
    mainVideoPosition(senderId, isMain, _mainWin) {
      // //test123
      console.log('mainVideoPosition Сделать окно главным относительно остальных')
      // document.getElementById('video_' + userName.replace(/ /g, '')).requestFullscreen()
      //This.updatePeerConnection(stream.calls['' + senderId])
      if (this.isShowFuncOther) {
        // this.$root.showInfo(This.$t('reject.call.info.noFunc7'))
      }
      else if (!this.isPaint && this.mainWin < 2 || _mainWin) {
        var participant = stream.calls['' + senderId]
        var spanMain = $('#iconMainVideo_' + senderId)
        var spanMain1 = $('#iconMainVideo1_' + senderId)
        var spanMain2 = $('#iconMainVideo2_' + senderId)

        if (participant.isMainWin) {
          spanMain.css('color', '#636363')
          spanMain1.hide()
          spanMain2.hide()
          this.mainWin = 0
          this.setTextMainWin(this.mainWin)
        } else {
          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant_obj = stream.calls['' + keys[i]]
            participant_obj.full = false
            participant_obj.isMainWin = false

            var spanMains = $('#iconMainVideo_' + participant_obj.nameReplase)
            var spanMains1 = $('#iconMainVideo1_' + participant_obj.nameReplase)
            var spanMains2 = $('#iconMainVideo2_' + participant_obj.nameReplase)

            spanMains.css('color', '#636363')
            spanMains1.hide()
            spanMains2.hide()
          }

          participant.full = true
          This.setTypeWin(2, true)// включены функции оператора isShowFuncOther

          spanMain.css('color', 'red')
          spanMain1.show()
          spanMain2.show()
          this.mainWin = 1
          this.setTextMainWin(this.mainWin)
        }
        if (isMain) {
          participant.isMainWin = false
          if (_mainWin > 0) {
            this.mainWin = _mainWin
            this.setTextMainWin(this.mainWin)
          }
        } else {
          participant.isMainWin = !participant.isMainWin
        }
        return true
      }
      else {
        if (this.isPaint) {
          this.$root.showInfo(This.$t('reject.call.info.noFunc5'))
        } else if (this.mainWin > 1) {
          this.$root.showInfo(This.$t('reject.call.info.noFunc6'))
        } else {
          this.$root.showInfo(This.$t('reject.call.info.noFunc7'))
        }
        return false
      }
    },
    //endregion

    //region Событие, макет отобразить все
    setIsMoreWin() {
      This.isMoreWin = !This.isMoreWin
      This.isTypeWin = false
    },

    setNewMicADd() {
      this.$root.showInfo('включен режим отладки микрофона')
      this.$emit('devLog', 'ws close')
    },
    //endregion

    //region Событие, чат
    setChatMessage(b) {
      if (b == false) {
        this.isChatShow = false
      } else if (b == true) {
        this.isChatShow = true
      } else {
        this.isChatShow = !this.isChatShow
      }

      if (this.isChatShow) {
        //......................................................
        // отключаем информацию
        if (this.isPanelInfoUser) {
          this.isPanelInfoUser = false
          //$('#divMainLinkUser').hide()
          if(!$('#divMainLinkUser').hasClass("isHideRightPanel")){
            $('#divMainLinkUser').removeClass('isShowRightPanel')
            $('#divMainLinkUser').addClass('isHideRightPanel')
          }
        }
        // закрыть информацию о чек-листе
        if (this.isPanelChik) {
          this.isPanelChik = false
          //$('#mainChikPanel').hide()
          if(!$('#mainChikPanel').hasClass("isHideRightPanel")){
            $('#mainChikPanel').removeClass('isShowRightPanel')
            $('#mainChikPanel').addClass('isHideRightPanel')
          }
        }
        //......................................................
        // открываем чат
        This.$notify.closeAll()
        $('.sc-user-input--text').attr('placeholder', This.$t('gc.addMessText'));
        if (This.isShowFuncOther) {
          $('#mainRoom').css('width', '100%')
          $('#mainAT').css('width', '100%')
        } else {
          $('#mainRoom').css('width', 'calc(100% - 400px)')
          $('#mainAT').css('width', 'calc(100% - 400px)')
        }

        //$('#mainChatMessage').show()
        if(!$('#mainChatMessage').hasClass("isShowRightPanel")){
          $('#mainChatMessage').removeClass('isHideRightPanel')
          $('#mainChatMessage').addClass('isShowRightPanel')
        }
        this.$emit('showChat', '')
      } else {
        $('#mainRoom').css('width', '100%')
        $('#mainAT').css('width', '100%')
        //$('#mainChatMessage').hide()
        if(!$('#mainChatMessage').hasClass("isHideRightPanel")){
          $('#mainChatMessage').removeClass('isShowRightPanel')
          $('#mainChatMessage').addClass('isHideRightPanel')
        }
      }
    },

    newMessageChat(text, name) {
      if (!this.isChatShow) {
        // this.$root.showInfo(name+': '+text, This.$t('gc.new.message'))
        // this.$root.showChat(name+': '+text, This.$t('gc.new.message'))

        // разделить ссылку по пробелам и проверять, является ли каждый элемент ссылкой
        // возвращать индекс массива, если нашли ссылку, до этого индекса записать в сообщение
        //сам индекс записать в ссылку
        let textArr = text.split(' ')
        let hasURL = isValidURL(textArr) //проверить обрабатывается ли ссылка с запятой (лучше обрезать)
        if (hasURL !== -1) {
          this.$notify({
            message: "",
            type: "",
            top: false,
            bottom: true,
            left: false,
            right: true,
            showClose: false,
            closeDelay: 5000,
            actionText: this.$t('gc.open.chat'),
            onActionClick: () => {
              This.$notify.closeAll()
              This.setChatMessage()
            },
          });
          // setTimeout(()=> {
          // console.log(document.querySelector('.notification__content'))

          let notifyMessAll = document.querySelectorAll('.notification__message')
          let notifyMess = notifyMessAll[notifyMessAll.length - 1]

          const link = document.createElement('a');
          link.href = textArr[hasURL].replace(/[,.!?\-]$/, '');
          link.textContent = ' ' + textArr[hasURL];
          notifyMess.children[0].remove()
          link.style.color = '#001aff';
          link.style.fontSize = '15px'
          link.style.fontFamily = 'Rooftop'
          link.style.marginLeft = '5px'
          link.style.display = 'block';
          link.style.overflow = 'hidden';
          link.style.textOverflow = 'ellipsis';
          link.style.whiteSpace = 'nowrap';
          link.target = '_blank';

          let textMes = '';
          for (let i = 0; i < hasURL; i++)
            textMes += textArr[i] + ' '

          const paragraph = document.createElement('p');
          paragraph.textContent = name + ': ' + textMes + ' ';
          paragraph.style.color = 'black';
          paragraph.style.fontSize = '15px'
          paragraph.style.fontFamily = 'Rooftop'
          paragraph.style.maxWidth = '400px';
          paragraph.style.display = 'block';
          paragraph.style.overflow = 'hidden';
          paragraph.style.textOverflow = 'ellipsis';
          paragraph.style.whiteSpace = 'nowrap';

          notifyMess.appendChild(paragraph)
          setTimeout(() => {
            let width = parseFloat(getComputedStyle(paragraph).width)
            // console.log(getComputedStyle(paragraph))
            if (width < 350) {
              link.style.maxWidth = 400 - width + 'px';
              notifyMess.appendChild(link)
            }
          }, 10)

          // }, 0)
        } else {
          this.$notify({
            message: name + ': ' + text,
            type: "",
            top: false,
            bottom: true,
            left: false,
            right: true,
            showClose: false,
            closeDelay: 5000,
            actionText: this.$t('gc.open.chat'),
            onActionClick: () => {
              This.$notify.closeAll()
              This.setChatMessage()
            },
          });
        }
      }
    },

    //region Событие, чат
    setDevPanel(b) {

      this.$emit('devLog', 'ws close')

      if (b == false) {
        this.isDevGCPanel = false
      } else if (b == true) {
        this.isDevGCPanel = true
      } else {
        this.isDevGCPanel = !this.isDevGCPanel
      }

      if (this.isDevGCPanel) {
        // отключаем информацию
        if (this.isPanelInfoUser) {
          this.isPanelInfoUser = false
          //$('#divMainLinkUser').hide()
          if(!$('#divMainLinkUser').hasClass("isHideRightPanel")){
            $('#divMainLinkUser').removeClass('isShowRightPanel')
            $('#divMainLinkUser').addClass('isHideRightPanel')
          }
        }

        if (This.isShowFuncOther) {
          $('#mainRoom').css('width', '100%')
          $('#mainAT').css('width', '100%')
        } else {
          $('#mainRoom').css('width', 'calc(100% - 400px)')
          $('#mainAT').css('width', 'calc(100% - 400px)')
        }

        $('#divDevPanel').show()
        this.$emit('showChat', '')
      } else {
        $('#mainRoom').css('width', '100%')
        $('#mainAT').css('width', '100%')
        $('#divDevPanel').hide()
      }
    },

    newUserEcpect(text, name) {
    },
    //endregion

    //region Событие, макет с главным окном
    setIsTypeWin() {
      This.isMoreWin = false
      This.isTypeWin = !This.isTypeWin
    },
    //endregion

    //region Установить тип макета окон
    setTypeWin(type, _full, _click, _save) {

      if (_save) {

        this.setLocalStorage('gc.typewin', type)
      } else if (type == -1) {
        console.log('!! get typeWin ' + this.getLocalStorage('gc.typewin'))
        this.typeMaket = this.getLocalStorage('gc.typewin')
        type = this.getLocalStorage('gc.typewin')
      }

      if (this.isShowFuncOther) {
        this.$root.showInfo(This.$t('reject.call.info.noFunc10'))
      } else if (!this.isPaint && this.mainWin < 2) {
        if (_click) {
          This.closeMainVideo()
        }

        if (This.isMyFullVideo) {
          This.isMyFullVideo = false

          $('#idDivLocalVideo').removeClass('iddivlocalvideo2')
          $('#idDivLocalVideo').addClass('iddivlocalvideo')
          console.log('is100 6')
          $('#idnewMyVideoClass').css('width', '100%')
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
          // $('#participants').remove($('#devVideo_Opermain'));
        }

        if (this.isAddToAll && type != 3) {
          this.isAddToAll = false

          $('#devDevBlockVideo_' + this.senderId).addClass('winVideoRoomDevMy')

          $('#idDivLocalVideo').append($('#devDevBlockVideo_' + this.senderId))
          $('#idDivLocalVideo').append($('#video_' + this.senderId))

          $('#devVideo_' + this.senderId).hide()
          $('#iconUserAddAll_' + this.senderId).removeClass('mdi mdi-grid v-btn_ot_ex_sp v-span-video')
          $('#iconUserAddAll_' + this.senderId).addClass('mdi mdi-grid-off v-btn_ot_ex_sp v-span-video')


          $('#idDivLocalVideo').removeClass('iddivlocalvideo2')
          $('#idDivLocalVideo').addClass('iddivlocalvideo')
          $('#idnewMyVideoClass').css('width', '0px')
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
        }


        if (type == 1) {
          $('#idDivLocalVideo').css('display', 'block')
          this.typeMaket = 1
          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            stream.calls['' + keys[i]].full = false
          }
          setTimeout(() => {
            This.resizeWin(5)
          }, 100)
        } else if (type == 2) {
          this.typeMaket = 2
          $('#idDivLocalVideo').css('display', 'block')

          if (!_full) {
            var keys = Object.keys(stream.calls);
            for (var i = 0; i < keys.length; ++i) {
              stream.calls['' + keys[i]].full = false
            }
          }

          setTimeout(() => {
            if (!_full) {
              if ('' + keys[0] != This.senderId) {
                stream.calls['' + keys[0]].full = true
              } else {
                stream.calls['' + keys[1]].full = true
              }
            }

            setTimeout(() => {
              This.resizeWin(6)
            }, 100)
          }, 100)
        } else {

          $('#idDivLocalVideo').css('display', 'none')
          this.typeMaket = 3
          this.isAddToAll = true

          $('#devDevBlockVideo_' + this.senderId).removeClass('winVideoRoomDevMy')

          $('#devVideo_' + this.senderId).show()
          $('#devVideo_' + this.senderId).append($('#devDevBlockVideo_' + this.senderId))
          var videoMy = $('#video_' + this.senderId)
          $('#devVideo_' + this.senderId).append(videoMy)

          $('#iconUserAddAll_' + this.senderId).removeClass('mdi mdi-grid-off v-btn_ot_ex_sp v-span-video')
          $('#iconUserAddAll_' + this.senderId).addClass('mdi mdi-grid v-btn_ot_ex_sp v-span-video')
          setTimeout(() => {
            This.resizeWin(7)
          }, 100)
        }
      } else if (_click) {
        if (this.isPaint) {
          this.$root.showInfo(This.$t('reject.call.info.noFunc8'))
        } else if (this.mainWin > 1) {
          this.$root.showInfo(This.$t('reject.call.info.noFunc9'))
        } else {
          this.$root.showInfo(This.$t('reject.call.info.noFunc10'))
        }
      }
    },
    //endregion

    //region Изменить верстку окон
    resizeWin(stat) {
      //qweqwe

      This.iconUser()

      var keys = Object.keys(stream.calls);
      var numSession = keys.length

      //console.log('resize ' + numSession)

      if (numSession == 1) {
        $('#idDivLocalVideo').addClass('onePre')
        try {
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').remove($('#idDivLocalVideo'))
        } catch (e) {
        }
        $('#participants').append($('#idDivLocalVideo'));
      } else if (This.isAvtoStart && This.isPhone) {
        $('#idDivLocalVideo').addClass('onePre')
        try {
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').remove($('#idDivLocalVideo'))
        } catch (e) {
          ////console.log(e)
        }
        $('#participants').append($('#idDivLocalVideo'));
      } else {
        $('#idDivLocalVideo').removeClass('onePre')
        try {
          $('#participants').remove($('#idDivLocalVideo'))
        } catch (e) {
        }
        if (!This.isMyFullVideo) {
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
        }


        if (this.isMyFullVideo) {
          //console.log('мое видео главнее всего')
          if (this.typeMaket == 1) {

            ////console.log('type = 1')
            var pos = 0;
            for (var i = 0; i < keys.length; ++i) {

              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId
              This.winDeleteClass(partName)

              if (numSession == 2) {
                $('#devVideo_' + partName).addClass('winVideoRoom2')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev2')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev2')
              } else if (numSession == 3) {
                $('#devVideo_' + partName).addClass('winVideoRoom3')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev3')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev3')
              } else if (numSession == 4 || numSession == 5) {
                $('#devVideo_' + partName).addClass('winVideoRoom45')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev45')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev45')
              } else if (numSession == 6 || numSession == 7) {
                $('#devVideo_' + partName).addClass('winVideoRoom67')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev67')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev67')
              } else if (numSession == 8 || numSession == 9) {
                //console.log('alarm 3 8')
                $('#devVideo_' + partName).addClass('winVideoRoom89')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev89')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev89')
              } else if (numSession == 10 || numSession == 11 || numSession == 12) {
                //console.log('alarm 3 9')
                $('#devVideo_' + partName).addClass('winVideoRoom101112')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev101112')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev101112')
              } else if (numSession == 13 || numSession == 14 || numSession == 15 || numSession == 16) {
                $('#devVideo_' + partName).addClass('winVideoRoom13_16')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev13_16')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev13_16')
              } else if (numSession > 16) {
                $('#devVideo_' + partName).addClass('winVideoRoomFrom17')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFrom17')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFrom17')
              } else {
                $('#devVideo_' + partName).addClass('winVideoRoom45')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev45')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev45')
              }

              try {
                $('#devMiniVideos').remove($('#devVideo_' + partName))
              } catch (e) {

              }


              $('#participants').append($('#devVideo_' + partName));
              $('#devMiniVideos').hide()

            }

            This.winDeleteClass(this.senderId + 'main')

            if (numSession == 2) {
              $('#devVideo_Opermain').addClass('winVideoRoom2')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev2')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev2')
            } else if (numSession == 3) {
              $('#devVideo_Opermain').addClass('winVideoRoom3')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev3')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev3')
            } else if (numSession == 4 || numSession == 5) {
              $('#devVideo_Opermain').addClass('winVideoRoom45')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev45')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev45')
            } else if (numSession == 6 || numSession == 7) {
              $('#devVideo_Opermain').addClass('winVideoRoom67')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev67')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev67')
            } else if (numSession == 8 || numSession == 9) {
              //console.log('alarm 4 8')
              $('#devVideo_Opermain').addClass('winVideoRoom89')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev89')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev89')
            } else if (numSession == 10 || numSession == 11 || numSession == 12) {
              //console.log('alarm 4 10')
              $('#devVideo_Opermain').addClass('winVideoRoom101112')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev101112')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev101112')
            } else if (numSession == 13 || numSession == 14 || numSession == 15 || numSession == 16) {
              $('#devVideo_Opermain').addClass('winVideoRoom13_16')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev13_16')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev13_16')
            } else if (numSession > 16) {

            } else {
              $('#devVideo_Opermain').addClass('winVideoRoom45')
              $('#devDevBlockVideo_Opermain').addClass('winVideoRoomDev45')
              $('#devDevBlockVideoInfo_Opermain').addClass('winVideoRoomDev45')
            }
          } else {
            ////console.log('type = full')
            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId
              This.winDeleteClass(partName)

              if (participant.full) {
                $('#devVideo_' + partName).addClass('winVideoRoomFully')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFully')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFully')
                try {
                  $('#devMiniVideos').remove($('#devVideo_' + partName))
                } catch (e) {
                }
                $('#participants').append($('#devVideo_' + partName));
              } else {
                $('#devVideo_' + partName).addClass('winVideoRoomFulln')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFulln')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFulln')

                try {
                  $('#participants').remove($('#devVideo_' + partName));
                } catch (e) {
                }
                $('#devMiniVideos').show()
                $('#devMiniVideos').append($('#devVideo_' + partName))


              }

            }
          }
        } else if (!this.isAddToAll) {
          // //console.log('isAddToAll = false, все окна без меня')
          if (this.typeMaket == 1) {
            var pos = 0;
            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId

              if (participant.senderId != This.senderId) {
                This.winDeleteClass(partName)
                if (numSession == 2) {
                  $('#devVideo_' + partName).addClass('winVideoRoom2')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev2')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev2')
                } else if (numSession == 3) {
                  $('#devVideo_' + partName).addClass('winVideoRoom3')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev3')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev3')
                } else if (numSession >= 4 && numSession <= 5) {
                  $('#devVideo_' + partName).addClass('winVideoRoom45')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev45')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev45')
                } else if (numSession >= 6 && numSession <= 7) {
                  $('#devVideo_' + partName).addClass('winVideoRoom67')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev67')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev67')
                } else if (numSession >= 8 && numSession <= 9) {
                  //console.log('alarm 8')
                  $('#devVideo_' + partName).addClass('winVideoRoom89')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev89')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev89')
                } else if (numSession >= 10 && numSession <= 13) {
                  //console.log('alarm 10')
                  $('#devVideo_' + partName).addClass('winVideoRoom101112')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev101112')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev101112')
                } else if (numSession >= 14 && numSession <= 17) {
                  $('#devVideo_' + partName).addClass('winVideoRoom13_16')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev13_16')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev13_16')
                } else if (numSession > 17) {
                  $('#devVideo_' + partName).addClass('winVideoRoomFrom17')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFrom17')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFrom17')
                } else {
                  $('#devVideo_' + partName).addClass('winVideoRoomDN')
                  $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDN')
                  $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDN')
                }

                try {
                  $('#devMiniVideos').remove($('#devVideo_' + partName))
                } catch (e) {

                }

                if (numSession > 17) {
                  //console.log('16>')
                  if (pos == 0) {
                    $('#idVrowPosition1').append($('#devVideo_' + partName));
                    pos++
                  } else if (pos == 1) {
                    $('#idVrowPosition2').append($('#devVideo_' + partName));
                    pos++
                  } else if (pos == 2) {
                    $('#idVrowPosition3').append($('#devVideo_' + partName));
                    pos++
                  } else if (pos == 3) {
                    $('#idVrowPosition4').append($('#devVideo_' + partName));
                    pos = 0
                  } else {
                    $('#idVrowPosition1').append($('#devVideo_' + partName));
                    pos = 0
                  }

                } else {
                  $('#participants').append($('#devVideo_' + partName));
                }


              }
            }
            $('#devMiniVideos').hide()
          } else {
            ////console.log('type = full')
            var fullWin = false
            for (var i = 0; i < keys.length; ++i) {
              if (stream.calls['' + keys[i]].full) {
                fullWin = true
              }
            }
            if (!fullWin) {
              if ('' + keys[0] != This.senderId) {
                stream.calls['' + keys[0]].full = true
              } else {
                stream.calls['' + keys[1]].full = true
              }
            }

            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId
              This.winDeleteClass(partName)

              if (participant.full) {

                $('#devVideo_' + partName).addClass('winVideoRoomFully')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFully')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFully')
                try {
                  $('#devMiniVideos').remove($('#devVideo_' + partName))
                } catch (e) {
                }
                $('#participants').append($('#devVideo_' + partName));
              } else if (participant.senderId != This.senderId) {
                $('#devVideo_' + partName).addClass('winVideoRoomFulln')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFulln')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFulln')

                try {
                  $('#participants').remove($('#devVideo_' + partName));
                } catch (e) {
                }
                $('#devMiniVideos').show()
                $('#devMiniVideos').append($('#devVideo_' + partName))

              }
            }
          }
        } else {
          //console.log('isAddToAll = true, мое окно вместе со всеми')
          if (this.typeMaket == 3) {
            var pos = 0;
            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId
              This.winDeleteClass(partName)

              if (numSession == 1) {
                $('#devVideo_' + partName).addClass('winVideoRoom2')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev2')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev2')
              } else if (numSession == 2) {
                $('#devVideo_' + partName).addClass('winVideoRoom3')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev3')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev3')
              } else if (numSession == 3 || numSession == 4) {
                $('#devVideo_' + partName).addClass('winVideoRoom45')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev45')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev45')
              } else if (numSession == 5 || numSession == 6) {
                $('#devVideo_' + partName).addClass('winVideoRoom67')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev67')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev67')
              } else if (numSession >= 7 && numSession <= 8) {
                //console.log('alarm 2 8')
                $('#devVideo_' + partName).addClass('winVideoRoom89')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev89')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev89')
              } else if (numSession >= 9 && numSession <= 12) {
                //console.log('alarm 2 10')
                $('#devVideo_' + partName).addClass('winVideoRoom101112')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev101112')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev101112')
              } else if (numSession >= 13 && numSession <= 16) {
                $('#devVideo_' + partName).addClass('winVideoRoom13_16')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDev13_16')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDev13_16')
              } else if (numSession > 16) {
                $('#devVideo_' + partName).addClass('winVideoRoomFrom17')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFrom17')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFrom17')
              } else {
                $('#devVideo_' + partName).addClass('winVideoRoomDN')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDN')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDN')
              }
              try {
                $('#devMiniVideos').remove($('#devVideo_' + partName))
              } catch (e) {
              }
              if (numSession > 16) {
                //console.log('16>')
                if (pos == 0) {
                  $('#idVrowPosition1').append($('#devVideo_' + partName));
                  pos++
                } else if (pos == 1) {
                  $('#idVrowPosition2').append($('#devVideo_' + partName));
                  pos++
                } else if (pos == 2) {
                  $('#idVrowPosition3').append($('#devVideo_' + partName));
                  pos++
                } else if (pos == 3) {
                  $('#idVrowPosition4').append($('#devVideo_' + partName));
                  pos = 0
                } else {
                  $('#idVrowPosition1').append($('#devVideo_' + partName));
                  pos = 0
                }

              } else {
                $('#participants').append($('#devVideo_' + partName));
              }
            }
            $('#devMiniVideos').hide()
          } else {
            for (var i = 0; i < keys.length; ++i) {
              var participant = stream.calls['' + keys[i]];
              var partName = participant.senderId
              This.winDeleteClass(partName)

              if (participant.full && participant.senderId != This.senderId) {
                $('#devVideo_' + partName).addClass('winVideoRoomFully')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFully')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFully')
                try {
                  $('#devMiniVideos').remove($('#devVideo_' + partName))
                } catch (e) {
                }
                $('#participants').append($('#devVideo_' + partName));
              } else if (participant.senderId != This.senderId) {
                $('#devVideo_' + partName).addClass('winVideoRoomFulln')
                $('#devDevBlockVideo_' + partName).addClass('winVideoRoomDevFulln')
                $('#devDevBlockVideoInfo_' + partName).addClass('winVideoRoomDevFulln')

                try {
                  $('#participants').remove($('#devVideo_' + partName));
                } catch (e) {
                }
                $('#devMiniVideos').show()
                $('#devMiniVideos').append($('#devVideo_' + partName))

              }

            }
          }
        }
      }
    },
    //endregion

    //region удалить все классы у окна и его элементов у пользователя
    winDeleteClass(name) {

      $('#devVideo_' + name).removeClass('winVideoRoomDN')
      $('#devVideo_' + name).removeClass('winVideoRoom2')
      $('#devVideo_' + name).removeClass('winVideoRoom3')
      $('#devVideo_' + name).removeClass('winVideoRoom45')
      $('#devVideo_' + name).removeClass('winVideoRoom67')
      $('#devVideo_' + name).removeClass('winVideoRoom89')
      $('#devVideo_' + name).removeClass('winVideoRoom101112')
      $('#devVideo_' + name).removeClass('winVideoRoom13_16')
      $('#devVideo_' + name).removeClass('winVideoRoomFrom17')
      $('#devVideo_' + name).removeClass('winVideoRoomFully')
      $('#devVideo_' + name).removeClass('winVideoRoomFulln')

      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDN')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev2')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev3')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev45')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev67')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev89')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev101112')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDev13_16')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDevFrom17')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDevFully')
      $('#devDevBlockVideo_' + name).removeClass('winVideoRoomDevFulln')

      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDN')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev2')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev3')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev45')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev67')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev89')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev101112')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDev13_16')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDevFrom17')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDevFully')
      $('#devDevBlockVideoInfo_' + name).removeClass('winVideoRoomDevFulln')

      $('#video_' + name).removeClass("btnVideo")
    },
    // endregion

    //region Очистка мертвых душ
    usersError(mess) {
      console.log('Очистка мертвых душ', mess)
      var keys = Object.keys(stream.calls);
      console.log('ОМД keys', keys)
      var numSession = keys.length
      var numU = numSession

      if (!this.devTestSession) {
        if (This.isStartCallT) {

          try {
            var arraytoSenderName = mess.usersActive.split(', ');
            var arraytoSenderID = mess.usersActiveSenderID.split(', ');
            // console.log('Массив активных имен с сервера usersActiveSenderID ', arraytoSenderID)
            // console.log('Массив активных имен с сервера arraytoSenderName ', arraytoSenderName)
            // console.log(arrayOfStrings)

            // на странице больше чем пришло от сервера
            var namesSet = new Set(arraytoSenderID);
            console.log('Set активных имен с сервера arrayOfStrings ', namesSet)
            // console.log(namesSet)

            for (var i = 0; i < numSession; i++) {

              if(keys[i] != This.senderId){
                try{
                  var pc = stream.calls['' + keys[i]].peer.peerConnection
                  var name = stream.calls['' + keys[i]].senderName

                  console.log('проверка, '+name+'что peer stream.calls[keys[i]] ', stream.calls['' + keys[i]])
                  console.log('проверка, '+name+'что peer signalingState ', pc.signalingState)
                  console.log('проверка, '+name+'что peer iceGatheringState ', pc.iceGatheringState)
                  console.log('проверка, '+name+'что peer iceConnectionState ', pc.iceConnectionState)
                  console.log('проверка, '+name+'что peer connectionState ', pc.connectionState)

                  try{
                    const mediaStreamTrackAudio = $(`#audio_` + keys[i])[0].srcObject.getTracks()[0]
                    const mediaStreamTrackVideo = $(`#video_` + keys[i])[0].srcObject.getTracks()[0]

                    if (mediaStreamTrackAudio) {
                      console.log('проверка, '+name+' mediaStreamTrackAudio трасса существует');
                    } else {
                      console.error('проверка, '+name+' mediaStreamTrackAudio трасса отсутствует');
                    }

                    if (mediaStreamTrackVideo) {
                      console.log('проверка, '+name+' mediaStreamTrackVideo трасса существует');
                    } else {
                      console.error('проверка, '+name+' mediaStreamTrackVideo трасса отсутствует');
                    }

                    if (mediaStreamTrackAudio && mediaStreamTrackAudio.readyState === 'live') {
                      console.log('проверка, '+name+' mediaStreamTrackAudio трасса активна');
                    } else {
                      console.log('проверка, '+name+' mediaStreamTrackAudio трасса не активна или отсутствует');
                    }
                  }catch (e) {
                    console.log('ошибка проверка, '+name+' mediaStreamTrackAudio трасса существует ',e)
                    //console.error('ошибка проверка, '+name+' Медиа трасса существует ',$(`#video_` + keys[i]))
                  }


                  if(pc.signalingState === 'stable' && pc.connectionState === 'connected'){
                    console.log('проверка, '+name+' peer рабочий ', stream.calls['' + keys[i]])
                    stream.calls['' + keys[i]].update = 0
                  }else{
                    console.log('проверка, '+name+'что peer не рабочий stream.calls[keys[i]] ', stream.calls['' + keys[i]])
                    console.log('проверка, '+name+'что peer не рабочий signalingState ', pc.signalingState)
                    console.log('проверка, '+name+'что peer не рабочий iceGatheringState ', pc.iceGatheringState)
                    console.log('проверка, '+name+'что peer не рабочий iceConnectionState ', pc.iceConnectionState)
                    console.log('проверка, '+name+'что peer не рабочий connectionState ', pc.connectionState)

                    if(stream.calls['' + keys[i]].update > 2){
                      This.updatePeerConnection(stream.calls['' + keys[i]])
                      stream.calls['' + keys[i]].update = 0
                    }else{
                      stream.calls['' + keys[i]].update = stream.calls['' + keys[i]].update+1
                    }
                  }
                  console.log('проверка, '+name+' peer .update', stream.calls['' + keys[i]].update)
                }catch (e) {
                  console.log('e ',e)
                  console.log('НУжно попробовать повторно подключится')
                }
              }
              else{
                console.log('проверка, что мой peer рабочий ',stream.calls['' + keys[i]].peer.peerConnection)
                var pc = stream.calls['' + keys[i]].peer.peerConnection

                console.log('проверка, что мой peer stream.calls[keys[i]] ', stream.calls['' + keys[i]])
                console.log('проверка, что мой peer signalingState ', pc.signalingState)
                console.log('проверка, что мой peer iceGatheringState ', pc.iceGatheringState)
                console.log('проверка, что мой peer iceConnectionState ', pc.iceConnectionState)
                console.log('проверка, что мой peer connectionState ', pc.connectionState)

                if(pc.signalingState === 'stable'){
                  console.log('проверка, что МОЙ peer рабочий ', stream.calls['' + keys[i]])
                  stream.calls['' + keys[i]].update = 0
                }
                else if(pc.signalingState === 'failed'){
                  console.log('МОЙ peer НЕ поток потерян, failed перезагрузка')
                  //location.reload();
                  This.ws.close()

                  stream.calls['' + keys[i]].update = 0
                }
                else{
                  console.log('проверка, что МОЙ peer НЕ рабочий stream.calls[keys[i]] ', stream.calls['' + keys[i]])

                  if(stream.calls['' + keys[i]].update > 4){
                    console.log('МОЙ peer НЕ поток потерян, нужно перегрузить')
                    //location.reload();
                    This.ws.close()

                    stream.calls['' + keys[i]].update = 0
                  }else{
                    stream.calls['' + keys[i]].update = stream.calls['' + keys[i]].update+1
                  }
                }

                console.log('проверка, '+name+' peer .update', stream.calls['' + keys[i]].update)

                if(stream.calls['' + keys[i]].peer.peerConnection.connectionState === 'failed'
                    && stream.calls['' + keys[i]].peer.peerConnection.iceConnectionState === 'disconnected'){
                  console.log('мой поток потерян, нужно попробовать перегрузить')

                  This.ws.close()
                  //location.reload();
                }
              }

              // проверка, что peer рабочий
              if (!namesSet.has(' ' + keys[i]) && !namesSet.has('' + keys[i])) {

                // console.log('DELETE SESSION - no name server ' + '' + arraytoSenderID[i])
                try {
                  This.onParticipantLeftName(keys[i], stream.calls['' + keys[i]].senderName)
                } catch (e) {
                  This.onParticipantLeftName(keys[i], arraytoSenderName[i])
                }

                numU--
              } else {
                // console.log('arraytoSenderName[i] '+arraytoSenderName[i]+' вроде живой')
              }
            }
          } catch (e) {
            console.error('Очистка мертвых душ e = ', e)
          }
        }
        else {
          console.log('пока рано')
        }
      }
      return numU
    },
    //endregion

    //region Функция которая отработает при наведении курсора на элемент
    onIn(senderId, isOper) {
      $('#devBlockVideo_' + senderId).show()
      if (isOper && !This.isExternalUser) {
        $('#devOperFunc_' + senderId).show()
      }
    },
    //endregion

    //region Функция которая отработает при выходе курсора за элемент
    onOut(senderId) {
      $('#devBlockVideo_' + senderId).hide()
      $('#devOperFunc_' + senderId).hide()
    },
    //endregion
    //endregion--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

    //region Пока не распределил -------------------------------------------------------------------------------------
    newCallData() {
      return {
        ws: null,
        peer: null,
        full: false,

        is_dev: false,
        save_video: true,
        send_video: true,
        saved_video_format: DEFAULT_SAVED_VIDEO_FORMAT,

        name: '',

        call_time_timeout: 1000,
        call_time_interval: null,

        bad_fps_sec: 0,
        bad_quality_sec: 0,

        call_time: 0,

        call_time_display: null,
        resolution_display: null,

        win_div: null,
        win_video_div: null,

        video: null,

        preview: null,
        canvas: null,
        canvas_ctx: null,

        active_mode: null,

        chat: [],
        chat_open: false,

        stats: {
          interval: null,
          timeout: 1000,

          time: 0,

          iceAddress: {
            localAddress: null,
            remoteAddress: null,
          },

          codec: {
            audio: null,
            video: null,
          },

          expert: {
            bitrate: null,
            bandwidth: null, // no firefox

            width: null,
            height: null,
            framerate: null, // no firefox
          },

          operator: {
            bandwidth: null, // no firefox
            bitrate: null, // no chrome

            width: null,
            height: null,
            framerate: null, // no firefox
          },
        },

        mouse: {
          is_lmb_pressed: false,
          is_over: false,

          screen: {x: 0, y: 0}, // screen
          location: {x: 0, y: 0}, // canvas
          local: {x: 0, y: 0},	// relative canvas (like x = 0.5 for location.x = 320 & width = 640)

          handler: {
            pointer: null,
            draw: null,
            trail: null,

            timeout: 10, // can't fall behind 10 ( https://www.w3schools.com/jsref/met_win_setinterval.asp )
          },

          setLocation: function (
              x,
              y,
              width,
              height,
              screen_x,
              screen_y
          ) {
            if (typeof screen_x === 'undefined') {
              screen_x = null;
            }

            if (typeof screen_y === 'undefined') {
              screen_y = null;
            }

            this.location = {
              x: x,
              y: y,
            };

            this.local = {
              x: (x / width).toFixed(4),
              y: (y / height).toFixed(4),
            };

            if ((screen_x !== null) && (screen_y !== null)) {
              this.screen = {
                x: screen_x,
                y: screen_y
              };
            }
          }
        },
      };
    },

    getIsMicUser(senderId) {
      //console.log('del getIsMicUser '+senderId)
      //console.log('del getIsMicUser '+senderId.replace(/ /g, ''))
      //console.log('del stream.calls[senderId] ', stream.calls[senderId.replace(/ /g, '')])
      try {
        return stream.calls[senderId].isMic
      } catch (e) {
        return false
      }
    },

    isMicUser(senderId, isMic) {
      console.log('isMicUser senderId ' + senderId + ' isMic ' + isMic)
      var nameReplace = senderId
      stream.calls[senderId].isMic = isMic
      if (!isMic) {
        $('#micVideo_' + nameReplace).show()
        $('#micVoiseActive_' + nameReplace).hide()
        $('#iconUserMicVideo_' + nameReplace).removeClass('fa fa-microphone v-btn_ot_ex_sp v-span-video')
        $('#iconUserMicVideo_' + nameReplace).addClass('fa fa-microphone-alt-slash v-btn_ot_ex_sp v-span-video')
        $('#iconUserMicVideo_' + nameReplace).css('color', '#ac4a4a')

      } else {
        $('#micVideo_' + nameReplace).hide()
        $('#micVoiseActive_' + nameReplace).show()

        $('#iconUserMicVideo_' + nameReplace).removeClass('fa fa-microphone-alt-slash v-btn_ot_ex_sp v-span-video')
        $('#iconUserMicVideo_' + nameReplace).addClass('fa fa-microphone v-btn_ot_ex_sp v-span-video')
        $('#iconUserMicVideo_' + nameReplace).css('color', '#404040')

      }
      this.isSoundUser(senderId, false)
    },

    isSoundUser(senderId, isSound) {
      var nameReplace = senderId
      if (isSound) {
        $('#micVoise1_' + nameReplace).removeClass('barCssSoindNoAnimal')
        $('#micVoise2_' + nameReplace).removeClass('barCssSoindNoAnimal')
        $('#micVoise3_' + nameReplace).removeClass('barCssSoindNoAnimal')

        if (This.isSoundWinFunc && This.typeMaket == 2 && This.teamSound && This.mainWin == 0) {

          This.teamSound = false
          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];
            if ('' + keys[i] == senderId) {
              participant.full = true
            } else {
              participant.full = false
            }
          }
          This.resizeWin(15)
          setTimeout(() => {
            This.teamSound = true
          }, 1000)
        }
      } else {
        $('#micVoise1_' + nameReplace).addClass('barCssSoindNoAnimal')
        $('#micVoise2_' + nameReplace).addClass('barCssSoindNoAnimal')
        $('#micVoise3_' + nameReplace).addClass('barCssSoindNoAnimal')
      }
    },

    sendVoiseAverage(average) {
      if (this.isStreamMic) {
        if (average > 15) {
          if (!this.isSound) {
            this.isSound = true
            var message = {
              id: 'isSoundUser',
              room: this.paramValueRoom,
              senderName: this.senderName,
              senderId: this.senderId,
              isSound: true,
            };
            ////console.log('messs ' + message)
            this.sendMessage(message);
          }
        } else {
          if (this.isSound) {
            this.isSound = false
            var message = {
              id: 'isSoundUser',
              room: this.paramValueRoom,
              senderName: this.senderName,
              senderId: this.senderId,
              isSound: false,
            };
            ////console.log('messs ' + message)
            this.sendMessage(message);
          }
        }
      }
    },

    isVideoUser(senderId, isVideo, hasCamera, isMinBit, isVideoMinBit) {
      console.log('isVideoUser()', senderId, isVideo, hasCamera, isMinBit, isVideoMinBit)
      if(isMinBit){
        if(!isVideoMinBit){
          console.log('isVideoUser() отключаем видео, низкий битрейт')
          var participant = stream.calls[senderId]
          $('#video_' + senderId).css("width", '0')
          if (participant.avatar) {
            $('#imgDevIconNoVideo_' + senderId).attr('src', `data:image/jpg;base64,${participant.avatar}`)
            $('#imgDevIconNoVideo_' + senderId).show()
            $('#noImgDevIconNoVideo_' + senderId).hide()

          } else {
            $('#imgDevIconNoVideo_' + senderId).hide()
            $('#noImgDevIconNoVideo_' + senderId).show()
          }
          $('#devDevIconNoVideo_' + senderId).show()
        }else{
          console.log('isVideoUser() включаем видео, нормальный битрейт')
          var participant = stream.calls[senderId]
          This.isVideoUser(senderId, participant.isVideo, true, false, false)
        }
      }else{
        if (isVideo && hasCamera) {
          console.log('isVideoUser() включаем видео, запрос')
          $('#devDevIconNoVideo_' + senderId).hide()
          $('#video_' + senderId).css("width", '100%')
          var participant = stream.calls[senderId]
          stream.calls[senderId].isVideo = true
        }
        else {
          console.log('isVideoUser() отключаем видео, запрос')
          var participant = stream.calls[senderId]
          stream.calls[senderId].isVideo = false

          $('#video_' + senderId).css("width", '0')
          if (participant.avatar) {
            $('#imgDevIconNoVideo_' + senderId).attr('src', `data:image/jpg;base64,${participant.avatar}`)
            $('#imgDevIconNoVideo_' + senderId).show()
            $('#noImgDevIconNoVideo_' + senderId).hide()

          } else {
            $('#imgDevIconNoVideo_' + senderId).hide()
            $('#noImgDevIconNoVideo_' + senderId).show()
          }
          $('#devDevIconNoVideo_' + senderId).show()
        }
      }
    },

    setIsMic() {
      console.log('setIsMic()')
      var participant = stream.calls[this.senderId].peer
      var media_streams = participant.peerConnection.getLocalStreams();
      if (this.isStreamMic) {
        this.isStreamMic = false
        var audio_tracks = media_streams[0].getAudioTracks();
        audio_tracks[0].enabled = this.isStreamMic;

        try {
          STREAM_AUDIO.enabled = this.isStreamMic;
          var audio_tracks2 = STREAM_AUDIO.getAudioTracks();
          if (audio_tracks2[0]) {
            audio_tracks2[0].enabled = this.isStreamMic;
          }
        } catch (e) {
          //console.log(e)
        }

      } else {
        this.isStreamMic = true
        var audio_tracks = media_streams[0].getAudioTracks();
        audio_tracks[0].enabled = this.isStreamMic;
        try {
          STREAM_AUDIO.enabled = this.isStreamMic;
          var audio_tracks2 = STREAM_AUDIO.getAudioTracks();
          if (audio_tracks2[0]) {
            audio_tracks2[0].enabled = this.isStreamMic;
          }
        } catch (e) {
          //console.log(e)
        }
      }

      var message = {
        id: 'isMicUser',
        room: this.paramValueRoom,
        senderName: this.senderName,
        senderId: this.senderId,
        isMic: this.isStreamMic,
      };

      this.sendMessage(message);
    },

    setISVS() {
      var participant = stream.calls[this.senderId].peer
      var media_streams = participant.peerConnection.getLocalStreams();
      if (this.isStreamVideo) {
        this.isStreamVideo = false
        var video_tracks = media_streams[0].getVideoTracks();
        video_tracks[0].enabled = this.isStreamVideo;
      } else {
        this.isStreamVideo = true
        var video_tracks = media_streams[0].getVideoTracks();
        video_tracks[0].enabled = this.isStreamVideo;
      }

      var message = {
        id: 'isVideoUser',
        room: This.paramValueRoom,
        senderName: This.senderName,
        senderId: This.senderId,
        isVideo: This.isStreamVideo,
        hasCamera: This.hasCamera
      };
      console.log('setISVS isVideoUser message', message)
      this.sendMessage(message);
    },

    setIsVideoStreamClick() {
      if (This.isMaxOperVideo) {
        console.log('Обожди isMaxOperVideo')
      } else {
        This.setIsVideoStream()
      }
    },

    setIsVideoStream() {
      console.log('upgradeVideo '+numSession)
      var keys = Object.keys(stream.calls);
      var numSession = keys.length

      var isOper = false
      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];
        if (participant.OperFunc == true) {
          isOper = true
        }
      }

      if (This.hasCamera) {

        if(isOper && numSession > 8){
          console.log("Нельзя включить камеру, есть оператор и много участников!")
          This.isStreamVideo = true
        }

        var participant = stream.calls[This.senderId].peer
        var media_streams = participant.peerConnection.getLocalStreams();
        if (This.isStreamVideo) {
          This.isStreamVideo = false
          try {
            console.log(media_streams)
            console.log(media_streams[0].getVideoTracks())
            console.log(STREAM_VIDEO)
            console.log(STREAM_VIDEO.getVideoTracks()[0].enabled)
            console.log(S_OLD_TREAM_VIDEO_FON)
            console.log(S_OLD_TREAM_VIDEO_FON.getVideoTracks()[0].enabled)
            STREAM_VIDEO.getVideoTracks()[0].enabled = false
            S_OLD_TREAM_VIDEO_FON.getVideoTracks()[0].enabled = false

          } catch (e) {
          }
          var video_tracks = media_streams[0].getVideoTracks();
          // if (This.hasCamera) {
          //   video_tracks[0].enabled = This.isStreamVideo;
          // }
          video_tracks[0].enabled = This.isStreamVideo;

          This.iconUser(true)

        }
        else {
          This.isStreamVideo = true
          var video_tracks = media_streams[0].getVideoTracks();
          // if (This.hasCamera) {
          //   video_tracks[0].enabled = This.isStreamVideo;
          // }
          video_tracks[0].enabled = This.isStreamVideo;
          try {
            console.log(media_streams)
            console.log(media_streams[0].getVideoTracks())
            console.log(STREAM_VIDEO)
            console.log(STREAM_VIDEO.getVideoTracks()[0].enabled)
            console.log(S_OLD_TREAM_VIDEO_FON)
            console.log(S_OLD_TREAM_VIDEO_FON.getVideoTracks()[0].enabled)
            STREAM_VIDEO.getVideoTracks()[0].enabled = true
            S_OLD_TREAM_VIDEO_FON.getVideoTracks()[0].enabled = true

          } catch (e) {
          }

          This.iconUser(false)
        }

        This.isVS_SS = This.isStreamVideo

        var message = {
          id: 'isVideoUser',
          room: This.paramValueRoom,
          senderName: This.senderName,
          senderId: This.senderId,
          isVideo: This.isStreamVideo,
          hasCamera: This.hasCamera
        };
        console.log('setIsVideoStream isVideoUser message', message)
        This.sendMessage(message);
      } else {
        console.log("Клик по кнопке вкл. камеру, когда нет камеры!")
      }
    },

// если подключили камеру без доступа
    setIsVideoStreamHasCamera(b) {
      if (b) {
        This.isStreamVideo = true
        This.iconUser(false)
      } else {
        This.isStreamVideo = false
        This.iconUser(true)
      }

      This.isVS_SS = This.isStreamVideo

      var message = {
        id: 'isVideoUser',
        room: This.paramValueRoom,
        senderName: This.senderName,
        senderId: This.senderId,
        isVideo: This.isStreamVideo,
        hasCamera: This.hasCamera
      };
      This.sendMessage(message);
    },

    iconUser(show) {
      if (!this.isStreamVideo || !this.hasCamera) {
        $('#imgDevIconNoVideoMain').attr('src', `data:image/jpg;base64,${this.avatar}`)
        $('#imgDevIconNoVideoMain1').attr('src', `data:image/jpg;base64,${this.avatar}`)

        if (this.isMyFullVideo) {
          if (this.avatar) {
            // $('#imgDevIconNoVideoMain1').css('width', '8vw')
            // $('#imgDevIconNoVideoMain1').css('height', '8vw')
            // $('#imgDevIconNoVideoMain1').css('margin-top', '0')

            $('#imgDevIconNoVideoMain1').addClass('isShowRightPanel')
            $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
            $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
            $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

            $('#imgDevIconNoVideoMain1').removeClass('isHideRightPanel')
            $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
            $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
            $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
          } else {
            // $('#noImgDevIconNoVideoMain1').css('width', '8vw')
            // $('#noImgDevIconNoVideoMain1').css('height', '8vw')
            // $('#noImgDevIconNoVideoMain1').css('margin-top', '0')
            $('#noImgDevIconNoVideoMain1').css('font-size', '34px')
            $('#noImgDevIconNoVideoMain1').css('padding-top', '10px')
            $('#noImgDevIconNoVideoMain1').css('margin-left', '0')

            $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
            $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
            $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
            $('#noImgDevIconNoVideoMain1').addClass('isShowRightPanel')

            $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
            $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
            $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
            $('#noImgDevIconNoVideoMain1').removeClass('isHideRightPanel')
          }
        } else {
          if (this.typeMaket == 3) {
            if (this.avatar) {
              // $('#imgDevIconNoVideoMain').css('width', '8vw')
              // $('#imgDevIconNoVideoMain').css('height', '8vw')
              // $('#imgDevIconNoVideoMain').css('margin-top', '0')

              $('#imgDevIconNoVideoMain1').addClass('isShowRightPanel')
              $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

              $('#imgDevIconNoVideoMain').removeClass('isHideRightPanel')
              $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
            } else {
              // $('#noImgDevIconNoVideoMain').css('width', '8vw')
              // $('#noImgDevIconNoVideoMain').css('height', '8vw')
              // $('#noImgDevIconNoVideoMain').css('margin-top', '0')
              $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
              $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain1').addClass('isShowRightPanel')

              $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
              $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain').removeClass('isHideRightPanel')
            }
          } else {
            if (this.avatar) {
              // $('#imgDevIconNoVideoMain1').css('width', '8vw')
              // $('#imgDevIconNoVideoMain1').css('height', '8vw')
              // $('#imgDevIconNoVideoMain1').css('margin-top', '0')
              // $('#imgDevIconNoVideoMain1').css('margin-right', '62%')
              $('#imgDevIconNoVideoMain1').addClass('isShowRightPanel')
              $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

              $('#imgDevIconNoVideoMain1').removeClass('isHideRightPanel')
              $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
            } else {
              // $('#noImgDevIconNoVideoMain1').css('width', '8vw')
              // $('#noImgDevIconNoVideoMain1').css('height', '8vw')
              // $('#noImgDevIconNoVideoMain1').css('margin-top', '0')
              $('#noImgDevIconNoVideoMain1').css('font-size', '20px')
              $('#noImgDevIconNoVideoMain1').css('padding-top', '0')
              $('#noImgDevIconNoVideoMain1').css('margin-left', '0')
              $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
              $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
              $('#noImgDevIconNoVideoMain1').addClass('isShowRightPanel')

              $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
              $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
              $('#noImgDevIconNoVideoMain1').removeClass('isHideRightPanel')
            }
          }
        }
      } else {
        $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
        $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

        $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
        $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
      }
    },

    confirmAgree(type, mess) {
      if (type == 1) {
        This.sendMessage(mess);
      } else if (type == 2) {
        This.sendMessage(mess);
      } else if (type == 5) {
        This.muteAll()
      } else if (type == 6) {
        This.onParticipantLeftName(mess.senderId, mess.senderName)
        This.sendMessage(mess);
      } else if (type == 7) {
        This.startSaveSession()
      }
    },

    confirmCancel() {

    },

    addIceCandidat(parsedMessage) {
      //console.log('del addIceCandidat ', parsedMessage)
      stream.calls[parsedMessage.name].peer.addIceCandidate(parsedMessage.candidate, function (error) {
        if (error) {
          console.error("Error adding candidate: " + error);
          return;
        }
      });
    },

    setMicToUser(parsedMessage) {
      if (parsedMessage.userClose) {
        This.$root.showInfo(parsedMessage.userClose + ' ' + This.$t('reject.call.info.mute'))
      }
      This.isStreamMic = true
      This.setIsMic()
    },

    attachSinkId(element, sinkId) {
      if (typeof element.sinkId !== 'undefined') {
        element.setSinkId(sinkId)
            .then(() => {
              ////console.log(`Success, audio output device attached: ${sinkId}`);
            })
            .catch(error => {
              let errorMessage = error;
              if (error.name === 'SecurityError') {
                errorMessage = `You need to use HTTPS for selecting audio output device: ${error}`;
              }
              console.error(errorMessage);
              // Jump back to first output device in the list as it's the default.
            });
      } else {
        console.warn('Browser does not support output device selection.');
      }
    },

    // для изменения названия кнопки, если есть демонстрация
    setMouseoverSS() {
      if (this.isScreenSharing) {
        this.isTextBtnSS = true
      }
    },

    clickItemMore(item) {

      //console.log('type')
      //console.log(type.type)

      this.isMoreWin = false

      if (item.type == 1) {// на весь экран
        this.fullWindows()
      } else if (item.type == 2) {// диалог с настройками
        if (this.isFirefox) {
          This.$refs.refConfirm.open(this.$t('gc.isallmute.dialog'),
              this.$t('gc.isallmute.dialog.mess'),
              5, {color: 'warning'}, '')
        } else {
          this.$emit('dialogSettCall', '')
        }

      } else if (item.type == 3) {// отключить микрофон всех участников
        This.$refs.refConfirm.open(this.$t('gc.isallmute.dialog'),
            this.$t('gc.isallmute.dialog.mess'),
            5, {color: 'warning'}, '')
      } else if (item.type == 4) {
        if (This.isSaveCall) {
          This.$root.showInfo('Нельзя включить запись, если кто-то из участников уже ее включил!')
        } else {
          This.$refs.refConfirm.open(this.$t('gc.saveSession.dialog'),
              this.$t('gc.saveSession.dialog.mess'),
              7, {color: 'warning'}, '')
        }
      } else if (item.type == 5) {//включить режим разработчика
        this.devGC = !this.devGC
        if (this.devGC) {
          item.text = this.$t('gc.group.menu.52')
          this.itemsMoreWin = [...this.itemsMoreWin, {text: this.$t('gc.group.menu.6'), icon: 'fab fa-dev', type: 6}]
        } else {
          item.text = this.$t('gc.group.menu.5')
          console.log('this.itemsMoreWin.findIndex(item => item.type == 6)', this.itemsMoreWin.findIndex(item => item.type == 6))
          this.itemsMoreWin.splice(this.itemsMoreWin.findIndex(item => item.type == 6), 1); // начиная с индекса 1, удалить 1 элемент
        }
      } else if (item.type == 6) {
        This.devTestSession = true
        This.testUserWin++
        This.testReceiveVideo('testWinVideo' + This.testUserWin, This.senderId + 4000 + This.testUserWin, false, false, true, false)
      } else if (item.type == 7) {
        this.isTranslation = !this.isTranslation
        this.$emit('setTranslation', this.isTranslation)
        if (this.isTranslation) {
          this.setLocalStorage('gc.isTranslation', '1')
        } else {
          this.setLocalStorage('gc.isTranslation', '2')
        }


        if (this.isTranslation) {
          item.text = this.$t('gc.group.menu.72')
          this.itemsMoreWin = [...this.itemsMoreWin, {
            text: This.auPlayMenuText,
            icon: This.auPlayMenuIcon,
            type: This.auPlayMenuType
          }]
        } else {
          item.text = this.$t('gc.group.menu.7')
          this.itemsMoreWin = this.itemsMoreWin.filter(function (obj) {
            return obj.type !== This.auPlayMenuType;
          });
        }
      } else if (item.type == 10) {
        console.log('item.type == 10')
        This.isAudioPlayATSender = 1
        localStorage["gc.isAudioPlayATSender"] = This.isAudioPlayATSender
        item.text = This.$t('gc.group.menu.11')
        This.auPlayMenuText = This.$t('gc.group.menu.11')
        item.icon = 'fas fa-volume-down'
        This.auPlayMenuIcon = 'fas fa-volume-down'
        item.type = 11
        This.auPlayMenuType = 11
        This.stopTranlsateResultAudio()
        console.log('item 11', localStorage["gc.isAudioPlayATSender"])
      } else if (item.type == 11) {
        console.log('item.type == 11')
        This.isAudioPlayATSender = 0
        localStorage["gc.isAudioPlayATSender"] = This.isAudioPlayATSender
        item.text = This.$t('gc.group.menu.10')
        This.auPlayMenuText = This.$t('gc.group.menu.10')
        item.icon = 'fas fa-volume-mute'
        This.auPlayMenuIcon = 'fas fa-volume-mute'
        item.type = 10
        This.auPlayMenuType = 10
        console.log('item 10', localStorage["gc.isAudioPlayATSender"])
      }
    },

    // на весь экран пока не сделал до конца, нужно еще доделать закрытие полного режима
    fullWindows() {
      document.documentElement.requestFullscreen();
      // console.log('gkgkgglglf')
      //
      // This.isFullScreen = true
      //
      // this.$emit('setFullScreen', This.isFullScreen);
      //Todo сюда надо показ кнопки.
      /**
       * Далее написать функцию для обработки клика по кнопке
       * по клику скрываем/показываем две нижние панели
       *
       * в GCMain сделать watch за размером массива, показывать наш элемент, если он  > 0
       * сделать ещё один класс в стиле, один с hidden, другой - visible
       */

      // setTimeout(()=>{
      //   var f = document.documentElement.fullscreen
      //   if(document.fullscreenEnabled){
      //     if(document.documentElement.cancelFullScreen) {
      //       document.documentElement.cancelFullScreen();
      //     } else if(document.documentElement.mozCancelFullScreen) {
      //       document.documentElement.mozCancelFullScreen();
      //     } else if(document.documentElement.webkitCancelFullScreen) {
      //       document.documentElement.webkitCancelFullScreen();
      //     }
      //   }else{
      //     document.documentElement.requestFullscreen();
      //   }
      // },100)
    },

    /**
     * Функция сокрытия/показа панелей
     */
    showHideButtonsPanel(){
      console.log(This.isPanelShow, 'This.isPanelShow from showHideButtonsPanel')
      if (!This.isPanelShow){
        // $('.lowButtonsPanel').hide()
        document.querySelector('.lowButtonsPanel').style.visibility = 'hidden';

        this.updateStorage()

        This.isPanelShow = true
      }
      else {
        // $('.lowButtonsPanel').show()
        document.querySelector('.lowButtonsPanel').style.visibility = 'visible';

        this.updateStorage()

        This.isPanelShow = false


      }
    },

    // диалог с настройками

    // отключить микрофон всех участников
    muteAll() {
      var keys = Object.keys(stream.calls);
      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];

        if (participant && participant.senderId != This.senderId && participant.isMic) {
          var message = {
            id: 'isMicToUser',
            room: This.paramValueRoom,
            senderName: participant.senderName,
            senderId: participant.senderId,
            userClose: This.senderName
          };
          this.sendMessage(message)
        }
      }
    },

    newAudioEnded() {
      console.log('newAudioEnded')
      This.$root.showInfo(This.$t('reject.call.info.noFunc3'))

      var audioInputSelect = []
      navigator.mediaDevices.enumerateDevices()
          .then(function (devices) {
            devices.forEach(function (device) {
              ////console.log('___ device.kind '+device.kind)
              if (device.kind == 'audioinput') {
                audioInputSelect.push({text: device.label, value: device.deviceId})
              }
            });
          })
          .then(() => {
            console.log('AUDIO newAudioEnded this.audioInputValue = ',audioInputSelect[0])
            This.audioInputValue = audioInputSelect[0]

            var constraints2 = {
              audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
              video: false,
            }
            if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
              navigator.mediaDevices.getUserMedia(constraints2).then(function (stream) {
                STREAM_AUDIO = stream
                This.saveSettingsCall(This.audioInputValue, This.audioOutputValue, This.videoValue, STREAM_VIDEO, STREAM_AUDIO)
              });
            }

          })
    },

    saveSettingsCall(update, _audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue) {
      console.log('roomcallwin saveSettingsCall update ', update)
      console.log('roomcallwin ', _audioInputValue, _audioOutputValue, _videoValue, _videoQualtiySendValue, _videoQualtiyRecvValue)

      if (update[0]) {
        console.log('roomcallwin AUDIO saveSettingsCall this.audioInputValue = ',_audioInputValue)
        this.audioInputValue = _audioInputValue
        this.audioInputUpdate()
      }

      if (update[1]) {
        console.log('roomcallwin AUDIO saveSettingsCall this.audioOutputValue = ',_audioOutputValue)
        this.audioOutputValue = _audioOutputValue
        this.audioOutputUpdate(This.senderId)
      }

      if (update[2]) {
        this.videoValue = _videoValue
        This.stopStreamLocalVideo()
        let senders = stream.calls[This.senderId].peer.peerConnection.getSenders();

        var constraints = {
          video: {
            optional: [
              {sourceId: this.videoValue}
            ]
          },
          audio: false,
        }

        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia(constraints)
              .then(function (stream) {
                STREAM_VIDEO = stream

                senders.forEach((sender) => {
                  if (sender.track && (sender.transport.state === 'connected')) {
                    if (sender.track.kind == 'video') {

                      if (STREAM_VIDEO != null) {
                        sender.track.stop
                        // const constraints = {
                        //   width: {min: 640, ideal: 1280},
                        //   height: {min: 320, ideal: 720},
                        //   advanced: [
                        //     {width: 1240, height: 720},
                        //     {aspectRatio: 1.333}
                        //   ]
                        // };
                        const constraints = {
                          width: 640,
                          height: 320,
                          frameRite: 15,
                          advanced: [
                            {width: 640, height: 320, frameRite: 15},
                            {aspectRatio: 1.333}
                          ]
                        };

                        STREAM_VIDEO.getVideoTracks()[0].applyConstraints(constraints)
                            .then(() => {
                              sender.replaceTrack(STREAM_VIDEO.getVideoTracks()[0]);
                              $(`#video_` + This.senderId)[0].srcObject = STREAM_VIDEO;
                            })
                            .catch(e => {
                              console.error(e)
                            });
                      }
                      // }

                    }
                    else if (sender.track.kind == 'audio') {
                      // if (_STREAM_AUDIO != null) {
                      //   sender.replaceTrack(_STREAM_AUDIO.getAudioTracks()[0]);
                      // }
                    }
                  }
                });
              })
              .catch(function (err) {
                console.log('catch no camera ', err)
                senders.forEach((sender) => {
                  if (sender.track && (sender.transport.state === 'connected')) {
                    if (sender.track.kind == 'audio') {
                      // if (_STREAM_AUDIO != null) {
                      //   console.log('new audio track no CAMERA ', _STREAM_AUDIO.getAudioTracks()[0])
                      //   sender.replaceTrack(_STREAM_AUDIO.getAudioTracks()[0]);
                      // }
                    }
                  }
                });
                // STREAM_AUDIO = _STREAM_AUDIO
              });
        }
      }

      if (update[3] || update[4]) {
        console.log('нужно поменять качество видео')
      }
    }
    ,

    addAudioTrack() {
      navigator.getUserMedia = navigator.getUserMedia ||
          navigator.webkitGetUserMedia ||
          navigator.mozGetUserMedia;
      if (navigator.getUserMedia) {
        navigator.getUserMedia({
              audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]}
            },
            function (stream) {
              ////console.log('___stream___')

              STREAM_AUDIO = stream
              // end fn stream
            },
            function (err) {
              ////console.log("The following error occured: " + err.name)
            });
      } else {
        ////console.log("getUserMedia not supported");
      }
    },

    // запуск или изменение в микрофоне
    audioInputUpdate() {

      console.log('запуск или изменение в микрофоне',This.audioInputValue);
      let senders = stream.calls[This.senderId].peer.peerConnection.getSenders();

      var constraints = {
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
        video: false,
      }

      navigator.getUserMedia = navigator.getUserMedia ||
          navigator.webkitGetUserMedia ||
          navigator.mozGetUserMedia;
      if (navigator.getUserMedia) {
        navigator.getUserMedia(constraints,
            function (stream) {

              STREAM_AUDIO = stream

              senders.forEach((sender) => {
                if (sender.track && (sender.transport.state === 'connected')) {
                  if (sender.track.kind == 'audio') {
                    if (STREAM_AUDIO != null) {
                      console.log('Замена аудио дорожки и удаление старой')
                      sender.replaceTrack(STREAM_AUDIO.getAudioTracks()[0]);
                    }
                  }
                }
              });

              console.log('AEUDIO audioInputUpdate',stream)
              stream.getAudioTracks()[0].addEventListener("unmute", event => {
                console.log('AEUDIO устройство микрофон вкл.')
              }, false);
              stream.getAudioTracks()[0].addEventListener("mute", event => {
                console.log('AEUDIO устройство микрофон выкл')
              }, false);
              stream.getAudioTracks()[0].addEventListener("ended", event => {
                console.log('AEUDIOустройство звука удалили')
                This.newAudioEnded()
              }, false);
              stream.getAudioTracks()[0].addEventListener("isolationchange", event => {
                console.log('AEUDIO 521')
              }, false);
              // sender.track.onended = () => navigator.mediaDevices.ondevicechange = tryAgain;

              var audioContext = new AudioContext();
              var analyser = audioContext.createAnalyser();
              var microphone = audioContext.createMediaStreamSource(stream);
              var javascriptNode = audioContext.createScriptProcessor(2048, 1, 1);

              analyser.smoothingTimeConstant = 0.8;
              analyser.fftSize = 1024;

              microphone.connect(analyser);
              analyser.connect(javascriptNode);
              javascriptNode.connect(audioContext.destination);

              javascriptNode.onaudioprocess = function () {
                var array = new Uint8Array(analyser.frequencyBinCount);
                analyser.getByteFrequencyData(array);
                var values = 0;

                var length = array.length;
                for (var i = 0; i < length; i++) {
                  values += (array[i]);
                }

                var average = values / length;
                This.sendVoiseAverage(average)

              } // end fn stream
            },
            function (err) {
              console.error(err)
            });
      } else {
        console.error('error audio')
      }
    },

    audioOutputUpdate(_senderId) {
      This.attachSinkId($('#video_' + _senderId)[0], this.audioOutputValue)
    },

    stopAudioFunc() {
      ////console.log('Stopping local stream');

      window.stream.getTracks().forEach(track => track.stop());
      window.soundMeter.stop();
      clearInterval(meterRefresh);
      // instantMeter.value = instantValueDisplay.innerText = '';
      // slowMeter.value = slowValueDisplay.innerText = '';
      // clipMeter.value = clipValueDisplay.innerText = '';
    },

    getOrient(senderId) {
      var participant = stream.calls[senderId];
      //console.log(participant.orient)
      return participant.orient
    },

    setDelta(senderId, delta) {
      var participant = stream.calls[senderId];
      participant.delta = delta
    },

    getDelta(senderId) {
      var participant = stream.calls[senderId];
      //console.log(participant.delta)
      return participant.delta
    },

    // emit отобразить доп функции для оператора
    showFuncOper(sender) {
      if (this.numScreenSharinWin > 0) {
        // this.screenSharingClose() //- закрыть активное расшаривание
        This.$root.showInfo(This.$t('reject.call.info.noFunc12'))
      } else {
        var participantSender = stream.calls[sender];


        var keys = Object.keys(stream.calls);
        for (var i = 0; i < keys.length; ++i) {
          var participant = stream.calls['' + keys[i]];

          if (participant && participant.scrs) {
            var message = {
              id: 'leaveRoomPr',
              room: This.paramValueRoom,
              senderName: participant.senderName,
              senderId: participant.senderId,
              userClose: This.senderName
            };
            this.sendMessage(message)
          }
        }

        if (this.isShowFuncOther && !this.isShowFunc) {
          This.$root.showInfo(This.$t('reject.call.info.noFunc4'))
        } else {
          //console.log(This.isSenderFuncOther)

          if (!This.isSenderFuncOther) {
            console.log('____participantSender.isRealFuncOper = true')
            participantSender.isRealFuncOper = true
            participantSender.isChekList = true
            console.log('_________________________ ')
            console.log('_________________________ participant.isChekList',participantSender.isChekList)

            This.isSenderFuncOther = sender
            this.$emit('showFuncOper', sender)
          } else if (sender == This.isSenderFuncOther) {
            console.log('____participantSender.isRealFuncOper = false')
            participantSender.isRealFuncOper = false
            //console.log('2 This.isSenderFuncOther')
            this.$emit('showFuncOper', sender)
            This.isSenderFuncOther = null
          } else {
            this.$root.showInfo(This.$t('reject.call.info.noFunc11'))
          }
        }
      }
    },
    //endregion Пока не распределил ----------------------------------------------------------------------------------

    //region Сохранение видео
    startSaveSession() {
      // this.testREC()

      this.$refs.refSaveVideo.init(This.paramValueRoom, This.senderId, STREAM_AUDIO_SAVE)
    },

    stopSaveCall() {
      this.$refs.refSaveVideo.closeCall()
    },

    saveClose() {
      this.isSaveCall = false
      this.isSaveCallMy = false

      var message = {
        id: 'saveCall',
        is: false,
        senderId: this.senderId,
        senderName: this.senderName,
        room: this.paramValueRoom
      };
      this.sendMessage(message)

      if (this.hasCamera) {
        var participant = stream.calls[This.senderId].peer
        var media_streams = participant.peerConnection.getLocalStreams();
        var video_tracks = media_streams[0].getVideoTracks();
        video_tracks[0].applyConstraints({
          width: 1240,
          height: 720,
          frameRate: 12
        });
      }
    },

    saveSecondClose(userName) {
      this.isSaveCallMy = false
      this.$root.showInfo('Функция отклонена сервером. ' + userName + ' включил запись экрана раньше Вас!')
    },

    startSave() {
      this.isSaveCall = true
      this.isSaveCallMy = true

      var message = {
        id: 'saveCall',
        is: true,
        senderName: this.senderName,
        senderId: this.senderId,
        room: this.paramValueRoom
      };
      this.sendMessage(message)
    },

    setStatusSaveCall(is) {
      if (is) {
        this.isSaveCall = true
      } else {
        this.isSaveCall = false
        this.isSaveCallMy = false
      }
    },

    statusSaveCall(message) {
      This.setStatusSaveCall(message.is)
    },
    //endregion

    //region Расшаривание экрана
    setAddFuncScreenSharing() {
      if (!this.isPaint && this.mainWin < 2 && !this.isShowFuncOther) {
        if (!this.isScreenSharing) {
          if (this.numScreenSharinWin > 0) {
            This.$refs.refConfirm.open(this.$t('gc.isSS.dialog'),
                this.$t('gc.isSS.dialog.mess'),
                4, {color: 'warning'}, '')
          } else {
            this.isScreenSharing = !this.isScreenSharing
            this.$refs.resRoomXR.init(This.paramValueRoom, 'scrsXR_' + This.senderName, This.typeBrowser, This.serverUrl, This.isDevUrl)
            // if (this.isDegradation && this.isVS_SS) {
            //   this.isStreamVideo = true
            //   this.setISVS()
            // }
          }
        } else {
          this.isScreenSharing = false
          this.numScreenSharinWin = 0
          this.$refs.resRoomXR.closeCall()
          // if (this.isDegradation && this.isVS_SS) {
          //   this.isStreamVideo = false
          //   this.setISVS()
          // }
        }
      } else {
        if (this.isShowFuncOther) {
          this.$root.showInfo(This.$t('reject.call.info.noFunc1'))
        } else {
          this.$root.showInfo(This.$t('reject.call.info.noFunc2'))
        }

      }
    },

    screenSharingClose() {
      this.$refs.resRoomXR.closeCall()
      this.isScreenSharing = false
      this.numScreenSharinWin = 0
      // if (this.isDegradation && this.isVS_SS) {
      //   this.isStreamVideo = false
      //   this.setISVS()
      // }
      var keys = Object.keys(stream.calls);
      var numSession = keys.length
      if (numSession == 1) {
        this.typeMaket = 1
        this.setTypeWin(this.typeMaket, false, true)//если одна сессия закрыть расшаривание экрана свое
        setTimeout(() => {
          this.resizeWin(104)
        }, 400)
      } else {
        this.setTypeWin(-1)//закрыть расшаривание экрана свое
      }
    },

    // закрыть все расшаривания и открыть свою
    closeSSAndScrS() {
      var keys = Object.keys(stream.calls);
      for (var i = 0; i < keys.length; ++i) {
        var participant = stream.calls['' + keys[i]];

        if (participant && participant.scrs) {
          var message = {
            id: 'leaveRoomPr',
            room: This.paramValueRoom,
            senderId: participant.senderId,
            senderName: participant.senderName,
            userClose: This.senderId
          };
          this.sendMessage(message)
        }
      }

      this.isScreenSharing = false
      this.numScreenSharinWin = 0

      this.$refs.resRoomXR.init(This.paramValueRoom, 'scrsXR_' + This.senderName, This.typeBrowser, '')
      // if (this.isDegradation && this.isVS_SS) {
      //   this.isStreamVideo = true
      //   this.setISVS()
      // }
    },
    //endregion Расшаривание экрана-------------------------------------------------------------------------------------

    //region Доп. функции оператора (включение интерфейса)

    //region сообщение от пользоватлей что они включили функции оператора
    setFuncOperOther(is) {
      if (is != this.isShowFuncOther && is == false) {
        try {
          if (this.otherCTX) {
            this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
          }
        } catch (e) {

        }
        this.isPaint = false

        setTimeout(() => {
          var keys = Object.keys(stream.calls);
          if (keys.length == 1) {
            this.typeMaket = 1
          } else {
            This.setTypeWin(-1, false, true)//пользоватлей что они выключили функции оператора
          }
        }, 500)

        this.mainWin = 0
        This.setTextMainWin(0)
        this.isShowFuncOther = false
        var canvas = $('#idCanvasPrintOther')
        canvas.hide()
        $('#idDivCanvasOther').append(canvas);
        this.otherCTX = null
      }
    },

    funcOperOther(message) {
      console.log('message == undefined ', message)
      var isStatusFuncOper = false
      if (message.senderIdoper != 0) {
        var participant = stream.calls[message.senderIdoper];
        console.log('funcOperOther participant', participant)
        if (participant) {
          isStatusFuncOper = true
        }
      }

      if (isStatusFuncOper) {

        var participant = stream.calls[message.senderIdoper];
        participant.isRealFuncOper = message.status
        console.log('gcA', message.status)


        if (message.status) {
          console.log(message.senderName + ' включил функции управления оператором')
          This.setChatMessage(false)
          This.setPanelInfoUser(false)

          This.oldsetTypeWin = This.typeMaket
          this.startFuncOperOther(message.senderIdoper)
          This.setTextMainWin(4, message.senderName)
        } else {
          try {
            if (this.otherCTX) {
              this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
            }
          } catch (e) {

          }
          this.isPaint = false

          setTimeout(() => {
            var keys = Object.keys(stream.calls);
            if (keys.length == 1) {
              this.typeMaket = 1
            } else {
              This.setTypeWin(-1, false, true)//funcOperOther
            }
          }, 500)

          this.mainWin = 0
          This.setTextMainWin(0)
          this.isShowFuncOther = false
          var canvas = $('#idCanvasPrintOther')
          canvas.hide()
          $('#idDivCanvasOther').append(canvas);
          this.otherCTX = null
        }
      } else {
        console.log('funcOperOther isStatusFuncOper false, закрываем управление')
        try {
          if (this.otherCTX) {
            this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
          }
        } catch (e) {

        }
        this.isPaint = false
        this.mainWin = 0
        This.setTextMainWin(0)

        console.log('isShowFunc ', This.isShowFunc)
        if (This.isShowFunc) {
          This.$emit('closeFuncOper')
        }

        this.isShowFuncOther = false
        var canvas = $('#idCanvasPrintOther')
        canvas.hide()
        $('#idDivCanvasOther').append(canvas);
        this.otherCTX = null
      }

      This.updateDataOper(5)
    },
    //endregion

    startFuncOperOther(senderId) {
      console.log('__________startFuncOperOther_______________ ')

      if (This.mainVideoPosition(senderId, true, 1)) {
        setTimeout(() => {
          console.log('__________startFuncOperOther2______________ ')
          This.isShowFuncOther = true

          var participant = stream.calls[senderId];
          participant.isRealFuncOper = true
          console.log('_________________________ ')
          console.log('_______participant.isRealFuncOper = true__________________ ')

          var v = document.getElementById('video_' + senderId);
          This.wCanvas = v.offsetWidth;
          This.hCanvas = Math.floor(This.wCanvas * (9 / 16));

          var canvas = $('#idCanvasPrintOther')
          if (document.getElementById('idCanvasPrintOther')) {
            console.log('norm')
          } else {
            alert('ne norm')
            console.log('ne norm')
            canvas = `<canvas id="idCanvasPrintOther" class="canvasPrintOther"
              :width="${This.wCanvas}" :height="${This.hCanvas}"></canvas>`
            $('#idDivCanvasOther').append(canvas);
          }

          console.log('canvas ', canvas)

          $('#devVideo_' + senderId).append(canvas);

          canvas.css('height', This.hCanvas + 'px')
          canvas.css('width', This.wCanvas + 'px')
          canvas.css('top', 'calc(50% - ' + This.hCanvas / 2 + 'px)')
          canvas.show()

          console.log('__________startFuncOperOther__3_____________ ')

          this.otherCTX = document.getElementById('idCanvasPrintOther').getContext('2d');
          console.log('__________startFuncOperOther__4____________ ', this.otherCTX)
          //console.log('then __')
        }, 1000)
      } else {
        console.error('mainVideoPosition false')
      }

      This.updateDataOper(4)
    },


    closeAllFuncOper(message) {
      this.isShowFuncOther = false
      This.setTextMainWin(1)
      this.isSenderFuncOther = null
      var participant = stream.calls[message.senderId];
      participant.isRealFuncOper = false
      console.log('_________________________ ')
      console.log('_______participant.isRealFuncOper = false__________________ ', message.senderId)
      if (this.mainWin > 1) {
        this.$emit('showFuncOper', message.senderId)
      }
      This.updateDataOper(3)
    },

    funcUpdateIconMenu(isShowFunc, senderId) {
      console.log('funcUpdateIconMenu ' + isShowFunc + ' senderId ' + senderId)
      This.isShowFunc = isShowFunc

      var participant = stream.calls[senderId];
      participant.isRealFuncOper = isShowFunc

      if (this.isShowFunc) {
        This.mainVideoPosition(senderId, true, 2)
        //--- отправка пользовалелям что вкл/ выкл функции оператора
        // ---
      } else {
        This.mainVideoPosition(senderId, true, 1)
      }

      var spanImenu = $('#iconOperFunc_' + senderId)
      var spanImenu1 = $('#iconOperFunc1_' + senderId)
      var spanImenu2 = $('#iconOperFunc2_' + senderId)


      if (isShowFunc) {
        spanImenu.css('color', 'red')
        spanImenu1.show()
        spanImenu2.show()
      } else {
        spanImenu.css('color', '#636363')
        spanImenu1.hide()
        spanImenu2.hide()
        This.isSenderFuncOther = null
      }

      var message = {
        id: 'funcOperOther',
        room: This.paramValueRoom,
        senderId: This.senderId,
        senderName: This.senderName,
        senderIdoper: senderId,
        status: isShowFunc
      }
      this.sendMessage(message)
      This.updateDataOper(2)
    },
    //endregion Дополнительные функции-------------------------------------------------------------------------------------

    // region Функции рисования у оператора

    setArrow(_isArrow, senderId) {
      if (_isArrow) {
        this.setCloseRightPanel()
      }
    },

    setPaint(_isPaint, senderId) {
      //console.log('setPaint &')
      this.isPaint = _isPaint

      if (this.isPaint) {
        this.setCloseRightPanel()
      }

      if (_isPaint) {
        This.mainVideoPosition(senderId, true, 3)
      } else {
        This.mainVideoPosition(senderId, true, 2)
      }

      var keys = Object.keys(stream.calls);
      for (var i = 0; i < keys.length; ++i) {
        if ('' + keys[i] == senderId && _isPaint) {
          var participant_obj = stream.calls['' + keys[i]]
          participant_obj.isPaint = true
          //console.log('setPrint true ' + '' + keys[i])
        } else {
          var participant_obj = stream.calls['' + keys[i]]
          participant_obj.isPaint = false
        }
      }

      var message = {
        id: 'drawMode',
        room: This.paramValueRoom,
        senderName: This.senderName,
        senderId: This.senderId,
        idTo: senderId,
        status: _isPaint
      }
      this.sendMessage(message)
    },

    clearCanvas(message) {
      this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
    },

    drawModeMess(message) {
      this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
    },

    /**region дублирование рисования у оператора*/
    moveDrawMess(message) {
      console.log('moveDrawMess', message)
      console.log(this.wCanvas)
      console.log(this.otherCTX)
      if (message.status === 'on') {

        if (this.otherX == -100 && this.otherY == -100) {
          this.otherX = message.x * this.wCanvas
          this.otherY = message.y * this.hCanvas

          this.otherCTX.beginPath();
        } else {
          this.otherCTX.lineWidth = message.pointer_width; // толщина линии
          this.otherCTX.strokeStyle = message.pointer_color;
          this.otherCTX.moveTo(this.otherX, this.otherY); //передвигаем перо
          this.otherCTX.lineTo(message.x * this.wCanvas, message.y * this.hCanvas); //рисуем линию
          this.otherCTX.stroke();

          this.otherX = message.x * this.wCanvas
          this.otherY = message.y * this.hCanvas
        }
      } else {
        this.otherCTX.lineTo(message.x * this.wCanvas, message.y * this.hCanvas); //рисуем линию
        this.otherCTX.stroke();

        this.otherX = -100
        this.otherY = -100
      }
    },
    /**region дублирование рисования стрелки у оператора*/
    arrowDrawMess(message) {
      console.log('arrowDrawMess', message)
      this.otherCTX.lineWidth = message.pointer_width; // толщина линии
      this.otherCTX.strokeStyle = message.pointer_color;
      this.otherCTX.clearRect(0, 0, This.wCanvas, This.hCanvas)
      if (message.type == 1) {
        // This.drawFilledArrow(this.otherCTX, message.x1* this.wCanvas, message.y1* this.hCanvas, message.x2* this.wCanvas, message.y2* this.hCanvas,  message.arrow, message.pointer_color, message.pointer_width)
        This.drawArrowNew(this.otherCTX, message.x1 * this.wCanvas, message.y1 * this.hCanvas, message.x2 * this.wCanvas, message.y2 * this.hCanvas, message.pointer_width, message.pointer_color)
      } else {
        // This.drawEmptyArrow(this.otherCTX, message.x1* this.wCanvas, message.y1* this.hCanvas, message.x2* this.wCanvas, message.y2* this.hCanvas,  message.arrow, message.pointer_color, message.pointer_width)
        This.drawArrowNew(this.otherCTX, message.x1 * this.wCanvas, message.y1 * this.hCanvas, message.x2 * this.wCanvas, message.y2 * this.hCanvas, message.pointer_width, message.pointer_color)
      }
    },


    drawArrowNew(ctx, x0, y0, x1, y1, _width, color) {
      let width = _width * 2
      let head_width = width * 3
      let head_length = width * 3

      console.log('drawArrowNew ', x0, y0, x1, y1, width, color, head_width, head_length)

      // сначала вычислите длину
      const length = Math.sqrt((x1 - x0) * (x1 - x0) + (y1 - y0) * (y1 - y0))
      let angle = Math.atan2(y1 - y0, x1 - x0);
      // отрегулируйте угол на 90 градусов, так как стрелка, которую мы поворачиваем, повернута на 90 градусов
      angle -= Math.PI / 2;

      let p0 = [x0, y0];

      // порядок будет: p1 -> p3 -> p5 -> p7 -> p6 -> p4 -> p2
      // сформулируйте две базовые точки
      let p1 = [x0 + width / 4, y0];
      let p2 = [x0 - width / 4, y0];

      // верхние базовые точки, которые соединяют заостренный конец с длинной стрелки
      let p3 = [x0 + width / 2, y0 + length - head_length];
      let p4 = [x0 - width / 2, y0 + length - head_length];

      //внешние точки треугольника
      let p5 = [x0 + head_width / 2, y0 + length - head_length - 5];
      let p6 = [x0 - head_width / 2, y0 + length - head_length - 5];

      // конечная точка стрелки
      let p7 = [x0, y0 + length];

      p1 = this.transform(p1, angle, p0);
      p2 = this.transform(p2, angle, p0);
      p3 = this.transform(p3, angle, p0);
      p4 = this.transform(p4, angle, p0);
      p5 = this.transform(p5, angle, p0);
      p6 = this.transform(p6, angle, p0)
      p7 = this.transform(p7, angle, p0);

      // начать сначала.
      // context.moveTo(p1[0], p1[1]);
      ctx.lineCap = 'round';
      ctx.fillStyle = color;
      ctx.beginPath();
      // отрисовка линий
      ctx.moveTo(p1[0], p1[1]);
      ctx.lineTo(p3[0], p3[1]);
      ctx.lineTo(p5[0], p5[1]);
      ctx.lineTo(p7[0], p7[1]);
      ctx.lineTo(p6[0], p6[1]);
      ctx.lineTo(p4[0], p4[1]);
      ctx.lineTo(p2[0], p2[1]);
      ctx.lineTo(p1[0], p1[1]);
      ctx.closePath();
      // ctx.arc(x0,y0,width/2,angle-Math.PI,angle)
      ctx.arc(x0, y0, width / 4, angle - Math.PI, angle)
      ctx.fill();
      //ctx.stroke();
    },

    transform(xy, angle, xy0) {
      // поместите x и y относительно x0 и y0, чтобы вращаться вокруг
      const rel_x = xy[0] - xy0[0];
      const rel_y = xy[1] - xy0[1];

      // вычисление повернутых относительных точек
      const new_rel_x = Math.cos(angle) * rel_x - Math.sin(angle) * rel_y;
      const new_rel_y = Math.sin(angle) * rel_x + Math.cos(angle) * rel_y;

      return [xy0[0] + new_rel_x, xy0[1] + new_rel_y];
    },


    drawLine(ctx, x1, y1, x2, y2) {
      ctx.beginPath();
      ctx.moveTo(x1, y1);
      ctx.lineTo(x2, y2);
      ctx.stroke();
    },

    drawHead(ctx, x1, y1, x2, y2, filled) {
      var dx = x2 - x1;
      var dy = y2 - y1;
      ctx.beginPath();
      ctx.moveTo(x1 + 0.5 * dy, y1 - 0.5 * dx); // https://dirask.com/posts/jMqM0j
      ctx.lineTo(x1 - 0.5 * dy, y1 + 0.5 * dx); // https://dirask.com/posts/1GoW61
      ctx.lineTo(x2, y2);
      ctx.closePath();
      filled ? ctx.fill() : ctx.stroke();
    },

    drawArrow(ctx, x1, y1, x2, y2, arrow, filled) {
      if (arrow == null) {
        arrow = 0.1;
      }
      var dx = x2 - x1;
      var dy = y2 - y1;
      var t = 1.0 - arrow;
      var middleX = dx * t + x1;
      var middleY = dy * t + y1;
      This.drawLine(ctx, x1, y1, middleX, middleY);
      This.drawHead(ctx, middleX, middleY, x2, y2, filled);
    },

    drawEmptyArrow(ctx, x1, y1, x2, y2, arrow, color, width) {
      ctx.lineWidth = width;
      ctx.strokeStyle = color;
      This.drawArrow(ctx, x1, y1, x2, y2, arrow, false);
    },

    drawFilledArrow(ctx, x1, y1, x2, y2, arrow, color, width) {
      console.log('drawFilledArrow', x1, y1, arrow)
      ctx.lineWidth = width;
      ctx.fillStyle = color;
      ctx.strokeStyle = color;
      This.drawArrow(ctx, x1, y1, x2, y2, arrow, true);
    },
    /**endregion*/

    // endregion Функции рисования у оператора-------------------------------------------------------------------------------------

    //region Для тестирования
    // добавление нового видео
    testReceiveVideo(senderName, _senderIdtest, isBodyPix, isMic, isVideo, isFuncOper) {
      console.log('testReceiveVideo ', senderName, _senderIdtest, isBodyPix, isMic, isVideo, isFuncOper)
      var constraints = {
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]},
        video: {
          mandatory: {
            maxWidth: 320,
            maxFrameRate: 15,
            minFrameRate: 15
          }
        }
      };
      var senderIdtest = _senderIdtest
      //console.log('del 3 senderName = '+senderName)
      var nameUC = this.setNameUC(senderName)

      stream.calls[senderIdtest] = this.newCallData()
      var participant = stream.calls[senderIdtest];

      participant.new = true
      participant.isMic = isMic
      participant.isFuncOper = isFuncOper
      if (isFuncOper) {
        This.startFuncOperOther(senderIdtest)
      }
      participant.full = false
      participant.nameReplase = senderName
      participant.isMainWin = false
      participant.OperFunc = false

      var nameSender = senderName
      participant.scrs = false

      if (senderName.substring(0, 9) == 'Оператор_' || senderName.substring(0, 9) == 'Operator_') {
        participant.OperFunc = true
      } else if (senderName.substring(0, 7) == 'scrsXR_') {
        var keys = Object.keys(stream.calls);
        for (var i = 0; i < keys.length; ++i) {
          var participant_obj = stream.calls['' + keys[i]];
          participant_obj.full = false // на весь экран это окно
          participant_obj.isMainWin = false // это окно закрепить на весь экран
        }

        participant.scrs = true
        nameSender = senderName.substring(7, senderName.length) + this.$t('gc.scrS')

        if (This.isAddToAll) {
          This.isAddToAll = false

          $('#devDevBlockVideo_' + This.senderId).addClass('winVideoRoomDevMy')

          $('#idDivLocalVideo').append($('#devDevBlockVideo_' + This.senderId))
          $('#idDivLocalVideo').append($('#video_' + senderIdtest))

          $('#devVideo_' + This.senderId).hide()
          $('#iconUserAddAll_' + senderIdtest).removeClass('mdi mdi-grid v-btn_ot_ex_sp v-span-video')
          $('#iconUserAddAll_' + senderIdtest).addClass('mdi mdi-grid-off v-btn_ot_ex_sp v-span-video')


          $('#idDivLocalVideo').removeClass('iddivlocalvideo2')
          $('#idDivLocalVideo').addClass('iddivlocalvideo')
          console.log('is100 7')
          $('#idnewMyVideoClass').css('width', '100%')
          $('.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev').append($('#idDivLocalVideo'));
        }

        This.typeMaket = 2
        This.numScreenSharinWin++
      } else {
        participant.SSFunc = false
        participant.OperFunc = false
      }

      console.log('senderIdtest' + senderIdtest)
      participant.senderId = senderIdtest
      participant.ws = this.ws
      participant.bodyPix = isBodyPix
      participant.isPaint = false

      var video12 = `<div id="${'devVideo_' + senderIdtest}" class="winVideoRoom1">
                          <div id="${'devDevIconNoVideo_' + senderIdtest}" class="devDevIconNoVideo">
                            <img id="${'imgDevIconNoVideo_' + senderIdtest}" class="devDevIconNoVideoTextDev">
                            <div id="${'noImgDevIconNoVideo_' + senderIdtest}" class="devDevIconNoVideoTextDev">${nameUC}
<!--                            <span id="${'spanNoVideoText' + senderIdtest}" class="devDevIconNoVideoText">${nameUC}</span>-->
                          </div>

<!--                          <span <div id="${'spanNoVideo' + senderIdtest}" class="fa fas fa-video-slash v-btn_ot_stats_sp ma-2" style="color: white; font-size: 60px" aria-hidden="true"></span>-->
</div>
                        <div id="${'devDevBlockVideo_' + senderIdtest}" class="divDevBlockOper">
                           <div id="${'devOperFunc_' + senderIdtest}" class="devVideoBlockFunc">
                             <button
                               id="${'devBtnOperFunc_' + senderIdtest}"
                               class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type1">
                               <i id="${'iconOperFunc_' + senderIdtest}" class="fas fa-ellipsis-v" style="font-size: 14px; color: #636363;"></i>
                               <i id="${'iconOperFunc1_' + senderIdtest}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: #dee1e5; margin-top: 5px;"></i>
                               <i id="${'iconOperFunc2_' + senderIdtest}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: red;"></i>
                             </button>
                           </div>
                          <div id="${'devBlockVideo_' + senderIdtest}" class="devVideoBlock1">
                            <button
                             id="${'devBlockVideoBtn1_' + senderIdtest}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type1">
                             <span id="${'iconUserMicVideo_' + senderIdtest}" style="font-size: 14px; color: rgba(68,68,68,0.07);" class="fa fa-microphone v-btn_ot_ex_sp v-span-video"  aria-hidden="true"></span>
                           </button>
                           <button
                             id="${'devBlockVideoBtn3_' + senderIdtest}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type3">

                             <i id="${'iconMainVideo_' + senderIdtest}" class="fa fa-thumbtack" style="font-size: 14px; color: #636363;"></i>
                             <i id="${'iconMainVideo1_' + senderIdtest}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: #dee1e5; margin-top: 5px;"></i>
                             <i id="${'iconMainVideo2_' + senderIdtest}" class="fas fa-slash" style="position:absolute; font-size: 14px; color: red;"></i>
                           </button>
                           <button
                             id="${'devBlockVideoBtn2_' + senderIdtest}"
                             class="v-btn v-btn_ot_ex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnecticontext type2">
                             <span  style="font-size: 14px; color: #636363;" class="fa fa-times v-btn_ot_ex_sp v-span-video"  aria-hidden="true"></span>
                           </button>
                          </div>
                        </div>
                        <div id="${'devDevBlockVideoInfo_' + senderIdtest}">

                          <div class="winVideoRoomInfo row">
  <!--                           <span class="fa fas fa-microphone-alt v-btn_ot_stats_sp ma-2" aria-hidden="true"></span>-->
                             <div id="${'micVoiseActive_' + senderIdtest}"  class="cssSoundBars">
                               <div id="${'micVoise1_' + senderIdtest}" class="barCssSound"></div>
                               <div id="${'micVoise2_' + senderIdtest}" class="barCssSound"></div>
                               <div id="${'micVoise3_' + senderIdtest}" class="barCssSound"></div>
                             </div>
                             <span <div id="${'micVideo_' + senderIdtest}" class="fa fas fa-microphone-alt-slash v-btn_ot_stats_sp ma-2 infoSpanWin1" style="color: red" aria-hidden="true"></span>

                             <span <div id="${'micVoiseSaveAT_' + senderIdtest}" class="fa fas fa-microphone-alt-slash v-btn_ot_stats_sp ma-2 infoSpanWin3" style="color: red" aria-hidden="true"></span>
                            <p class="pdivVideoRow">${nameSender}</p>
                          </div>
                        </div>`

      var audio12 = `<audio
                  id="${'audio_' + senderIdtest}"
                  autoplay controls muted class="isHideWidth">`

      if (participant.OperFunc) {

        $('#devOperFunc_' + senderIdtest).show()

        video12 = video12 + `<canvas id="${'canvas_' + senderIdtest}" autoplay="" class="winVideovideoCanvas" style="z-index:15; position: absolute; background: transparent"></canvas>
                        <video id="${'video_' + senderIdtest}" autoplay="" class="winVideovideo"></video>
                       </div>`
      } else {
        $('#devOperFunc_' + senderIdtest).hide()
        video12 = video12 + `<canvas id="${'canvas_' + senderIdtest}" autoplay="" class="winVideovideoCanvas"></canvas>
                        <video id="${'video_' + senderIdtest}" autoplay="" class="winVideovideo"></video>
                       </div>`
      }

      This.isMicUser(senderIdtest, true)

      $('#participants').append(video12);

      $('#micVideo_' + senderIdtest).hide()
      $('#micVoiseActive_' + senderIdtest).hide()
      $('#micVoiseSaveAT_' + senderIdtest).hide()
      $('#micVoiseSaveATflag_' + senderIdtest).hide()
      $('#devDevIconNoVideo_' + senderIdtest).hide()
      $('#iconMainVideo1_' + senderIdtest).hide()
      $('#iconMainVideo2_' + senderIdtest).hide()
      $('#iconOperFunc1_' + senderIdtest).hide()
      $('#iconOperFunc2_' + senderIdtest).hide()

      if (participant.scrs) {
        This.mainVideoPosition(senderIdtest)
      }

      this.isSoundUser(senderIdtest, false)

      $('#devBlockVideo_' + senderIdtest).hide()
      $('#devOperFunc_' + senderIdtest).hide()

      //console.log('no oper func')
      //console.log($('devOperFunc_' + senderIdtest))
      $('#canvas_' + senderIdtest)[0].style.display = 'none'

      // object-fit: revert;

      try {
        if (participant.scrs) {
          $('#video_' + senderIdtest).addClass('screenVideoN')
          $('#devBlockVideoBtn1_' + senderIdtest).hide()
          $('#devBlockVideo_' + senderIdtest).addClass('noBackgroundSrcS')
        }
        if (senderName.substring(0, 9) == 'Оператор_' || senderName.substring(0, 9) == 'Operator_') {
          $('#video_' + senderIdtest).addClass('screenVideoN')
        }
      } catch (e) {
      }

      if (This.isExternalUser) {
        console.log('isExternalUser = true  test')
        $('#devOperFunc_' + senderIdtest).hide()
        $('#devBlockVideoBtn1_' + senderIdtest).hide()
        $('#devBlockVideoBtn2_' + senderIdtest).hide()
      }


      $('#video_' + senderIdtest).on('click', function () {
        if (!stream.calls[senderIdtest].full && This.mainWin == 0) {

          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];
            participant.full = false
          }

          $('#video_' + senderIdtest).addClass("btnVideo")

          setTimeout(() => {
            This.isSoundWinFunc = false
            stream.calls[senderIdtest].full = true
            This.setTypeWin(2, true)//isSoundWinFunc
            setTimeout(() => {
              if (This.numScreenSharinWin < 1) {
                This.isSoundWinFunc = false
              }
            }, 5000)

          }, 100)
        }
      });

      $('#devDevBlockVideoInfo_' + senderIdtest).on('click', function () {
        if (!stream.calls[senderIdtest].full && This.mainWin == 0) {

          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            var participant = stream.calls['' + keys[i]];
            participant.full = false
          }

          $('#video_' + senderIdtest).addClass("btnVideo")

          setTimeout(() => {
            This.isSoundWinFunc = false
            stream.calls[senderIdtest].full = true
            This.setTypeWin(2, true)//рисование?
            setTimeout(() => {
              if (This.numScreenSharinWin < 1) {
                This.isSoundWinFunc = false
              }
            }, 5000)

          }, 100)
        }
      });


      // появление только при наведении
      if (!this.isFirefox) {
        var videoDops = document.getElementById('video_' + senderIdtest);

        videoDops.addEventListener('mousemove', e => {
          var delHMax = e.srcElement.clientHeight - e.srcElement.clientHeight / 4;
          var delHMin = e.srcElement.clientHeight / 4;
          var delWMax = e.srcElement.clientWidth - e.srcElement.clientWidth / 4;
          var delWMin = e.srcElement.clientWidth / 4;
          x = e.offsetX;
          y = e.offsetY;
          if (delHMin < y && y < delHMax && delWMin < x && x < delWMax) {
            This.onIn(senderIdtest, participant.OperFunc)
          } else {
            This.onOut(senderIdtest)
          }
        });

        $('#video_' + senderIdtest).mouseout(e => {
          This.onOut(senderIdtest)
        });

        var btnBlock1 = $('button#devBlockVideoBtn1_' + senderIdtest)
        btnBlock1.mouseover(e => {

          This.onIn(senderIdtest, participant.OperFunc)
        });
        btnBlock1.on('click', function () {
          if (participant.isMic == true) {
            This.clickMuteUserCall(participant.senderIdtest, participant.senderName, participant.OperFunc)
          } else if (participant.OperFunc == true) {
            This.clickMuteUserCall(participant.senderIdtest, participant.senderName, participant.OperFunc)
          }
        });


        var btnBlock2 = $('button#devBlockVideoBtn2_' + senderIdtest)
        btnBlock2.mouseover(e => {
          This.onIn(senderIdtest, participant.OperFunc)
        });
        btnBlock2.on('click', function () {
          This.deleteTestVideo(senderIdtest)
        });

        var btnBlock3 = $('button#devBlockVideoBtn3_' + senderIdtest)
        btnBlock3.mouseover(e => {
          This.onIn(senderIdtest, participant.OperFunc)
        });
        btnBlock3.on('click', function () {
          This.mainVideoPosition(senderIdtest)
        });

        var btnBlockOper = $('button#devBtnOperFunc_' + senderIdtest)
        btnBlockOper.mouseover(e => {
          This.onIn(senderIdtest, participant.OperFunc)
        });
        btnBlockOper.on('click', function () {
          This.showFuncOper(senderIdtest)
        });
      }


      participant.video = video12
      participant.audio = audio12

      this.resizeWin(16)

    },

    deleteTestVideo(senderIdtest) {
      //console.log("onParticipantLeftName")

      $('#video_' + senderId).remove()
      $('#devVideo_' + senderId).remove()

      delete stream.calls[senderId]
      This.updateDataOper(1)
      this.resizeWin(14)

    },

    closeMyVideoPS() {
      console.log('Закрытие своего видео битрейт или много пользователей')
      var participant = stream.calls[this.senderId].peer
      var media_streams = participant.peerConnection.getLocalStreams();

      this.isStreamVideo = false
      try {
        STREAM_VIDEO.getVideoTracks()[0].enabled = true
        S_OLD_TREAM_VIDEO_FON.getVideoTracks()[0].enabled = true
      } catch (e) {
      }

      var video_tracks = media_streams[0].getVideoTracks();
      video_tracks[0].enabled = this.isStreamVideo;

      this.isVS_SS = this.isStreamVideo

      console.log('this.avatar ',this.avatar)

      if (this.avatar) {
        $('#imgDevIconNoVideoMain').attr('src', `data:image/jpg;base64,${this.avatar}`)
        $('#imgDevIconNoVideoMain1').attr('src', `data:image/jpg;base64,${this.avatar}`)
        $('#imgDevIconNoVideoMain1').addClass('isShowRightPanel')
        $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain1').removeClass('isShowRightPanel')

        $('#imgDevIconNoVideoMain1').removeClass('isHideRightPanel')
        $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain1').addClass('isHideRightPanel')
      } else {
        $('#noImgDevIconNoVideoMain1').css('font-size', '34px')
        $('#noImgDevIconNoVideoMain1').css('padding-top', '10px')
        $('#noImgDevIconNoVideoMain1').css('margin-left', '0')

        $('#imgDevIconNoVideoMain1').removeClass('isShowRightPanel')
        $('#imgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain').removeClass('isShowRightPanel')
        $('#noImgDevIconNoVideoMain1').addClass('isShowRightPanel')

        $('#imgDevIconNoVideoMain1').addClass('isHideRightPanel')
        $('#imgDevIconNoVideoMain').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain').addClass('isHideRightPanel')
        $('#noImgDevIconNoVideoMain1').removeClass('isHideRightPanel')
      }

      var message = {
        id: 'isVideoUser',
        room: This.paramValueRoom,
        senderName: This.senderName,
        senderId: This.senderId,
        isVideo: This.isStreamVideo,
        hasCamera: This.hasCamera
      };
      console.log('closeMyVideoPS isVideoUser message', message)
      this.sendMessage(message);
    },
    //endregion Для тестирования-------------------------------------------------------------------------------------

    //region SendMessage и остальные функции
    setWS(ws){
      this.ws = ws
    },

    sendMessage(message) {
      //this.ws.value.readyState === WebSocket.CLOSED
      console.log('22 ',this.ws.readyState)

      if (this.ws) {
        if(this.ws.readyState !== WebSocket.CLOSED){
          var jsonMessage = JSON.stringify(message)
          this.ws.send(jsonMessage)
        }else{
          console.error('error sendMessage this.ws ',message, this.ws)
        }
        return true
      }
    },

    // сохранение скриншота оператора
    saveScreenshot(senderId) {
      // stream.calls[senderId].peer.peerConnection.getSenders();

      var message = {
        id: 'saveScreenshotOper',
        setting: '',
        type: 1,
        roomId: This.paramValueRoom,
        senderId: senderId,
        senderIdExpert: This.senderId
      };

      console.log('saveScreenshotOper ', message)
      this.sendMessage(message);

    },

    sendScreenshot(base64data, senderId) {
      var body = {'senderId': senderId, 'nameFile': 'screen' + senderId, 'base64data': base64data}

      console.log('sendScreenshot body' + body)

      This.$root.restPost(`/groupCall/saveScreen`, body).then((res) => {
        console.log('sendScreenshot res', res)
        This.$root.showSucc(This.$t('expert.mess.info.screen-save'))
      })
    },

    handlerPS(e) {
    },

    oom() {
      this.sendMessage({
        id: 'leaveRoom'
      });
    },

    stopSaveVideo() {
      This.$refs.refSaveVideo.closeCall()
    },

    minBit(message) {
      if (message.status) {
        $('#minBitrateVideo_' + message.senderId).show()
      } else {
        $('#minBitrateVideo_' + message.senderId).hide()
      }
    },
    //endregion SendMessage и остальные функции-------------------------------------------------------------------------------------

    //region В режиме разработчика
    getSenderID() {
      return This.senderId
    },

    clickEditBitrate() {
      this.ws.close(3000);
      //this.isDevBitrate = !this.isDevBitrate
    },

    addOperListDialog() {
      //console.log('addOperListDialog 1')
      this.$emit('addOperDialog')
    },

    addMyAvatar(avatar) {

    },

    getAvatar() {
      var avatars = {}
      var keys = Object.keys(stream.calls);
      for (var i = 0; i < keys.length; ++i) {
        var participant_obj = stream.calls['' + keys[i]];
        avatars['' + keys[i]] = participant_obj.avatar
      }
      return avatars;
    },

    setCloseRightPanel() {
      this.isPanelInfoUser = false
      this.isChatShow = false
      //$('#mainChatMessage').hide()
      if(!$('#mainChatMessage').hasClass("isHideRightPanel")){
        $('#mainChatMessage').removeClass('isShowRightPanel')
        $('#mainChatMessage').addClass('isHideRightPanel')
      }
      //$('#divMainLinkUser').hide()
      if(!$('#divMainLinkUser').hasClass("isHideRightPanel")){
        $('#divMainLinkUser').removeClass('isShowRightPanel')
        $('#divMainLinkUser').addClass('isHideRightPanel')
      }
      $('#mainRoom').css('width', '100%')
      $('#mainAT').css('width', '100%')
    },

    setPanelInfoUser(b) {

      if (b == false) {
        this.isPanelInfoUser = false
      } else if (b == true) {
        this.isPanelInfoUser = true
      } else {
        this.isPanelInfoUser = !this.isPanelInfoUser
      }

      if (this.isPanelInfoUser) {
        //......................................................
        // закрытие чата
        if (this.isChatShow) {
          this.isChatShow = false
          //$('#mainChatMessage').hide()
          if(!$('#mainChatMessage').hasClass("isHideRightPanel")){
            $('#mainChatMessage').removeClass('isShowRightPanel')
            $('#mainChatMessage').addClass('isHideRightPanel')
          }
        }
        // закрыть информацию о чек-листе
        if (this.isPanelChik) {
          this.isPanelChik = false
          //$('#mainChikPanel').hide()
          if(!$('#mainChikPanel').hasClass("isHideRightPanel")){
            $('#mainChikPanel').removeClass('isShowRightPanel')
            $('#mainChikPanel').addClass('isHideRightPanel')
          }
        }
        //......................................................
        // показать информацию о пользователях
        if (This.isShowFuncOther) {
          $('#mainRoom').css('width', '100%')
          $('#mainAT').css('width', '100%')
        } else {
          $('#mainRoom').css('width', 'calc(100% - 400px)')
          $('#mainAT').css('width', 'calc(100% - 400px)')
        }
        $('#divMainLinkUser').show()
        if(!$('#divMainLinkUser').hasClass("isShowRightPanel")){
          $('#divMainLinkUser').removeClass('isHideRightPanel')
          $('#divMainLinkUser').addClass('isShowRightPanel')
        }
      } else {
        // убрать информацию о пользователях
        $('#mainRoom').css('width', '100%')
        $('#mainAT').css('width', '100%')
        //$('#divMainLinkUser').hide()
        if(!$('#divMainLinkUser').hasClass("isHideRightPanel")){
          $('#divMainLinkUser').removeClass('isShowRightPanel')
          $('#divMainLinkUser').addClass('isHideRightPanel')
        }
      }
    },

    // Панель чек-листов
    setPanelChik(b) {

      if (b == false) {
        this.isPanelChik = false
      } else if (b == true) {
        this.isPanelChik = true
      } else {
        this.isPanelChik = !this.isPanelChik
      }

      if (this.isPanelChik) {
        //......................................................
        // закрытие чата
        if (this.isChatShow) {
          this.isChatShow = false
          //$('#mainChatMessage').hide()
          if(!$('#mainChatMessage').hasClass("isHideRightPanel")){
            $('#mainChatMessage').removeClass('isShowRightPanel')
            $('#mainChatMessage').addClass('isHideRightPanel')
          }
        }
        // отключаем информацию
        if (this.isPanelInfoUser) {
          this.isPanelInfoUser = false
          //$('#divMainLinkUser').hide()
          if(!$('#divMainLinkUser').hasClass("isHideRightPanel")){
            $('#divMainLinkUser').removeClass('isShowRightPanel')
            $('#divMainLinkUser').addClass('isHideRightPanel')
          }
        }
        //......................................................
        // показать информацию о чек-листе
        if (This.isShowFuncOther) {
          $('#mainRoom').css('width', '100%')
          $('#mainAT').css('width', '100%')
        } else {
          $('#mainRoom').css('width', 'calc(100% - 400px)')
          $('#mainAT').css('width', 'calc(100% - 400px)')
        }
        //$('#mainChikPanel').show()
        if(!$('#mainChikPanel').hasClass("isShowRightPanel")){
          $('#mainChikPanel').removeClass('isHideRightPanel')
          $('#mainChikPanel').addClass('isShowRightPanel')
        }

        this.$emit('openChikPanel', This.getDataOpers())
      } else {
        // убрать информацию о чек-листе
        $('#mainRoom').css('width', '100%')
        $('#mainAT').css('width', '100%')
        //$('#mainChikPanel').hide()
        if(!$('#mainChikPanel').hasClass("isHideRightPanel")){
          $('#mainChikPanel').removeClass('isShowRightPanel')
          $('#mainChikPanel').addClass('isHideRightPanel')
        }
      }
    },

    stopStreamLocalVideo() {
      try {
        if ($(`#video_` + This.senderId)[0]) {
          $(`#video_` + This.senderId)[0].srcObject.getTracks().forEach(function (track) {
            track.stop();
            console.log('track.stop() vjs dlg onCancel')
          });
        }
        if (STREAM_VIDEO != null) {
          STREAM_VIDEO.getTracks().forEach(function (track) {
            track.stop();
            console.log('track.stop() vjs dlg onCancel')
          });
        }
      } catch (e) {
        console.log('error stopStreamLocalVideo ', e)
      }

    },
    soundRecAT(parsedMessage) {
      console.log('soundRecAT', parsedMessage)
      if (parsedMessage.wav) {

        var Sound = (function () {
          var df = document.createDocumentFragment();
          return function Sound(src) {
            var snd = new Audio(src);
            // console.log('Audio snd', snd)
            // console.log('Audio audioTracks', snd.audioTracks.length)
            df.appendChild(snd); // keep in fragment until finished playing
            snd.addEventListener('ended', function () {
              // console.log('!!!!!!!!!!!!!!!!!!!!!!!!!ended')
              df.removeChild(snd);
              sender.replaceTrack(trackAudio);
            });


            setTimeout(() => {
              // console.log('Audio 2  audioTracks', snd.audioTracks.length)
              snd.volume = 0
              snd.play();
            }, 100)

            setTimeout(() => {
              snd.pause()
              // console.log('Audio 3 audioTracks', snd.audioTracks.length)
            }, 500)

            setTimeout(() => {
              snd.volume = 1

              snd.play();
            }, 1000)
            return snd;
          }
        }());
        This.$emit('soundRecAT', false, 'EN', 'RU', parsedMessage.wav)
        var snd = Sound("data:audio/wav;base64," + parsedMessage.wav);

      } else {
        if (parsedMessage.isStatus) {
          $('#spanStatusTranslate_' + parsedMessage.senderId).show()
          $('#spanStatusTranslateText_' + parsedMessage.senderId).text(this.$t('gc.group.textStatusInfo2'))

          $('#micVoiseSaveAT_' + parsedMessage.senderId).show()
          $('#micVoiseSaveATflag_' + parsedMessage.senderId).show()

          $('#micVoiseSaveATflag_' + parsedMessage.senderId).empty();
          $('#micVoiseSaveATflag_' + parsedMessage.senderId).append(This.spanLang[parsedMessage.langSrc + '_22']);
        } else {
          $('#spanStatusTranslate_' + parsedMessage.senderId).hide()

          $('#micVoiseSaveAT_' + parsedMessage.senderId).hide()
          $('#micVoiseSaveATflag_' + parsedMessage.senderId).hide()
        }
      }
    },
    // остановить воспроизведение аудио перевода
    stopTranlsateResultAudio() {
      if (This.soundAudioATplay != null) {
        if (This.senderIdStopATaudio == This.senderId) {
          $('#devAudioTranslate_' + This.senderIdStopATaudio).hide()
          $('#devAudioTranslate1_' + This.senderIdStopATaudio).hide()
        } else {
          $('#devAudioTranslate_' + This.senderIdStopATaudio).hide()
        }
        console.log('stopTranlsateResultAudio', This.soundAudioATplay)
        This.soundAudioATplay.pause()
        This.soundAudioATplay = null
      }
    },

    asrtTranlsateResultAudio(parsedMessage) {
      // console.log('asrtTranlsateResultAudio', parsedMessage)
      // console.log('This.senderId', This.senderId)

      if (This.isAudioPlayATSender == 0) {
        var Sound = (function () {

          var df = document.createDocumentFragment();

          return function Sound(src) {
            if (This.soundAudioATplay != null) {
              This.soundAudioATplay.pause()
              This.soundAudioATplay = null
              if (This.senderIdStopATaudio == This.senderId) {
                $('#devAudioTranslate_' + This.senderIdStopATaudio).hide()
                $('#devAudioTranslate1_' + This.senderIdStopATaudio).hide()
              } else {
                $('#devAudioTranslate_' + This.senderIdStopATaudio).hide()
              }
            }

            This.soundAudioATplay = new Audio(src);
            try {
              df.appendChild(This.soundAudioATplay);

              This.soundAudioATplay.addEventListener('paused', function () {
                console.log('!!!!!!!!!!!!!!!!!!!!!!!!!paused')
                df.removeChild(This.soundAudioATplay);

                if (parsedMessage.senderId == This.senderId) {
                  $('#devAudioTranslate_' + parsedMessage.senderId).hide()
                  $('#devAudioTranslate1_' + parsedMessage.senderId).hide()
                } else {
                  $('#devAudioTranslate_' + parsedMessage.senderId).hide()
                }

                This.soundAudioATplay = null
              });

              This.soundAudioATplay.addEventListener('ended', function () {
                console.log('!!!!!!!!!!!!!!!!!!!!!!!!!ended')
                df.removeChild(This.soundAudioATplay);

                if (parsedMessage.senderId == This.senderId) {
                  $('#devAudioTranslate_' + parsedMessage.senderId).hide()
                  $('#devAudioTranslate1_' + parsedMessage.senderId).hide()
                } else {
                  $('#devAudioTranslate_' + parsedMessage.senderId).hide()
                }

                This.soundAudioATplay = null
              });

              This.soundAudioATplay.addEventListener('error', function () {
                This.$root.showErr(This.$t('gc.error.audioFile'))
                console.error(This.$t('gc.error.audioFile') + ' wav: ', "data:audio/wav;base64," + parsedMessage.wav);
              });

              This.soundAudioATplay.addEventListener("loadeddata", function () {
                setTimeout(() => {
                  This.soundAudioATplay.volume = 1

                  if (parsedMessage.senderId == This.senderId) {
                    $('#devAudioTranslate_' + parsedMessage.senderId).show()
                    $('#devAudioTranslate1_' + parsedMessage.senderId).show()
                  } else {
                    $('#devAudioTranslate_' + parsedMessage.senderId).show()
                    $('#spanStatusTranslate_' + parsedMessage.senderId).hide()
                  }

                  This.soundAudioATplay.currentTime = 0.0;

                  console.log('loadeddata snd.play();')
                  This.senderIdStopATaudio = parsedMessage.senderId;
                  This.soundAudioATplay.play();
                }, 0)
              })

              $('#spanStatusTranslate_' + parsedMessage.senderId).hide()
            } catch (e) {
              console.log('@@@@@@#######EEEE ' + e)
            }
            return snd;
          }
        }());

        var sound = Sound("data:audio/wav;base64," + parsedMessage.wav);

      } else {
        console.log('audio не проигрывается')
      }
    },

    // отправка аудио на сервер
    sendBlobAudioTranslate(blob, langSrc, langTgt, timestamp, senderIdOper, senderName, deviceId, deviceName) {

      var message = {
        id: 'sendBlobTranslateOper',
        room: This.paramValueRoom,
        senderId: senderIdOper,
        senderIdOper: senderIdOper,
        blob: blob,
        timestamp: timestamp,
        langSrc: langSrc,
        langTgt: langTgt,
        SenderName: senderName,
        DeviceId: deviceId,
        DeviceName: deviceName,
      };
      console.log('sendBlobTranslateOper ', message)
      this.sendMessage(message);
    },

    getAvatarToSenderId(senderId) {
      return This.avatar
    },

    saveAudioATOper(senderId, senderName) {

      var message = {
        id: 'saveAudioATOper',
        room: This.paramValueRoom,
        idTo: senderId,
        senderId: This.senderId,
        senderName: This.senderName
      };

      This.sendMessage(message)

      let senders = stream.calls[senderId].peer.peerConnection.getReceivers();
      senders.forEach((sender) => {
        try {
          if (sender.track.kind == 'audio') {
            console.log('sender.track)', sender.track)

            var voiceBlob

            navigator.mediaDevices.getUserMedia({audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]}})
                .then(stream => {
                  console.log('stream.getTracks()', stream.getTracks())

                  stream.getTracks().forEach(function (track) {
                    if (track.kind == 'audio') {
                      // track.stop();
                      stream.removeTrack(sender.track)

                      console.log('sender', sender)
                      console.log('sender.track', sender.track)

                      // stream.addTrack(sender.track)
                      console.log('stream.getTracks()', stream.getTracks())
                    }
                  });

                  This.mediaRecorder = new MediaRecorder(stream);

                  let voice = [];
                  This.mediaRecorder.start()

                  This.mediaRecorder.addEventListener("dataavailable", function (event) {
                    voice.push(event.data);
                  });

                  This.mediaRecorder.addEventListener("stop", function () {
                    This.voiceBlob = new Blob(voice, {
                      type: 'audio/wav'
                    });
                    console.log('voiceBlob', This.voiceBlob)


                    let reader = new FileReader();
                    //console.log('This.voiceBlob', This.voiceBlob)
                    var time = This.setTimestamp()

                    reader.readAsDataURL(This.voiceBlob);
                    reader.onloadend = function () {
                      var base64String = reader.result;
                      var bBase64String = base64String.substr(base64String.indexOf(', ') + 1)

                      var time = This.setTimestamp()
                      console.log('sendBlobServer id mess ' + This.paramValueRoom + This.senderId + time)
                      This.idSecondMessAT = This.paramValueRoom + This.senderId + time

                      var langSrc = localStorage['gc.langSrc']
                      var langTgt = localStorage['gc.langTgt']

                      This.sendBlobAudioTranslate(bBase64String, langTgt, langSrc, time, senderId, senderName, senderId, 'RealWear')
                    }

                    // if (This.isSendBlob) {
                    //   This.sendBlobServer()
                    // }
                    // This.isSendBlob = true
                  });

                });
          }
        } catch (e) {
          console.error('error all audio = ' + e)
          console.error(sender)
        }

      });
    },

    sendAudioATOper(senderId) {
      console.log('      This.mediaRecorder.stop()')
      var message = {
        id: 'sendAudioATOper',
        room: This.paramValueRoom,
        idTo: senderId,
        senderId: This.senderId,
        senderName: This.senderName
      };
      This.sendMessage(message)
      This.mediaRecorder.stop()
    },

    setTimestamp() {
      const dateTime = new Date();
      var month = dateTime.getMonth() + 1 < 10 ? '0' + (dateTime.getMonth() + 1) : (dateTime.getMonth() + 1)
      var date = dateTime.getDate() < 10 ? '0' + dateTime.getDate() : dateTime.getDate()
      var hours = dateTime.getHours() < 10 ? '0' + dateTime.getHours() : dateTime.getHours()
      var minutes = dateTime.getMinutes() < 10 ? '0' + dateTime.getMinutes() : dateTime.getMinutes()
      var seconds = dateTime.getSeconds() < 10 ? '0' + dateTime.getSeconds() : dateTime.getSeconds()
      console.log('setTimestamp ', dateTime)
      console.log('Timestamp ' + dateTime.getFullYear() + month + date + hours + minutes + seconds)
      return '' + dateTime.getFullYear() + month + date + hours + minutes + seconds;
    },

    sendTextTranslateNewStatus(parsedMessage) {
      console.log('parsedMessage' + parsedMessage)
      $('#spanStatusTranslate_' + parsedMessage.senderId).show()
      $('#spanStatusTranslateText_' + parsedMessage.senderId).text(this.$t('gc.group.textStatusInfo1'))
    },

    setLocalStorage(name, value) {
      localStorage[name] = value
    },

    getLocalStorage(name, type) {
      return localStorage[name];
    },

    updateDataOper(t) {
      console.log('updateDataOper ' + t)
      This.$emit('updateDataOper', This.getDataOpers())
    },

    getDataOpers() {
      var operators = []
      var keys = Object.keys(stream.calls);
      var senderOperFunc = ''
      var senderChekList = ''

      for (var i = 0; i < keys.length; ++i) {
        var callData = stream.calls['' + keys[i]];

        if (callData.OperFunc == true) {
          console.log('callData ', callData)
          if (callData.isRealFuncOper) {
            senderOperFunc = callData.senderId
          }

          if(callData.isChekList) {
            senderChekList = callData.senderId
          }

          var oper = {
            roomId: This.paramValueRoom,
            senderId: callData.senderId,
            label: callData.senderName,
            prId: callData.prId,
            resultId: callData.resultId,
            stepId: callData.stepId,
            fileId: callData.fileId
          }
          operators.push(oper);
        }
      }
      console.log('senderOperFunc ',senderOperFunc)
      console.log('senderChekList ',senderChekList)

      if(senderOperFunc == ''){
        senderOperFunc = senderChekList;
      }
      console.log('senderOperFunc2 ',senderOperFunc)
      return {
        senderId: senderOperFunc,
        operators: operators
      }
    },

    updateGcFileId(mess) {
      console.log('updateGcFileId', mess)
      var participant = stream.calls[mess.senderId];
      participant.fileId = mess.fileId
      This.updateDataOper(12)
    },

    asrtTranlsateNoResult(mess) {
      console.log('asrtTranlsateNoResult', mess)
      $('#spanStatusTranslate_' + mess.senderId).hide()
    },

    sendScreen(idScreen, typeSend, blob) {

    },

    async sendScreenshotOper(parsedMessage) {
      console.log('sendScreenshotOper', parsedMessage)

      // var blob = new Blob([parsedMessage.bytes], { type: 'image/jpeg' });

      // const base64Data = parsedMessage.bytes; // Замените на вашу Base64 строку
      // const base64Response = fetch(`data:image/jpeg;base64,${base64Data}`);
      // const blob = base64Response.blob();


      const base64Data = parsedMessage.base64; // Замените на вашу Base64 строку
      console.log('parsedMessage.base64 ', base64Data)
      const base64Response = await fetch(`data:image/jpeg;base64,${base64Data}`);
      const arrayBuffer = await base64Response.arrayBuffer();
      const blob = new Blob([arrayBuffer], {type: 'image/jpeg'});


      This.$refs.previewScreenshots.addScreen(parsedMessage.nameOper, parsedMessage.nameDevice, parsedMessage.time,
          parsedMessage.urlFile, parsedMessage.screenshotId, parsedMessage.url, blob)
    },

    deleteScreenshot(id) {
      var message = {
        id: 'deleteScreenshot',
        screenId: id
      };
      this.sendMessage(message);
    },

    sendToOperator() {
    },
    sendToChekList() {
    },
    sendToChat(blob) {
      //отправляем блоб (с пометками или без) в чат
      this.$emit('sendScreenshotToChat', blob);
    },

    testScreenshot() {
      var message = {
        id: 'saveScreenshotTest',
        setting: '',
        type: 1,
        roomId: This.paramValueRoom,
        senderId: This.senderId,
        senderIdExpert: This.senderId
      };

      console.log('saveScreenshotTest ', message)
      this.sendMessage(message);
    }

    // // ответ от сервера перевода после отправки аудио
    // restOneATmessage(message){
    //   console.log('restOneATmessage ',message)
    //   This.$refs.refAudioTranslation.restOneATmessage(parsedMessage)
    // }
    //endregion
  }
}

</script>

<style>
.isHideWidth{
  width: 1px;
}

.devDevBlockConnectcss {
  position: absolute;
  bottom: calc(50% - 40px);
  width: 100%;
  height: 26px;
}

.pdivVideoConndectcss {
  position: absolute;
  height: 100%;
  width: 100%;
  bottom: 50%;
  color: white;
  font-size: 18px;
  text-align: center;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  float: left;
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

.rowIconLayout {
  pointer-events: all;
  position: absolute;
  right: -18px;
  bottom: 36px;
  width: 60px;
}

.div-moreWinMAX {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  right: 0;
  background: transparent;
  z-index: 17;
}

.div-moreWin-AT {
  position: absolute;
  right: calc(50% - 120px);
  bottom: 282px;
  z-index: 17;
}

.div-moreWin {
  position: absolute;
  right: calc(50% - 146px);
  bottom: 82px;
  z-index: 17;
}

.itemList-more {
  height: 38px;
  min-height: 0px;
  border-bottom: solid 1px rgba(0, 0, 0, 0.21);
  padding-left: 10px;
}

.itemList-more:hover {
  background: var(--v-xr2L5-base);
}

.itemList-more:hover i {
  color: var(--v-xr2D2-base);
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

button.mr-1.btnFooter1.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-right-radius: 0 !important;
  border-top-right-radius: 0 !important;
  border-bottom-left-radius: 5px !important;
  border-top-left-radius: 5px !important;
}

button.mr-1.btnFooter3.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-left-radius: 0 !important;
  border-top-left-radius: 0 !important;
  border-bottom-right-radius: 5px !important;
  border-top-right-radius: 5px !important;
}

.devVideoBlock1 {
  background: rgba(0, 0, 0, 0.5);
  border-radius: 4px;
  width: max-content;
  height: 46px;
  position: absolute;
  top: calc(50% - 30px);
  left: calc(50% - 60px);
  padding: 4px;
  z-index: 1;
}

.devVideoBlock2 {
  background: rgba(0, 0, 0, 0.5);
  border-radius: 4px;
  width: max-content;
  height: 46px;
  position: absolute;
  top: calc(50% - 30px);
  left: calc(50% - 24px);
  padding: 4px;
  z-index: 1;
}

.devVideoBlockFunc {
  background: rgba(0, 0, 0, 0.5);
  height: 40px;
  width: 40px;
  padding-right: 4px;
  position: absolute;
  top: calc(50% - -16px);
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

button.v-btn.v-btn_ot_ex.v-btn--router.v-size--small.theme--dark.v-btn--tile.v-btn--depressed.v-btn--rounded.mybrnecticontext.type6 {
  left: 2px;
  border-top-right-radius: 0;
  border-top-left-radius: 0;
  padding: 0;
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

.winVideoRoomDevFully {
  width: 85%;
  height: 100%;
  position: absolute;
}

.winVideoRoomFulln {
  object-fit: contain;
  height: 160px;
  width: 100%;
  background: #3c3c3d;
  border: solid 1px rgba(0, 0, 0, 0.51);
}

/* портретная ориентация */
@media screen and (orientation: portrait) {
  /* Стили для портретной */
  .winVideoRoomFully {
    object-fit: contain;

    width: 100%;
    height: 100%;
    background: #3c3c3d;
    border: solid 1px rgba(0, 0, 0, 0.51);
  }

  .winVideoRoomDevFully {
    width: 100%;
    height: 100%;
    position: absolute;
  }

  .winVideoRoomFulln {
    object-fit: contain;
    height: 20%;
    width: 20%;
    bottom: 0;
    background: #3c3c3d;
    border: solid 1px rgba(0, 0, 0, 0.51);
  }

  .winVideoRoomDevFulln {
    height: 20%;
    width: 20%;
    bottom: 0;
    position: absolute;
  }
}

/* горизонтальная ориентация */
@media screen and (orientation: landscape) {
  /* стили для горизонтальной */
  .winVideoRoomFully {
    object-fit: contain;

    width: 85%;
    height: 100%;
    background: #3c3c3d;
    border: solid 1px rgba(0, 0, 0, 0.51);
  }

  .winVideoRoomDevFully {
    width: 85%;
    height: 100%;
    position: absolute;
  }

  .winVideoRoomFulln {
    object-fit: contain;
    height: 180px;
    width: 100%;
    background: #3c3c3d;
    border: solid 1px rgba(0, 0, 0, 0.51);
  }

  .winVideoRoomDevFulln {
    height: 180px;
    width: 100%;
    position: absolute;
  }
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
  padding-bottom: 2px;
  padding-right: 2px;

  background: #1b1b1b;
}

.winVideoRoomInfo.row {
  padding-left: 5px
}

.winVideoRoomInfo {
  padding-top: 4px;
  padding-left: 5px;
  background: rgba(0, 0, 0, 0.76);
  position: absolute;
  width: 100%;
  height: 44px;
  bottom: 0;
  border-radius: 5px;
  color: #ffffff;
  font-size: 16px;
}

.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev {
  height: 50px;
  padding: 0px !important;
  background-color: #dedede !important;
  margin: 0;
  background: #dedede !important;
  position: absolute;
}

footer.v-footer.vFooterRoom.v-sheet.theme--light.v-footer--absolute.v-footer--padless {
  background: transparent;
}

.row.pa-6.text-center.grey.lighten-2.rounded-bl-xl.infoTooldev {
  position: absolute;
}

button.mr-1.btnFooter1.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-right-radius: 0 !important;
  border-top-right-radius: 0 !important;
}

button.mr-1.btnFooter3.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-bottom-left-radius: 0 !important;
  border-top-left-radius: 0 !important;
  /*border-top-right-radius: 6px;*/
  /*border-bottom-right-radius: 6px;*/
}

.winVideoLocal {
  object-fit: contain;
  width: 100%;
  height: 100%;
}

button.fabAddSet.save.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default.xr-btn-normal {
  background-color: #c13434 !important;
  color: #f0f0f0;
  margin: 0 !important;
  margin-right: 4px !important;
  margin-top: 3px !important;
  font-size: 14px;
  min-width: 54px;
}

p.pdivVideoRow {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  float: left;
  width: calc(100% - 100px);
  padding-top: 6px;
  padding-left: 4px;
}

span.fa.fas.fa-microphone-alt-slash.v-btn_ot_stats_sp.ma-2.infoSpanWin1 {
  margin: 5px 4px -1px 0px !important;
  padding: 5px 0 0 0 !important;
}

span.fa.fas.fa-microphone-alt-slash.v-btn_ot_stats_sp.ma-2.infoSpanWin3 {
  margin: 5px 4px -1px 0px !important;
  padding: 5px 0 0 0 !important;
}

span.fa.fas.fa-signal.v-btn_ot_stats_sp.ma-2.infoSpanWin2 {
  margin: 5px 4px -1px 2px !important;
  padding: 5px 0 0 0 !important;
}


.cssSoundBarsAT {
  padding-top: 4px;
  width: 30px;
  height: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  /*     background: black; */
}

.barCssSoundAT {
  background: #b87517;
  bottom: 1px;
  height: 5px;
  width: 5px;
  margin: 0px 1px;
  border-radius: 15px;
  -webkit-animation: soundAT 0ms -600ms linear infinite alternate;
  animation: soundAT 0ms -600ms linear infinite alternate;
}

@keyframes soundAT {
  0% {
    opacity: .35;
    height: 3px;
  }
  100% {
    opacity: 1;
    height: 15px;
  }
}

.barCssSoundAT:nth-child(1) {
  left: 1px;
  animation-duration: 433ms;
}

.barCssSoundAT:nth-child(2) {
  left: 7px;
  animation-duration: 474ms;
}

.barCssSoundAT:nth-child(3) {
  left: 14px;
  animation-duration: 407ms;
}



/* УЛУЧШАЕМ ТОЛЬКО ВАШУ ТЕКУЩУЮ РЕАЛИЗАЦИЮ */

.cssSoundBars {
  padding-top: 4px;
  width: 40px; /* Чуть шире */
  height: 32px; /* Чуть выше */
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(0, 0, 0, 0.15); /* Легкий фон */
  border-radius: 8px; /* Скругленные углы */
  border: 1px solid rgba(255, 255, 255, 0.1); /* Тонкая рамка */
  transition: all 0.3s ease;
}

/* При наведении или активности */
.cssSoundBars:hover,
.cssSoundBars.active {
  background: rgba(244, 195, 37, 0.46);
  border-color: rgba(244, 195, 37, 0.78);
  box-shadow: 0 2px 8px rgba(244, 195, 37, 0.53);
}

.barCssSoindNoAnimal {
  animation: none !important;
  opacity: 0.4;
}

.barCssSound {
  background: linear-gradient(to top, rgba(244, 201, 60, 0.8), rgba(244, 195, 37, 0.53)); /* Градиент вместо плоского цвета */
  bottom: 1px;
  height: 5px;
  width: 6px; /* Чуть шире */
  margin: 0px 1px; /* Больше отступ */
  border-radius: 3px 3px 0 0; /* Скругление только сверху */
  animation: sound 400ms -600ms linear infinite alternate;
  transition: all 0.2s ease;
  box-shadow: 0 1px 3px rgba(59, 130, 246, 0.3); /* Легкая тень */
}

@keyframes sound {
  0% {
    opacity: 0.35;
    height: 4px;
    transform: translateY(0);
  }
  50% {
    opacity: 0.8;
    height: 12px;
    transform: translateY(-2px); /* Легкий подъем */
  }
  100% {
    opacity: 1;
    height: 16px; /* Выше максимум */
    transform: translateY(-4px);
  }
}

.barCssSound:nth-child(1) {
  animation-duration: 433ms;
  animation-delay: 0ms;
}

.barCssSound:nth-child(2) {
  animation-duration: 367ms;
  animation-delay: 100ms;
}

.barCssSound:nth-child(3) {
  animation-duration: 500ms;
  animation-delay: 200ms;
}

/* Когда пользователь говорит - зеленый цвет */
.cssSoundBars.speaking .barCssSound {
  background: linear-gradient(to top, #22c55e, #4ade80);
  box-shadow: 0 1px 4px rgba(34, 197, 94, 0.4);
  animation-duration: 300ms; /* Быстрее когда говорит */
}




@keyframes pulseGlow {
  0%, 100% {
    opacity: 0.5;
    transform: scale(1);
  }
  50% {
    opacity: 0.8;
    transform: scale(1.02);
  }
}

.clVideoMirror {
  transform: rotateY(180deg);
  -webkit-transform: rotateY(180deg);
  -moz-transform: rotateY(180deg);
}

.v-list.v-sheet.theme--light.v-list--flat {
  margin: 0;
  padding: 0;
}

.multi-rippleDiv {
  margin-top: 2px;
}

.multi-ripple {
  width: 30px;
  height: 30px;
  margin-top: 2px;
}

.cAT {
  position: absolute;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 0.3rem solid #c6852f;
  -webkit-animation: 1.5s ripple infinite;
  animation: 1.5s ripple infinite;
}

.cAT:nth-child(2) {
  animation-delay: 0.5s;
}

@keyframes ripple {
  from {
    transform: scale(0);
    opacity: 1;
  }

  to {
    transform: scale(1);
    opacity: 0.5;
  }
}

.divAudioTranslate {
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 1;
  pointer-events: none;
  left: calc(50% - 56px);
  top: calc(50% - 60px);
}

.divAudioTranslateLoc {
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 1;
  pointer-events: none;
  left: calc(0% - 15px);
  top: calc(0% - 15px);
}

.spanSTraText {
  padding: 6px 16px 8px 16px;
  animation: glowing 1300ms infinite;
  border-radius: 5px;
  color: #333333;
}

.spanSTra {
  position: absolute;
  width: 100%;
  z-index: 1;
  pointer-events: none;
  left: 0;
  top: calc(50% - 18px);
  font-size: 16px;
  text-align: center;
}

@keyframes glowing {
  0% {
    background: rgba(198, 133, 47, 0.22);
  }
  50% {
    background: rgba(198, 133, 47, 0.68);
  }
  100% {
    background: #c6852f;
  }
}

.blinkAudoiAT {
  animation-name: blinkAT;
  animation-timing-function: linear;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}

@keyframes blinkAT {
  50% {
    opacity: 0;
  }
}

.p1video {
  padding-left: 1px !important;
  padding-right: 1px !important;
}

/*========================================================================*/
button.mr-1.btnFooter2 {
  pointer-events: all !important;
  font-size: 30px !important;
  width: 44px !important;
  height: 44px !important;
  border-radius: 0 !important;
}

.vrow-menu-right {
  pointer-events: all;
  position: absolute;
  right: 16px;
  bottom: 32px;
  background: hsla(0, 0%, 100%, 0);
  height: 44px;
  border-bottom-left-radius: 5px;
  border-top-left-radius: 5px;
}

/*.vrow-menu-right > button {*/
/*  pointer-events: all !important;*/
/*  font-size: 30px !important;*/
/*  width: 44px !important;*/
/*  height: 44px !important;*/
/*  border-radius: 0 !important;*/
/*  border-radius: 6px !important;*/
/*}*/
/*-----------------------------------------------------------------------*/
.gc-users-btn {
  width: auto !important;
  min-width: 44px;
}

.gc-users-btn .gc-users-span {
  font-size: 20px;
  font-weight: bold;
}

.gc-users-btn .gc-users-icon {
  position: absolute;
  font-size: 10px !important;
  margin-right: -22px;
  margin-bottom: -22px;
}

.gc-users-btn .gc-users-icon-ext {
  position: absolute;
  font-size: 14px !important;
  margin-right: -22px;
  margin-bottom: -22px;
  color: OrangeRed !important;
}

p.notification__text {
  font-family: var(--xr-main-font-family-normal) !important;
}

button.notification__action {
  font-family: var(--xr-main-font-family-normal) !important;
}

.disable-events {
  pointer-events: none
}

.minBitrateVideo{
  font-size: 16px;
  margin-left: 2px !important;
  margin-right: 5px !important;
  margin-top: 5px !important;
}

.isHideRightPanel{
  display: none!important
}
.isShowRightPanel{
  display: block!important
}

.isFlexRightPanel{
  display: flex!important
}

.connectionCalldiv{
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  background: #3c3c3d;
  z-index: 5;
}

.connectionCalldivP{
  position: absolute;
  top: 50%;
  width: 100%;
  text-align: center;
  color: var(--v-xr2L5-base);
}

</style>

