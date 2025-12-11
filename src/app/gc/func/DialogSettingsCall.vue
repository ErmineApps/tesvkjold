<template>
  <XrDialog ref="settingGCDlg"
            :title="$t('settings')"
            :resize="true"
            :min-width="740"
            :min-height="450"
            :fullscreen="false"
            @onresizestop="onResize"
            @onclose="onClose"
            class="gcDialogSetting"
            width="800"
            height="500"
  >
    <v-card class="hFull2" style="padding-bottom: 2px; border: none; box-shadow: none;">
      <v-card-text class="pa-0 hFull">
<!--        <v-tabs centered grow v-model="currentTab"  id="idTabSlot" class="tabSlotWeb" active-class="xr-tab-set-active">-->
<!--          <v-tab key='audioSett' class="vtabSettGC" v-t="'expert.audio'"></v-tab>-->
<!--          <v-tab key='videoSett' class="vtabSettGC" v-t="'expert.video'"></v-tab>-->
<!--        </v-tabs>-->

<!--        <v-tabs-items v-model="currentTab" style="height: calc(100% - 48px)">-->
<!--          <v-tab-item key='audioSett'>-->
<!--            <div id="xrTBm2">-->
<!--              <div class="gcSelectPart">-->
<!--                <v-select-->
<!--                  v-model="audioInputValue"-->
<!--                  :items="audioInputSelect"-->
<!--                  :label="$t('gc.mic')"-->
<!--                  class="pt-3"-->
<!--                  outlined-->
<!--                  placeholder=" "-->
<!--                ></v-select>-->
<!--                <v-select-->
<!--                  v-model="audioOutputValue"-->
<!--                  :items="audioOutputSelect"-->
<!--                  :label="$t('gc.speaker')"-->
<!--                  class="pt-3"-->
<!--                  outlined-->
<!--                  placeholder=" "-->
<!--                ></v-select>-->
<!--              </div>-->
<!--            </div>-->
<!--          </v-tab-item>-->
<!--          <v-tab-item key='videoSett'>-->
<!--            <div id="xrTBm3" class="">-->
<!--              <div class="gcSelectPart">-->
<!--                <v-select-->
<!--                  v-model="videoValue"-->
<!--                  :items="videoSelect"-->
<!--                  :label="$t('gc.camera')"-->
<!--                  class="pt-3"-->
<!--                  outlined-->
<!--                  placeholder=" "-->
<!--                ></v-select>-->
<!--                <v-select-->
<!--                  v-model="videoQualtiySendValue"-->
<!--                  :items="videoQualtiySendSelect"-->
<!--                  :label="'Разрешение отправляемого видео (максимальное)'"-->
<!--                  class="pt-3"-->
<!--                  outlined-->
<!--                  placeholder=" "-->
<!--                ></v-select>-->

<!--              </div>-->
<!--            </div>-->
<!--          </v-tab-item>-->
<!--        </v-tabs-items>-->


        <v-row class="hFull">
          <!-- ======================================================== -->
          <!-- Левая часть -->
          <v-col class="hFull" cols="5">
            <div id="divDlgSetVideo" class="dialogSet-divVideo hFull">
              <!-- Видео -->
              <video id="videoPreSett" autoplay="" data-uid="31" class="videoSettPrevDiv hFull" width="640"
                     height="300" v-bind:style="{'background-image': 'url('+fon1_+')'}">
              </video>
              <p v-if="!hasCameraSett" class="pErrorDevicesDlgSett">{{ this.$t('ex.error.video') }}</p>
              <!-- "Левые" настройки фона-->
              <div v-if="isDegradationSett" class="fon-sett-class">
                <v-row>
                  <!--                    <div class="vDivFon-sett"> <img :src="noImageSvg_" class="imgFon-sett" alt=""  @click="clickNumFon(0)">-->
                  <!--                      <v-icon v-if="numSettFon==0" class="vIconFon1">far fa-check-circle</v-icon></div>-->
                  <!--                    <div class="vDivFon-sett"> <img :src="fon1_" class="imgFon-sett" alt="" @click="clickNumFon(1)" >-->
                  <!--                      <v-icon v-if="numSettFon==1"  class="vIconFon1">far fa-check-circle</v-icon></div>-->
                  <!--                    <div class="vDivFon-sett"> <img :src="fon2_" class="imgFon-sett" alt="" @click="clickNumFon(2)">-->
                  <!--                      <v-icon v-if="numSettFon==2"  class="vIconFon1">far fa-check-circle</v-icon></div>-->
                  <!--                    <div class="vDivFon-sett"> <img :src="fon3_" class="imgFon-sett" alt="" @click="clickNumFon(3)">-->
                  <!--                      <v-icon v-if="numSettFon==3"  class="vIconFon1">far fa-check-circle</v-icon></div>-->
                  <div class="vDivFon-sett"><img :src="fonSvg_" class="imgFon-sett-svg" alt="" @click="clickNumFon(0)">
                    <v-icon v-if="numSettFon==0" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #a1d6fc" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(1)">
                    <v-icon v-if="numSettFon==1" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #eaca74" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(2)">
                    <v-icon v-if="numSettFon==2" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #ffc5e2" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(3)">
                    <v-icon v-if="numSettFon==3" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #c1f6b6" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(4)">
                    <v-icon v-if="numSettFon==4" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #a56464" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(5)">
                    <v-icon v-if="numSettFon==5" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                  <div class="vDivFon-sett"><img style="background: #a5db35" class="imgFon-sett" alt=""
                                                 @click="clickNumFon(6)">
                    <v-icon v-if="numSettFon==6" class="vIconFon1">far fa-check-circle</v-icon>
                  </div>
                </v-row>
              </div>

              <!--              <v-btn color="white" fab style="-->
              <!--                z-index: 1;-->
              <!--                    position: absolute;-->
              <!--                    right: 392px;-->
              <!--                    top: 304px;-->
              <!--            pointer-events: all;-->
              <!--            font-size: 30px;-->
              <!--            width: 50px;-->
              <!--            height: 50px;-->
              <!--            border-radius: 50px;-->
              <!--                border-style: dashed;-->
              <!--    border-color: #fefefe!important;-->
              <!--    background: transparent;-->
              <!--    background-color: transparent!important;}"-->
              <!--                     class="mr-6"-->
              <!--                     :title="isDegradationSett ? $t('gc.func.disable-mic2') : $t('gc.func.play-mic2')"-->
              <!--                     @click="clickFon">-->
              <!--                <v-icon v-if="isDegradationSett" style="font-size: 18px;">fas fa-user-alt</v-icon>-->
              <!--                <v-icon v-if="!isDegradationSett" style="font-size: 18px;">fas fa-user-alt-slash</v-icon>-->
              <!--              </v-btn>-->
            </div>
          </v-col>

          <!-- ======================================================== -->
          <!-- Правая часть -->
          <v-col cols="7">
            <div class="gcSelectPart">
              <p class="pGCSetting">{{$t('expert.audio')}}</p>
              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <v-select
                    v-model="audioInputValue"
                    :items="audioInputSelect"
                    :label="$t('gc.mic')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col class="colSGC1" cols="1">
<!--                  <canvas id="canvasAudioSet" class="dcanvasAudioSet"></canvas>-->

                  <div id="canvasAudioSet" class="circleAS">
                    <div id="canvasAudioSet1" class="rectangleAS redAS"></div>
                    <div id="canvasAudioSet2" class="rectangleAS blueAS"></div>
                    <div id="canvasAudioSet3" class="rectangleAS greenAS"></div>
                    <div id="canvasAudioSet4" class="rectangleAS blueAS"></div>
                    <div id="canvasAudioSet5" class="rectangleAS redAS"></div>
                  </div>
                  <v-icon id="iconAudioSet3" class="viconAudioSet">mdi-microphone</v-icon>
                </v-col>
              </v-row>
              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <v-select
                    v-model="audioOutputValue"
                    :items="audioOutputSelect"
                    :label="$t('gc.speaker')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">
                  <v-btn
                         :disabled="isPlayAudioTest"
                         :title="$t('gc.test.audio')"
                         class="btnSettingGC"
                         @click="clickTestSpeaker">
                    <v-icon style="font-size: 16px;">fas fa-volume-up</v-icon>
                  </v-btn>
                </v-col>
              </v-row>
              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <!-- Микрофон про пробелу -->
                  <v-select
                    v-model="isMicroWithSpaceBar"
                    :items="yesNoSelect"
                    :label="$t('gc.turn.micro.with.space')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">

                </v-col>
              </v-row>

              <p class="pGCSetting">{{$t('expert.video')}}</p>
              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <v-select
                    v-model="videoValue"
                    :items="videoSelect"
                    :label="$t('gc.camera')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">

                </v-col>
              </v-row>

              <v-row v-if="hasCameraSett" class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <!-- Зеркальное отображение -->
                  <v-select
                    v-model="isMirror"
                    :items="yesNoSelect"
                    :label="$t('labelMirror')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">

                </v-col>
              </v-row>

              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <v-select
                    v-model="videoQualtiySendValue"
                    :items="videoQualtiySendSelect"
                    :label="$t('gc.video.QualtiySend')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">

                </v-col>
              </v-row>

              <v-row class="rowSGC">
                <v-col class="colSGC" cols="11">
                  <v-select
                    v-model="videoQualtiyRecvValue"
                    :items="videoQualtiyRecvSelect"
                    :label="$t('gc.video.QualtiyRecv')"
                    class="pt-3"
                    outlined
                    placeholder=" "
                  ></v-select>
                </v-col>
                <v-col cols="1">

                </v-col>
              </v-row>

            </div>
<!--            <p>dsafhsadlkhfsahdf</p>-->
<!--            <v-tabs v-model="setTab" hide-slider centered grow-->
<!--                    background-color="xr-tab-set-background"-->
<!--                    active-class="xr-tab-set-active">-->
<!--              &lt;!&ndash; закладки показываем, когда задан rowId и он не равен 0 &ndash;&gt;-->
<!--              <template v-if="rowId && rowId != 0">-->
<!--                <v-tab key="tabItem" title="Атрибуты канала">-->
<!--                  <v-icon>mdi-file-document-edit-outline</v-icon>-->
<!--                </v-tab>-->
<!--                <v-tab key="tabPw"  title="Параметры связи">-->
<!--                  <v-icon v-if="pwData.id">mdi-shield-check-outline</v-icon>-->
<!--                  <v-icon v-else>mdi-shield-outline</v-icon>-->
<!--                </v-tab>-->
<!--              </template>-->
<!--              &lt;!&ndash; карточка нового канала. когда rowId = 0 &ndash;&gt;-->
<!--              <v-tab class="xr-plan-set-active-tab2" key="tabCreate" :title="'Атрибуты канала'">-->
<!--                <v-icon>mdi-clipboard-text-outline</v-icon>-->
<!--                &lt;!&ndash; Карточка нового канала &ndash;&gt;-->
<!--                <span class="pl-1">{{ $t('esb.channel.card') }}</span>-->
<!--              </v-tab>-->
<!--            </v-tabs>-->



<!--              <v-tabs centered grow v-model="currentTab"  id="idTabSlot" class="tabSlotWeb" active-class="xr-tab-set-active">-->
<!--                <v-tab key='audioSett' class="vtabSettGC" v-t="'expert.audio'"></v-tab>-->
<!--                <v-tab key='videoSett' class="vtabSettGC" v-t="'expert.video'"></v-tab>-->
<!--              </v-tabs>-->

<!--            <v-tabs-items v-model="currentTab" style="height: calc(100% - 48px)">-->
<!--              <v-tab-item key='audioSett'>-->
<!--                <div id="xrTBm2">-->
<!--                  <div class="gcSelectPart">-->
<!--                    <v-select-->
<!--                      v-model="audioInputValue"-->
<!--                      :items="audioInputSelect"-->
<!--                      :label="$t('gc.mic')"-->
<!--                      class="pt-3"-->
<!--                      outlined-->
<!--                      placeholder=" "-->
<!--                    ></v-select>-->
<!--                    <v-select-->
<!--                      v-model="audioOutputValue"-->
<!--                      :items="audioOutputSelect"-->
<!--                      :label="$t('gc.speaker')"-->
<!--                      class="pt-3"-->
<!--                      outlined-->
<!--                      placeholder=" "-->
<!--                    ></v-select>-->
<!--                  </div>-->
<!--                </div>-->
<!--              </v-tab-item>-->
<!--              <v-tab-item key='videoSett'>-->
<!--                <div id="xrTBm3" class="">-->
<!--                  <div class="gcSelectPart">-->
<!--                    <v-select-->
<!--                      v-model="videoValue"-->
<!--                      :items="videoSelect"-->
<!--                      :label="$t('gc.camera')"-->
<!--                      class="pt-3"-->
<!--                      outlined-->
<!--                      placeholder=" "-->
<!--                    ></v-select>-->
<!--                    <v-select-->
<!--                      v-model="videoQualtiySendValue"-->
<!--                      :items="videoQualtiySendSelect"-->
<!--                      :label="'Разрешение отправляемого видео (максимальное)'"-->
<!--                      class="pt-3"-->
<!--                      outlined-->
<!--                      placeholder=" "-->
<!--                    ></v-select>-->

<!--                  </div>-->
<!--                </div>-->
<!--              </v-tab-item>-->
<!--            </v-tabs-items>-->
          </v-col>
          <!-- ======================================================== -->
        </v-row>
      </v-card-text>
    </v-card>



    <audio id="audioSettPreview"></audio>

    <!-- ======================================================== -->
    <template v-slot:footer>
      <v-flex>
        <v-card-actions class="ma-0 pa-0">

          <v-spacer></v-spacer>
          <XrBtn type="cancel" @click="onCancel"></XrBtn>
          <XrBtn type="save" @click="onSave"></XrBtn>
        </v-card-actions>
      </v-flex>
    </template>
    <!-- ======================================================== -->

  </XrDialog>
</template>

<script>
import noImageSvg from "@/assets/icons/layout_v2_tiled.svg"
import fonSvg from "@/assets/img/fon/fonBlur.svg"
import $ from "jquery";

import ringTone from '../../../assets/voice/ringTestSpeaker.mp3';

var This

let STREAM_AUDIO_PREVIEW = null
let S_OLD_TREAM_VIDEO_FON = null

export default {
  name: "DialogSettingsCall",
  data() {
    return {
      isDialogSett: false,
      audioInputSelect: [],
      audioOutputSelect: [],
      videoSelect: [],
      videoQualtiySendSelect: [{text: this.$t('gc.video.qualtiy1'), value: '1'},{text: this.$t('gc.video.qualtiy2'), value: '2'},{text: this.$t('gc.video.qualtiy3'), value: '3'}],
      videoQualtiyRecvSelect: [{text: this.$t('gc.video.qualtiy1'), value: '1'},{text: this.$t('gc.video.qualtiy2'), value: '2'},{text: this.$t('gc.video.qualtiy3'), value: '3'}],
      yesNoSelect: [{text: this.$t('yes'), value: true}, {text: this.$t('no'),value: false}],
      oldVideoValue: null,
      audioInputValue: null,
      audioOutputValue: null,
      echoCancellationValue: true,
      volumeValue: 0.1,
      videoValue: null,
      videoQualtiySendValue: '1',
      videoQualtiyRecvValue: '1',
      isDegradationSett: false,
      isShowCall: false,
      fon1_: {r: 161, g: 214, b: 252, a: 255},
      fon2_: {r: 234, g: 202, b: 116, a: 255},
      fon3_: {r: 255, g: 197, b: 226, a: 255},
      fon4_: {r: 193, g: 246, b: 182, a: 255},
      fon5_: {r: 165, g: 100, b: 100, a: 255},
      fon6_: {r: 193, g: 219, b: 53, a: 255},
      noImageSvg_: noImageSvg,
      fonSvg_: fonSvg,
      numSettFon: -1,
      isMirror: true,
      isSaveNew: false,// проверка, были ли измененны выбора устройств
      streamVideo: null,// для временного хранения стрима, во время отображения диалога
      hasCameraSett: true, //доступность камеры в диалогое

      isPreSetting: true,
      currentTab: 0,
      isPlayAudioTest: false,

      isStartControlVideo: false,
      isStartControlAudio: false,
      isVideo: true,
      isAudio: true,

      isMicroWithSpaceBar: false // вкл/выкл микро по пробелу
    }
  },

  watch: {
    isDialogSett: {
      handler: function () {
        if (This.isDialogSett) {

        } else {

        }
      }
    },
    isMirror: {
      handler: function () {
        if (This.isMirror) {
          $(`#videoPreSett`).addClass('clVideoMirror')
        } else {
          $(`#videoPreSett`).removeClass('clVideoMirror')
        }
      },
    },
    videoValue: {
      handler: function () {
        this.previewSettUpdate(2)
      },
    },
    audioInputValue: {
      handler: function () {
        this.previewSettUpdate(3)
      },
    },
    audioOutputValue: {
      handler: function () {
        this.previewSettUpdate(4)
      },
    },
    videoQualtiySendValue: {
      handler: function () {
        this.previewSettUpdate(6)
      },
    },
    videoQualtiyRecvValue: {
      handler: function () {
        this.previewSettUpdate(7)
      },
    },
    isDegradationSett: {
      handler: function () {
        this.previewSettUpdate(5)
      },
    },
    currentTab: {
      handler: function (oldTab, newTab) {
        if (This.currentTab == 1) {
          console.log('tab 1')
        } else {
          console.log('tab 2')
        }
      }
    },
  },

  computed: {},

  props: {
    hasCamera: false,
    isExternalUser: false
  },

  created() {
    This = this
  },

  methods: {
    show(videoStream, audioStream) {

      this.hasCameraSett = this.hasCamera
      this.isSaveNew = false
      this.$refs.settingGCDlg.open()
      this.isDialogSett = true
      this.update()

      if (this.videoValue) {
        this.oldVideoValue = this.videoValue
        var constraints = {
          video: {
            optional: [
              {sourceId: this.videoValue}
            ]
          },
          audio: false
        }

        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia(constraints)
            .then(function (stream) {
              $(`#videoPreSett`).show()
              This.streamVideo = stream
            })
            .then(function () {
              $(`#videoPreSett`)[0].srcObject = This.streamVideo;

              if (This.isMirror) {
                $(`#videoPreSett`).addClass('clVideoMirror')
              } else {
                $(`#videoPreSett`).removeClass('clVideoMirror')
              }

              $(`#canvas-stream-mask`).hide()
              $(`#canvas-stream-mask-2`).hide()

            });
        }

        // if(videoStream != null){
        //   $(`#videoPreSett`).show()
        //   $(`#videoPreSett`)[0].srcObject = videoStream;
        //
        //   if(This.isMirror){
        //     $(`#videoPreSett`).addClass('clVideoMirror')
        //   }else{
        //     $(`#videoPreSett`).removeClass('clVideoMirror')
        //   }
        // }else{}
      }

      this.startAudioPreview()
    },

    /**Вызов диалого настроек во время звонка*/
    showCall() {
      this.hasCameraSett = this.hasCamera
      this.isSaveNew = false
      this.isShowCall = true
      this.isDialogSett = true
      this.$refs.settingGCDlg.open()

      this.update()

      if (this.videoValue) {
        var constraints = {
          video: {optional: [{sourceId: this.videoValue}]},
          audio: false
        }
        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia(constraints)
            .then(function (stream) {
              $(`#videoPreSett`)[0].srcObject = stream;
            });
        }
      }
    },

    update() {
      //List microphones.
      This.audioInputSelect = []
      This.audioOutputSelect = []
      This.videoSelect = []

      navigator.mediaDevices.enumerateDevices()
        .then(function (devices) {
          devices.forEach(function (device) {
            if (device.kind == 'audioinput') {
              This.audioInputSelect.push({text: device.label, value: device.deviceId})
            } else if (device.kind == 'audiooutput') {
              This.audioOutputSelect.push({text: device.label, value: device.deviceId})
            } else if (device.kind == 'videoinput') {
              This.videoSelect.push({text: device.label, value: device.deviceId})
            }
          });
        }).catch(function (err) {
        console.error('++NO NO NO++'+err.name + ": " + err.message);
      });

      console.log('++NO NO NO++ audioInputSelect',This.audioInputSelect)
      console.log('++NO NO NO++ audioOutputSelect',This.audioOutputSelect)
      console.log('++NO NO NO++ videoSelect',This.videoSelect)
    },

    startSettingsPreview(_isMirror, _videoValue, _videoQualtiySendValue,
                         _videoQualtiyRecvValue, _audioInputValue, _audioOutputValue) {

      this.isPreSetting = true

      //console.log('updateDev ',_isMirror, ' ', _videoValue, ' ',_videoQualtiySendValue, ' ',_videoQualtiyRecvValue, ' ',_audioInputValue, ' ',_audioOutputValue)

      this.isMirror = Boolean(_isMirror)

      This.videoQualtiySendSelect = [{text: This.$t('gc.video.qualtiy1'), value: '1'}, {text: This.$t('gc.video.qualtiy2'), value: '2'}, {text: This.$t('gc.video.qualtiy3'), value: '3'}]
      This.videoQualtiyRecvSelect = [{text: This.$t('gc.video.qualtiy1'), value: '1'}, {text: This.$t('gc.video.qualtiy2'), value: '2'}, {text: This.$t('gc.video.qualtiy3'), value: '3'}]

      if (!_videoQualtiySendValue) {
        This.videoQualtiySendValue = '1'
      } else {
        This.videoQualtiySendValue = _videoQualtiySendValue
      }

      if (!_videoQualtiyRecvValue) {
        This.videoQualtiyRecvValue = '1'
      } else {
        This.videoQualtiyRecvValue = _videoQualtiyRecvValue
      }

      if (this.isMirror) {
        $(`#videoPreSett`).addClass('clVideoMirror')
      } else {
        $(`#videoPreSett`).removeClass('clVideoMirror')
      }

      if (navigator.mediaDevices.getUserMedia) {

        // var constraintsAudioNoVideo = {
        //   video: false,
        //   audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 1.5}]}
        // }
        // let chunks = [];

        if (!navigator.mediaDevices || !navigator.mediaDevices.enumerateDevices) {
          console.log("dialogSettingsCall enumerateDevices() not supported.");

          return false;
        }
        else{
          console.log('startSettingsPreview _audioInputValue ',_audioInputValue)
          console.log('startSettingsPreview _audioOutputValue ',_audioOutputValue)
          navigator.mediaDevices.enumerateDevices()
            .then(function (devices) {
              devices.forEach(function (device) {
                console.log('++NO NO NO++__ device.kind '+device.kind+' '+device.deviceId)
                if (device.kind == 'audioinput') {
                  This.audioInputSelect.push({text: device.label, value: device.deviceId})

                  if (device.deviceId === _audioInputValue) {
                    console.log('_audioInputValue',_audioInputValue)
                    This.audioInputValue = _audioInputValue
                  }

                } else if (device.kind == 'audiooutput') {
                  This.audioOutputSelect.push({text: device.label, value: device.deviceId})
                  if (device.deviceId === _audioOutputValue) {
                    This.audioOutputValue = _audioOutputValue
                  }

                } else if (device.kind == 'videoinput') {
                  This.videoSelect.push({text: device.label, value: device.deviceId})

                  if (device.deviceId === _videoValue) {
                    This.videoValue = _videoValue
                  }
                }
              })

            })
            .catch(function (err) {
              console.error('updateDev ', err)
              This.$emit('onErrorDevices', err, 6)
            })
            .then(() => {

              if (This.videoSelect.length == 0) {
                console.log('Не выбрана камера')
              }

              This.$emit('onSaveSettingCall', This.audioInputValue, This.audioOutputValue, This.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, This.isMirror, This.isDegradationSett)

              if(!_audioInputValue || !_audioOutputValue){
                if (!_audioInputValue) {
                  This.audioInputValue = This.audioInputSelect[0].value
                  console.log('все таки нет, тогда  ',This.audioInputValue)
                }
                if (!_audioOutputValue) {
                  This.audioOutputValue = This.audioOutputSelect[0].value
                  console.log('все таки нет, тогда  ',This.audioOutputValue)
                }
                This.$emit('onSaveSettingCall', This.audioInputValue, This.audioOutputValue, This.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, This.isMirror, This.isDegradationSett)
              }

              if (!This.videoValue) {
                This.videoValue = This.videoSelect[0].value
              }

              var constraintsNoAudio = {
                video: {optional: [{sourceId: This.videoValue}]},
                audio: false,
              }

              if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                navigator.mediaDevices.getUserMedia(constraintsNoAudio).then(function (stream) {

                  try {
                    stream.getTracks().forEach(function (track) {
                      track.stop();
                      console.log('track stop updateDev ')
                    });
                  } catch (e) {
                    console.error('e 1 ', e)
                  }
                  This.getValue()

                  // This.$emit('onErrorDevices', 'no error', 0)
                  This.isVideo = true
                  This.isStartControlVideo = true
                  This.startControl()
                }).catch(function (err) {
                  This.isVideo = false
                  This.isStartControlVideo = true
                  This.startControl()
                  console.log("onErrorDevices video "+This.isVideo+" err ",err)
                  //This.$emit('onErrorDevices', err, 3)
                });

                // Проверка доступности микрофона
                navigator.mediaDevices.getUserMedia({ audio: true }).then(function (microphoneStream) {
                  try {
                    microphoneStream.getTracks().forEach(function (track) {
                      track.stop();
                      console.log('Микрофон остановлен');
                    });
                  } catch (e) {
                    console.error('Ошибка при остановке микрофона:', e);
                  }
                  console.log('Микрофон успешно остановлен');

                  // Обработка успешного завершения
                  This.getValue();
                  This.isAudio = true
                  This.isStartControlAudio = true
                  This.startControl()
                }).catch(function (err) {
                  This.isAudio = false
                  This.isStartControlAudio = true
                  This.startControl()
                  console.log("onErrorDevices audio "+This.isAudio+" err ",err)
                });
              }
              else {
                console.log('error, navigator.mediaDevices && navigator.mediaDevices.getUserMedia = false')
              }

            })
            .catch(function (err) {
              console.log('error, .catch(function (err) { ',err)
              This.$emit('onErrorDevices', err, 6)
            })
        }
      }
      else {
        This.$emit('onErrorDevices', 'getUserMedia не поддерживается в вашем браузере!', 1)
      }

      this.isPreSetting = false
    },

    startControl(){
      if(This.isStartControlAudio && This.isStartControlVideo){
        if(This.isVideo && This.isAudio){
          This.$emit('onErrorDevices', 'Успешно завершено', 0);
        }else if(This.isVideo && !This.isAudio){
          This.$emit('onErrorDevices', "нет микрофона", 7)
        }else if(!This.isVideo && This.isAudio) {
          This.$emit('onErrorDevices', "нет камеры", 3)
        }else{
          This.$emit('onErrorDevices', "нет камеры и микрофона", 8)
        }
      }else{
        console.log('startControl пока нет данных')
      }
    },

    // получить предварительные настройки для звонка
    updateDev(){

    },

    previewSettUpdate(num) {
      if(!This.isPreSetting){
        This.isSaveNew = true

        var constraints = {
          video: {optional: [{sourceId: This.videoValue}]},
          audio: false,
        }

        // if (this.videoValue == "") {
        //   constraints = {
        //     video: false,
        //     audio: true
        //   }
        // }

        if (num == 2) {
          console.log('поменяли видео устройство на ', This.videoValue)
          if (this.videoValue != "") {
            navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
              This.hasCameraSett = true
              if ($(`#videoPreSett`)[0]) {
                This.stopStreamLocalVideo(4)
                $(`#videoPreSett`)[0].srcObject = stream;
              }
            }).catch(function (err) {
              This.hasCameraSett = false
              This.stopStreamLocalVideo(3)
              $(`#videoPreSett`)[0].srcObject = null;
              //This.$emit('onErrorDevices', err, 6)
            });
          }


        }
        else if(num == 3) {

          if(this.isDialogSett){
            this.startAudioPreview()
          }else{
            console.log('диалог закрыт')
          }
          console.log('navigator.mediaDevices num == 3')
        }
      }
    },

    startAudioPreview(){
      navigator.mediaDevices.getUserMedia({
        audio: {optional: [{sourceId: This.audioInputValue}, {echoCancellation: true}, {noiseSuppression: true}, {autoGainControl: true}, {volume: 0.0}]}
      })
        .then(function(stream) {

          This.stopAudioTrackPreview()

          setTimeout(()=>{
            STREAM_AUDIO_PREVIEW = stream
            // Создаем аудио элемент
            // const audio = document.createElement('audio');

            const videoPreSett = document.getElementById('videoPreSett');


            // Устанавливаем устройство вывода аудио



            videoPreSett.setSinkId(This.audioOutputValue)
              .then(function() {
              console.log('Устройство вывода аудио установлено');
            })
              .catch(function(err) {
                console.log('Ошибка при установке устройства вывода аудио: ', err);
              });

            // audio.setSinkId(This.audioOutputValue)
            //   .then(function() {
            //     console.log('Устройство вывода аудио установлено');
            //   })
            //   .catch(function(err) {
            //     console.log('Ошибка при установке устройства вывода аудио: ', err);
            //   });

            //audio.volume = 0; // This will mute the audio


            // navigator.mediaDevices.selectAudioOutput()
            //   .then(deviceId => {
            //     // Используйте deviceId для установки устройства вывода аудио
            //     console.log('deviceId для установки устройства вывода аудио', deviceId);
            //   })
            //   .catch(error => {
            //     console.error('Error selecting audio output device.', error);
            //   });

            // // Отключите свой собственный аудиопоток
            // const audioTracks = stream.getAudioTracks();
            // audioTracks.forEach(track => track.enabled = false);

            var audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            var analyser = audioCtx.createAnalyser();
            var source = audioCtx.createMediaStreamSource(STREAM_AUDIO_PREVIEW);
            source.connect(analyser);
            analyser.connect(audioCtx.destination);
            analyser.fftSize = 32;
            var bufferLength = analyser.frequencyBinCount;
            var dataArray = new Uint8Array(bufferLength);

            var element1 = document.getElementById('canvasAudioSet1');
            var element2 = document.getElementById('canvasAudioSet2');
            // var element3 = document.getElementById('canvasAudioSet3');
            var element3 = document.getElementById('iconAudioSet3');
            var element4 = document.getElementById('canvasAudioSet4');
            var element5 = document.getElementById('canvasAudioSet5');

            function draw() {
              analyser.getByteFrequencyData(dataArray);

              var opacity1 = dataArray[4] / 55.0;
              var opacity2 = dataArray[2] / 255.0;
              var opacity3 = 0.1+dataArray[0] / 55.0;
              var opacity4 = dataArray[3] / 255.0;
              var opacity5 = dataArray[6] / 55.0;

              element1.style.opacity = opacity1;
              element2.style.opacity = opacity2;
              element3.style.opacity = opacity3;
              element4.style.opacity = opacity4;
              element5.style.opacity = opacity5;

              requestAnimationFrame(draw);
            }

            draw();
          },300)
        })
        .catch(function(err) {
          console.log(err.name + ": " + err.message);
        });
      },

    clickTestSpeaker (){
      const audio = new Audio(ringTone);
      audio.setSinkId(This.audioOutputValue);
      audio.play();
      This.isPlayAudioTest = true
      setTimeout(()=>{      This.isPlayAudioTest = false},3000)
    },

    clickNumFon(num) {
      this.numSettFon = num
      if (num == 0) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask').captureStream()
        This.$emit('setBackground', '')
      } else if (num == 1) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon1_)
      } else if (num == 2) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon2_)
      } else if (num == 3) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon3_)
      } else if (num == 4) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon4_)
      } else if (num == 5) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon5_)
      } else if (num == 6) {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask-2').captureStream()
        This.$emit('setBackground', this.fon6_)
      } else {
        $(`#videoPreSett`)[0].srcObject = document.getElementById('canvas-stream-mask').captureStream()
        This.$emit('setBackground', '')
      }
      this.previewSettUpdate()
    },

    getValue() {
      this.$emit('getValue', this.audioInputValue, this.audioOutputValue, this.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, this.isDegradationSett)
    },

    onClose() {

      This.stopAudioTrackPreview()

      if (this.oldVideoValue != this.videoValue) {
        try {
          if (This.streamVideo != null) {
            This.streamVideo.getTracks().forEach(function (track) {
              if (track.kind == 'video') {
                track.stop();
                console.log('onCancel strVideo track.stop()')
              }
            });
            This.streamVideo = null
          }
          This.stopStreamLocalVideo(6)
        } catch (e) {
          console.error('e 3 ', e)
        }
      }
      this.videoValue = this.oldVideoValue

      this.isDialogSett = false
    },

    onCancel() {

      if (this.oldVideoValue != this.videoValue) {
        try {
          if (This.streamVideo != null) {
            This.streamVideo.getTracks().forEach(function (track) {
              if (track.kind == 'video') {
                track.stop();
                console.log('onCancel strVideo track.stop()')
              }
            });
            This.streamVideo = null
          }
          This.stopStreamLocalVideo(2)
        } catch (e) {
          console.error('e 3 ', e)
        }
      }
      this.videoValue = this.oldVideoValue

      This.stopAudioTrackPreview()

      this.isDialogSett = false
      this.$refs.settingGCDlg.close()
    },

    onSave() {
      try {
        if (This.streamVideo != null) {
          This.streamVideo.getTracks().forEach(function (track) {
            if (track.kind == 'video') {
              track.stop();
              console.log('track.stop() vs onSave')
            }
          });
          This.streamVideo = null
        }
        This.stopStreamLocalVideo(1)
      } catch (e) {
        console.error('e 4 ', e)
      }

      This.stopAudioTrackPreview()

      if (This.hasCameraSett) {
        This.$emit('onErrorDevices', '', 0)
      } else {
        This.$emit('onErrorDevices', 'noCameraStream', 6)
      }

      This.oldVideoValue = This.videoValue

      if (this.isSaveNew) {
        if (this.isShowCall) {
          this.isShowCall = false
          This.$emit('onSaveSettingCall', This.audioInputValue, This.audioOutputValue, This.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, This.isMirror, This.isDegradationSett)
        }
        else {
          This.$emit('onSaveSettingMain', This.audioInputValue, This.audioOutputValue, This.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, This.isMirror, This.isDegradationSett,
          this.isMicroWithSpaceBar)
        }
      }
      else {
        This.$emit('onSaveSettingMain', This.audioInputValue, This.audioOutputValue, This.videoValue, This.videoQualtiySendValue, This.videoQualtiyRecvValue, This.isMirror, null,
        this.isMicroWithSpaceBar)
      }

      //   autoGainControl
      //   ConstrainBoolean Объект , который определяет , является предпочтительной ли автоматическая регулировка усиления и / или требуется.
      //   channelCount
      // A, ConstrainULong указывающий количество каналов или диапазон значений количества каналов, которые являются приемлемыми и / или обязательными.
      //   echoCancellation
      // ConstrainBoolean Объект с указанием предпочтительными является ли или нет эхоподавления и / или необходимости.
      //   latency
      // ConstrainDouble Указав задержку или диапазон задержек , которые приемлемы и / или необходимости.
      //   noiseSuppression
      // A, ConstrainBoolean который указывает, является ли подавление шума предпочтительным и / или необходимым.
      //   sampleRate
      // A, ConstrainULong указывающий допустимую и / или требуемую частоту дискретизации или диапазон частот дискретизации.
      //   sampleSize
      // A с ConstrainULong указанием размера выборки или диапазона размеров выборки, которые являются приемлемыми и / или обязательными.
      //   volume
      // A, ConstrainDouble указывающий допустимый и / или требуемый объем или диапазон объемов.


      this.isDialogSett = false
      this.$refs.settingGCDlg.close()
    },

    onResize() {

    },

    clickFon() {
      This.isDegradationSett = !This.isDegradationSett

      if (!This.isDegradationSett) {
        This.numSettFon = -1
      }

    },

    // удалить все треки с VideoJS
    stopStreamLocalVideo(type) {
      if ($(`#videoPreSett`)[0]) {
        if ($(`#videoPreSett`)[0].srcObject) {
          $(`#videoPreSett`)[0].srcObject.getTracks().forEach(function (track) {
            track.stop();
            console.log('track.stop() vjs dlg stopStreamLocalVideo ' + type)
          });
        }
      }
    },

    stopAudioTrackPreview(){
      if(STREAM_AUDIO_PREVIEW){
        console.log('остановить аудио трекп ввезде')
        STREAM_AUDIO_PREVIEW.getTracks().forEach(function(track) {
          track.stop();
        });
      }
    }
  }
}

</script>

<style>
.pErrorDevicesDlgSett {
  font-size: 24px;
  color: red;
  font-weight: 400;
  text-align: center;
  z-index: 1222;
  position: absolute;
  width: calc(38%);
  top: calc(50% - 32px);
  justify-content: center;
  align-items: center;
}

/*.vcheckboxSettings {*/
/*  padding: 41px;*/
/*  margin-top: 0px;*/
/*  margin-left: 8px;*/
/*  font-size: 110%;*/
/*  -ms-transform: scale(1.5);*/
/*  -moz-transform: scale(1.5);*/
/*  -webkit-transform: scale(1.5);*/
/*  -o-transform: scale(1.5);*/
/*  transform: scale(1.5);*/
/*  padding: 10px;*/
/*}*/

/*.vcheckboxLabel {*/
/*  margin-left: 14px;*/
/*  color: #212121;*/
/*  font-size: 16px;*/
/*}*/

.fon-sett-class {
  width: 524px;
  height: 132px;
  border: solid 2px #959595;

  margin-top: -7px;
  border-bottom-left-radius: 8px;
  border-bottom-right-radius: 8px;
  /*position: absolute;*/
  padding-top: 0px;
  overflow-y: auto;
  padding-left: 14px;
  padding-right: 14px;
}

.vDivFon-sett {
  width: 84px;
  height: 46px;
  margin: 6px;
}

.imgFon-sett-svg {
  border: solid 1px #cac9c9;
  width: 84px;
  height: 46px;
  margin: 6px;
  cursor: pointer;
  padding: 8px;
  padding-left: 22px;
  padding-right: 22px;
}

.imgFon-sett {
  width: 84px;
  height: 46px;
  margin: 6px;
  cursor: pointer;
}

i.v-icon.notranslate.vIconFon1.far.fa-check-circle.theme--light {
  font-size: 14px;
  color: #00aa47;
  bottom: 10px;
  left: 73px;
  top: -34px;
}

.hFull {
  height: 100% !important;
}

/*.hFullCalc {*/
/*  !*height: calc(100% - 32px) !important;*!*/
/*  height: 100% !important;*/
/*}*/

.hFull2 {
  height: calc(100% - 2px);
}

.clVideoMirror {
  transform: rotateY(180deg);
  -webkit-transform: rotateY(180deg);
  -moz-transform: rotateY(180deg);
}

/*------------------------------------------------------------------*/
.gcDialogSetting .xr-dialog-card .v-card__title {
  background: var(--v-xr2L2-base);
}

.gcDialogSetting .dialogSet-divVideo {
  width: 100%;
  background: var(--v-xr2D2-base) !important;
  border: solid 3px var(--v-xr2D2-base) !important;
}

.gcDialogSetting .videoSettPrevDiv {
  object-fit: contain;
  width: 100%;
  background: var(--v-xr2D2-base);
}

.gcDialogSetting .v-card {
  background: var(--v-xr2L4-base);
}

.gcDialogSetting .gcSelectPart label {
  background: var(--v-xr2L4-base) !important;
  font-size: 16px !important;
}

.vtabSettGC {
  width: 50%;
  margin: 0;
  padding: 0;
}

.pGCSetting{
  margin-bottom: 0px!important
}

.divA{
  background: red;
}

.dcanvasAudioSet{
  position: absolute;
  margin-top: 1px;
  width: 36px;
  height: 36px;
  border-radius: 27px;
  border: solid 1px rgb(158 146 120);
}

.viconAudioSet{
  position: absolute;
  padding-top: 5px;
  padding-left: 3px;
  font-size: 30px!important;
  color: #62a523!important;
}

.rowSGC{
  padding: 0;
}

.colSGC{
  padding: 0;
}

.colSGC1{
  padding: 0;
  padding-left: 18px;
  padding-top: 18px;
}

.circleAS {
  width: 34px;
  height: 34px;
  margin-top: 2px;
  margin-left: 1px;
  position: absolute;
  border-radius: 50%;
  background-color: rgba(255, 236, 193, 0);
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
}

.rectangleAS {
  width: 4px;
  margin: 1px;
  border-radius: 4px;
  background-color: #62a523;
}

.redAS {
  height: 14px;
}

.greenAS {
  height: 26px;
  opacity: 0!important;
}

.blueAS {
  height: 20px;
  opacity: 0!important;
}

.btnSettingGC {
   border: solid 1px #9e9278;
   margin-top: 6px;
   background: transparent!important;
 }

</style>
