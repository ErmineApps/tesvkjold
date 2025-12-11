<template>
<div id="divDevPanel" class="divMainDevPanel" style="display: none">
<p class="pcahtmessagTitle">{{ this.$t('gc.devPanel.title') }}</p>
<v-btn :title="$t('close')" class="btncloseChat" @click="clickCloseDevPanel">
      <span class="fa-layers fa-fw">
        <i class="fas fa-times" style="font-size: 12px; margin-right: 3px; margin-bottom: 4px;"></i>
      </span>
</v-btn>

<v-col class="vcolLinkUser">

  <v-row dense>
    <v-col>
      <!-- Подключить оператора -->
<!--      <XrBtn-->
<!--        v-if="!isExternalUser"-->
<!--        :class="'btnAddNewOper'"-->
<!--        type="hot"-->
<!--        icon="mdi-account-hard-hat-outline"-->
<!--        @click="addNewOper"-->
<!--        :text="$t('gc.addOper')"-->
<!--        :title="$t('gc.addOperTitle')"-->
<!--      ></XrBtn>-->
    </v-col>
  </v-row>

  <div class="divBlockListInfoUser fullHeight">

    <!-------------------------------->
    <!-- Пользователи онлайн -->
    <v-expansion-panels v-model="panel1" :readonly="readonly">
      <v-expansion-panel :readonly="false" class="gc-expansion-panel">
        <v-expansion-panel-header :readonly="readonly">{{ $t('gc.devPanel.title.statsUser') }}
          {{ panel1 == null ? ('(' + itemsUsersInfoCount + ')') : '' }}
<!--          <XrBtn @click="info" type="normal" :text="'info'"/>-->
        </v-expansion-panel-header>
        <v-expansion-panel-content :readonly="readonly" class="gc-log-info-panel">
          <!-------------------------------->
          <v-list color="transparent">
            <v-list-item
              v-for="(item, i) in itemsUsersInfo"
              :key="i"
              class="info-devitem-users"
              disabled
            >

              <row style="width: 100%; max-width: 100%;">
                <v-list-item-content>
                  <v-list-item-icon class="infoDevUserLinsIkon">
                    <img v-if="!item.isscrS && !item.isOper" :src="item.avatar" class="infoDevUserLinsIkonImg">
                    <v-icon v-else :color=item.color class="iconInfoUserOS" v-text="item.icon"></v-icon>
                  </v-list-item-icon>
                  <v-list-item-title :color=item.colorText style="color: black; width: calc(100% - 90px); max-width: calc(100% - 90px);"
                                     v-text="item.senderName"></v-list-item-title>

                                <v-list-item-action v-if="item.isMy" style="width: 80px;max-width: 40px;margin: 0; margin-left: 2px;padding: 0;">

<!--                                  <v-btn v-if="!item.isscrS" :title="$t('gc.isMicUser.dialog.mess')"-->
<!--                                         class="mr-1 btnInfoLinkUser"-->
<!--                                         color="white"-->
<!--                                         fab-->
<!--                                         style="width: 80px;max-width: 40px; margin0:0; margin-left:2px; padding: 0;"-->
<!--                                         @click="clickMic(item)">-->
<!--                                    <v-icon v-if="isEchoAudio" style="font-size: 16px; color: #3d3d3d">fas fa-microphone-alt</v-icon>-->
<!--                                    <v-icon v-else style="font-size: 16px; color: #bf5151 !important;">fas fa-microphone-alt-slash-->
<!--                                    </v-icon>-->
<!--                                  </v-btn>-->
                                </v-list-item-action>
                </v-list-item-content>
                <div style="width: 100%; max-width: 100%;">
<!--                  <div class="ml-0" v-if="!item.isMy">-->
                  <div class="ml-0">
                    <p class="titleDevTypeStats">Аудио</p>
                    <v-list-item-content style="height: 20px;">
<!--                      <p class="pDevPanelAudioTit">audioLevel</p>-->
                      <p class="pDevPanelAudioTit">Уровень звука (0 до 1)</p>
                      <p :id="'pdevAudio1_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content style="height: 20px;">
<!--                      <p class="pDevPanelAudioTit">audioEnergy</p>-->
                      <p class="pDevPanelAudioTit">Звуковая энергия</p>
                      <p :id="'pdevAudio8_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content  v-if="item.isMy" style="height: 20px;">
                      <!--                      <p class="pDevPanelAudioTit">echoReturnLoss</p>-->
                      <p class="pDevPanelAudioTit">Потеря эха</p>
                      <p :id="'pdevAudiomy1_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content v-if="item.isMy" style="height: 20px;">
                      <!--                      <p class="pDevPanelAudioTit">echoReturnLossEnhancement</p>-->
                      <p class="pDevPanelAudioTit">Усиление эхо-сигнала</p>
                      <p :id="'pdevAudiomy2_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content v-if="!item.isMy" style="height: 20px;">
<!--                      <p class="pDevPanelAudioTit">packetsReceived</p>-->
                      <p class="pDevPanelAudioTit">Пакетов получено</p>
                      <p :id="'pdevAudio2_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content v-if="!item.isMy" style="height: 20px;">
                      <p class="pDevPanelAudioTit">Пакетов отброшено</p>
                      <p :id="'pdevAudio3_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content style="height: 20px;">
                      <p class="pDevPanelAudioTit">Пакетов потеряно</p>
                      <p :id="'pdevAudio4_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
                    <v-list-item-content style="height: 20px;">
<!--                      <p class="pDevPanelAudioTit">bufferDelay</p>-->
                      <p class="pDevPanelAudioTit">Буферная задержка Мs</p>
                      <p :id="'pdevAudio5_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
<!--                    <v-list-item-content style="height: 20px;">-->
<!--                      <p class="pDevPanelAudioTit">totalAudioEnergy</p>-->
<!--                      <p :id="'pdevAudio6_'+item.senderId" class="pDevPanelAudio">222</p>-->
<!--                    </v-list-item-content>-->
                    <v-list-item-content style="height: 20px;">
<!--                      <p class="pDevPanelAudioTit">totalSamplesDuration</p>-->
                      <p class="pDevPanelAudioTit">Продолжительность</p>
                      <p :id="'pdevAudio7_'+item.senderId" class="pDevPanelAudio">222</p>
                    </v-list-item-content>
<!--                    <v-list-item-content style="font-size: 12px; color: red">-->
<!--                      <textarea :id="'pdevAudioFull_'+item.senderId" class="pDevPanelAudioFull">222</textarea>-->
<!--                    </v-list-item-content>-->
                    <!--                <v-btn color="white" fab-->
                    <!--                       class="mr-1 btnInfoLinkUser"-->
                    <!--                       :title="$t('gc.deleteuser.dialog')"-->
                    <!--                       @click="clickCloseUser(item)">-->
                    <!--                  <v-icon style="font-size: 16px; color: #3d3d3d">fas fa-times</v-icon>-->
                    <!--                </v-btn>-->
                  </div>
                </div>
              </row>


<!--                  <v-list-item-content>-->
<!--                    <v-list-item-title v-text="item.senderName" style="color: black"-->
<!--                                       :color=item.colorText></v-list-item-title>-->
<!--                  </v-list-item-content>-->





<!--              <v-list-item-action v-if="!item.isMy">-->
<!--                <v-btn color="white" fab-->
<!--                       class="mr-1 btnInfoLinkUser"-->
<!--                       v-if="!item.isscrS"-->
<!--                       :title="$t('gc.isMicUser.dialog.mess')"-->
<!--                       @click="clickMic(item)">-->
<!--                  <v-icon v-if="item.isMic" style="font-size: 16px; color: #3d3d3d">fas fa-microphone-alt</v-icon>-->
<!--                  <v-icon v-else style="font-size: 16px; color: #bf5151 !important;">fas fa-microphone-alt-slash-->
<!--                  </v-icon>-->
<!--                </v-btn>-->
<!--              </v-list-item-action>-->

            </v-list-item>
          </v-list>
          <!-------------------------------->
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
    <!-------------------------------->
  </div>
</v-col>
</div>
</template>

<script>

import $ from "jquery";

var This

export default {
  name: "DevPanel",
  mounted() {
    This = this
  },
  props: {
    itemsUsersInfo: null,         // страница в режиме звонка
  },
  data() {
    return {
      room: '',
      mapInterval: new Map(),
      mapPeerConnection: new Map(),
      titleActiveUs: this.$t('gc.user-online'),
      panel1: 0,
      panel2: 0,
      readonly: false,
      isEchoAudio: false,
    }
  },

  computed: {
    itemsUsersInfoCount: function () {
      return this.itemsUsersInfo.length
    },
  },

  methods: {

    getStatsSend(senderId, peerConnection){
      console.log('111 dev getStatsSend '+senderId)

      var totalAudioEnergyOld = 1;

      try {
        var statsInterval = setInterval(getConnectionStats, 1000);
        This.mapInterval.set(senderId, statsInterval)
        This.mapPeerConnection.set(senderId, peerConnection)
        /* add event handlers, etc. */
      } catch (err) {
        console.error(`Error creating RTCPeerConnection: ${err}`);
      }

      function getConnectionStats() {
        peerConnection.getStats(null).then((stats) => {
          let statsOutput = "";

          // console.log('stats',stats)

          /*
          audioOutputLevel: этот параметр представляет уровень выходного аудиосигнала в dBov (децибелы выше опорного уровня). Это может помочь контролировать громкость аудиопотока и убедиться, что он находится на соответствующем уровне.

          audioInputLevel: этот параметр представляет уровень входного аудиосигнала в dBov. Это может помочь контролировать громкость звука, захваченного микрофоном, и убедиться, что он находится на соответствующем уровне.

          jitterBufferDelay: этот параметр представляет количество времени, которое требуется для буферизации и воспроизведения пакетов из буфера дрожания. Это может помочь контролировать задержку аудиопотока и убедиться, что она находится в допустимых пределах.

          jitterBufferEmittedCount: этот параметр представляет количество пакетов, отправленных из буфера дрожания. Это может помочь отслеживать скорость воспроизведения пакетов из буфера.

          audioPacketLost: этот параметр представляет количество аудиопакетов, потерянных во время передачи. Это может помочь отслеживать скорость потери пакетов и выявлять потенциальные проблемы с перегрузкой сети или другие проблемы.

          totalAudioEnergy и totalSamplesDuration: эти параметры можно использовать для расчета энергии звука и отношения сигнал-шум (SNR) аудиопотока. Они могут помочь отслеживать качество аудиопотока и выявлять потенциальные проблемы с шумом или другие проблемы с качеством звука.

          packetsDiscarded Совокупное количество пакетов RTP, отброшенных буфером дрожания из-за позднего или раннего поступления, т. е. эти пакеты не воспроизводятся. Пакеты RTP, отброшенные из-за дублирования пакетов
          * */

          stats.forEach((report) => {

            if (report.kind === "audio") {
              var jitterBufferDelay = 1;
              var jitterBufferEmittedCount = 1;
              var totalAudioEnergy = -1;
              var totalSamplesDuration = 1;

              Object.keys(report).forEach((statName) => {
                statsOutput += `${statName}: ${report[statName]}<br>\n`;

                if(statName === 'audioLevel'){
                  //console.log('2222 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio1_' + senderId).text((report[statName]*1000).toFixed(0)/1000)
                }else if(statName === 'packetsReceived'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio2_' + senderId).text(report[statName])
                }else if(statName === 'echoReturnLoss'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudiomy1_' + senderId).text(report[statName])
                }else if(statName === 'echoReturnLossEnhancement'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudiomy2_' + senderId).text(report[statName])
                }else if(statName === 'packetsDiscarded'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio3_' + senderId).text(report[statName])
                }else if(statName === 'packetsLost'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio4_' + senderId).text(report[statName])
                }else if(statName === 'jitterBufferDelay'){
                  //console.log('2223 jitterBufferDelay _'+report[statName]+'_')
                  jitterBufferDelay = report[statName]
                }else if(statName === 'jitterBufferEmittedCount'){
                  //console.log('2223 jitterBufferEmittedCount _'+report[statName]+'_')
                  jitterBufferEmittedCount = report[statName]
                }else if(statName === 'totalAudioEnergy'){
                  //console.log('2223 totalAudioEnergy _'+report[statName]+'_')
                  // $('#pdevAudio6_' + senderId).text(report[statName])
                  totalAudioEnergy = report[statName].toFixed(6)
                }else if(statName === 'totalSamplesDuration'){
                  //console.log('2223 totalSamplesDuration _'+report[statName]+'_')
                  $('#pdevAudio7_' + senderId).text(report[statName].toFixed(0))
                  totalSamplesDuration = report[statName].toFixed(2)
                }
                // if(statName === 'jitterBufferDelay'){
                //   //console.log('2224 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio1_' + senderId).text(report[statName])
                // }if(statName === 'jitterBufferEmittedCount'){
                //   console.log('2225 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio2_' + senderId).text(report[statName])
                // }if(statName === ''){
                //   console.log('2226 data _'+statName+'_'+report[statName]+'_')
                // }if(statName === 'audioPacketLost'){
                //   console.log('2227 data _'+statName+'_'+report[statName]+'_')
                // }if(statName === 'totalAudioEnergy'){
                //   console.log('2228 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio3_' + senderId).text(report[statName])
                // } if(statName === 'totalSamplesDuration'){
                //   console.log('2229 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio4_' + senderId).text(report[statName])
                // } else{
                //
                // }
              });

              var bufferDelay =  jitterBufferDelay / jitterBufferEmittedCount
              if(bufferDelay != 1){
                //console.log('bufferDelay _'+bufferDelay+'_')
                $('#pdevAudio5_' + senderId).text(bufferDelay.toFixed(3))
              }

              if(totalAudioEnergy != -1){
                var echo = ((totalAudioEnergy - totalAudioEnergyOld)).toFixed(3)
                $('#pdevAudio8_' + senderId).text(echo)
                totalAudioEnergyOld = totalAudioEnergy
              }
              // console.log('111 getStatsSend statsOutput',statsOutput)
              // $('#pdevAudioFull_' + senderId).text(statsOutput)
            }
          });

          // document.querySelector(".stats-box").innerHTML = statsOutput;
        });
      }
    },

    getStats(senderId, peerConnection){
      console.log('222 dev getStats '+senderId)
      console.log('222 itemsUsersInfo',this.itemsUsersInfo)

      var totalAudioEnergyOld = 1;
      var totalSamplesDurationOld = 1;

      try {
        var statsInterval = setInterval(getConnectionStats, 1000);
        This.mapInterval.set(senderId, statsInterval)
        This.mapPeerConnection.set(senderId, peerConnection)
        /* add event handlers, etc. */
      } catch (err) {
        console.error(`Error creating RTCPeerConnection: ${err}`);
      }

      function getConnectionStats() {
        peerConnection.getStats(null).then((stats) => {
          let statsOutput = "";

          // console.log('stats',stats)

          /*
          audioOutputLevel: этот параметр представляет уровень выходного аудиосигнала в dBov (децибелы выше опорного уровня). Это может помочь контролировать громкость аудиопотока и убедиться, что он находится на соответствующем уровне.

          audioInputLevel: этот параметр представляет уровень входного аудиосигнала в dBov. Это может помочь контролировать громкость звука, захваченного микрофоном, и убедиться, что он находится на соответствующем уровне.

          jitterBufferDelay: этот параметр представляет количество времени, которое требуется для буферизации и воспроизведения пакетов из буфера дрожания. Это может помочь контролировать задержку аудиопотока и убедиться, что она находится в допустимых пределах.

          jitterBufferEmittedCount: этот параметр представляет количество пакетов, отправленных из буфера дрожания. Это может помочь отслеживать скорость воспроизведения пакетов из буфера.

          audioPacketLost: этот параметр представляет количество аудиопакетов, потерянных во время передачи. Это может помочь отслеживать скорость потери пакетов и выявлять потенциальные проблемы с перегрузкой сети или другие проблемы.

          totalAudioEnergy и totalSamplesDuration: эти параметры можно использовать для расчета энергии звука и отношения сигнал-шум (SNR) аудиопотока. Они могут помочь отслеживать качество аудиопотока и выявлять потенциальные проблемы с шумом или другие проблемы с качеством звука.

          packetsDiscarded Совокупное количество пакетов RTP, отброшенных буфером дрожания из-за позднего или раннего поступления, т. е. эти пакеты не воспроизводятся. Пакеты RTP, отброшенные из-за дублирования пакетов
          * */

          stats.forEach((report) => {

            if (report.kind === "audio") {
              var jitterBufferDelay = 1;
              var jitterBufferEmittedCount = 1;
              var totalAudioEnergy = -1;
              var totalSamplesDuration = 1;

              Object.keys(report).forEach((statName) => {
                statsOutput += `${statName}: ${report[statName]}<br>\n`;

                if(statName === 'audioLevel'){
                  //console.log('2222 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio1_' + senderId).text((report[statName]*1000).toFixed(0)/1000)
                }else if(statName === 'packetsReceived'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio2_' + senderId).text(report[statName])
                }else if(statName === 'packetsDiscarded'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio3_' + senderId).text(report[statName])
                }else if(statName === 'packetsLost'){
                  //console.log('2223 data _'+statName+'_'+report[statName]+'_')
                  $('#pdevAudio4_' + senderId).text(report[statName])
                }else if(statName === 'jitterBufferDelay'){
                  //console.log('2223 jitterBufferDelay _'+report[statName]+'_')
                  jitterBufferDelay = report[statName]
                }else if(statName === 'jitterBufferEmittedCount'){
                  //console.log('2223 jitterBufferEmittedCount _'+report[statName]+'_')
                  jitterBufferEmittedCount = report[statName]
                }else if(statName === 'totalAudioEnergy'){
                  //console.log('2223 totalAudioEnergy _'+report[statName]+'_')
                  // $('#pdevAudio6_' + senderId).text(report[statName])
                  totalAudioEnergy = report[statName].toFixed(6)
                }else if(statName === 'totalSamplesDuration'){
                  //console.log('2223 totalSamplesDuration _'+report[statName]+'_')
                  $('#pdevAudio7_' + senderId).text(report[statName].toFixed(0))
                  totalSamplesDuration = report[statName].toFixed(2)
                }
                // if(statName === 'jitterBufferDelay'){
                //   //console.log('2224 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio1_' + senderId).text(report[statName])
                // }if(statName === 'jitterBufferEmittedCount'){
                //   console.log('2225 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio2_' + senderId).text(report[statName])
                // }if(statName === ''){
                //   console.log('2226 data _'+statName+'_'+report[statName]+'_')
                // }if(statName === 'audioPacketLost'){
                //   console.log('2227 data _'+statName+'_'+report[statName]+'_')
                // }if(statName === 'totalAudioEnergy'){
                //   console.log('2228 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio3_' + senderId).text(report[statName])
                // } if(statName === 'totalSamplesDuration'){
                //   console.log('2229 data _'+statName+'_'+report[statName]+'_')
                //   $('#pdevAudio4_' + senderId).text(report[statName])
                // } else{
                //
                // }
              });

              var bufferDelay =  jitterBufferDelay / jitterBufferEmittedCount
              if(bufferDelay != 1){
                //console.log('bufferDelay _'+bufferDelay+'_')
                $('#pdevAudio5_' + senderId).text(bufferDelay.toFixed(3))
              }

              if(totalAudioEnergy != -1){
                var echo = ((totalAudioEnergy - totalAudioEnergyOld)).toFixed(3)
                $('#pdevAudio8_' + senderId).text(echo)
                totalAudioEnergyOld = totalAudioEnergy
              }
              // console.log('222 statsOutput',statsOutput)
              // $('#pdevAudioFull_' + senderId).text(statsOutput)
            }
          });

          // document.querySelector(".stats-box").innerHTML = statsOutput;
        });
      }
    },

    addNewOper() {
      this.$emit('addNewOper')
    },

    clickMic(item) {
      console.log('clickMic')
      console.log(item)
      if (!item.isMic) {
        if (item.senderName.substring(0, 9) == 'Оператор_' || item.senderName.substring(0, 9) == ' Оператор' || item.senderName.substring(0, 9) == 'Operator_' || item.senderName.substring(0, 9) == ' Operator') {
          this.$emit('clickMic', item.senderId, item.senderName)
        }
      } else {
        this.$emit('clickMic', item.senderId, item.senderName)
      }
    },

    clickCloseUser(item) {
      console.log('clickCloseUser')
      console.log(item)
      this.$emit('clickCloseUser', item.senderId, item.senderName)
    },

    clickCloseDevPanel() {
      this.$emit('clickCloseDevPanel')
    },

    addUser(item) {
      console.log('add user ', item)
      this.$emit('addUser', item.senderId, item.senderName)
    },

    disUser(item) {
      this.$emit('disUser', item.senderId, item.senderName)
    },

    info(){
      alert('info')
    }

  }
}
</script>

<style scoped>
i.v-icon.notranslate.divinfo2I.fa.fa-chalkboard-teacher.theme--light.green--text {
  margin-top: 4px;
}

.divMainDevPanel {
  position: fixed;
  width: 400px;
  height: 100%;
  /*background: #fff6e1;*/
  background: var(--xr-gc-pane-background-color);
  z-index: 91000;
  right: 0px;
  top: 0px;
}

.pcahtmessagTitle {
  height: 50px;
  width: 100%;
  padding-top: 12px;
  padding-left: 12px;
  position: absolute;
  z-index: 12;
  border-bottom: solid 1px #02020236 !important;
  /*background: var(--v-xr2L2-base);*/
  background: var(--xr-gc-pane-header-background-color);
  color: var(--xr-gc-pane-header-color);
  font-size: 20px;
}

.info-devitem-users {
  margin-top: 1px;
  margin-bottom: 1px;
  min-height: 1px;
  margin-left: 1px;
  padding: 2px;
}

.divinfo2ISpan {
  margin: 10px;
  margin-right: 16px !important;
  margin-left: 0px;
}

.divinfo2I {
  font-size: 22px;
}

button.fabAddSet {
  margin: 0 !important;
  border-bottom-right-radius: 0px !important;
  border-top-right-radius: 0px !important;
  height: 44px !important;
  position: relative !important;
}

button.mr-1.btnInfoLinkUser.v-btn.v-btn--is-elevated.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default.white {
  pointer-events: all !important;
  font-size: 26px !important;
  width: 34px !important;
  height: 34px !important;
  border-radius: 4px !important;
  border: solid 1px #02020236 !important;
  -webkit-box-shadow: 0 3px 1px -2px rgb(0 0 0 / 0%), 0 2px 2px 0 rgb(0 0 0 / 0%), 0 1px 5px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 3px 1px -2px rgb(0 0 0 / 10%), 0 2px 2px 0 rgb(0 0 0 / 10%), 0 1px 5px 0 rgb(0 0 0 / 10%);
}

button.mr-1.btnInfoLinkUser.v-btn.v-btn--disabled.v-btn--fab.v-btn--has-bg.v-btn--round.theme--light.v-size--default {
  color: red !important;
  pointer-events: all !important;
  font-size: 26px !important;
  width: 34px !important;
  height: 34px !important;
  border-radius: 4px !important;
  border: solid 1px #02020236 !important;
  -webkit-box-shadow: 0 3px 1px -2px rgb(0 0 0 / 0%), 0 2px 2px 0 rgb(0 0 0 / 0%), 0 1px 5px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 3px 1px -2px rgb(0 0 0 / 10%), 0 2px 2px 0 rgb(0 0 0 / 10%), 0 1px 5px 0 rgb(0 0 0 / 10%);
}

.v-list-item__icon.infoDevUserLinsIkon {
  width: 34px;
  max-width: 34px;
  margin-right: 14px !important;
  margin-top: 0;
  padding-top: 4px;
}

.infoDevUserLinsIkonImg {
  width: 32px;
  border-radius: 15px;
}

.iconInfoUserOS {
  border-radius: 35px !important;
  /*border: solid 2px #4caf50 !important;*/
  border: solid 2px #43a047 !important;
  width: 33px;
  height: 33px;
  font-size: 14px;
}

.iconInfoUserOSOg {
  border-radius: 35px !important;
  border: solid 2px #cb5400 !important;
  width: 33px;
  height: 33px;
  font-size: 14px;
}

.divMainDevPanelUser .toolbarUser {
  background: red;
  width: 100%;
  height: 100px;
}

/*=====================================================================*/
.vcolLinkUser {
  margin: 54px 0px 0px 0px;
  padding: 0px;
}

.vcolLinkUser .btnAddNewOper {
  float: right;
}

.divBlockListInfoUser {
  margin-top: 4px;
  margin-left: 0px;
  margin-right: 0px;
  overflow-y: auto;
}

.v-expansion-panel-content__wrap{
  overflow-y: auto;
  height: calc(100% - 50px);
}

/*---------------------------------------------------------------------*/
.gc-expansion-panel {
  border-top-left-radius: 0px !important;
  border-top-right-radius: 0px !important;
}

.gc-expansion-panel.v-expansion-panel:before {
  box-shadow: none !important;
  -webkit-box-shadow: none !important;
}

.gc-expansion-panel > .v-expansion-panel-header {
  padding-left: 10px;
  /*font-family: 'Rooftop-Regular', "Helvetica Neue", "Segoe UI", Helvetica, Arial, sans-serif !important;*/
  font-family: var(--xr-main-font-family);
  font-weight: normal;
  font-size: 20px;
  background: var(--v-xr2D3-base);
  height: 50px;
  color: white;
}

.gc-expansion-panel >>> .v-expansion-panel-content__wrap {
  padding-left: 10px;
  padding-right: 5px;
  padding-top: 0px;
  padding-bottom: 0px;
  background: var(--v-xr2L5-base);
}

.gc-expansion-panel > .v-expansion-panel-header--active {
  border-bottom: solid 1px rgba(2, 2, 2, 0.13);
  min-height: 39px;
}

/*------------------------------------------------------------------*/
::-webkit-scrollbar {
  height: 6px; /* высота для горизонтального скролла */
  width: 6px;
}

::-webkit-scrollbar-track {
  background: #343434;
}

::-webkit-scrollbar-thumb {
  background: var(--v-xr2L1-base);
  border-radius: 2px;
}

::-webkit-scrollbar-thumb:vertical:hover,
::-webkit-scrollbar-thumb:horizontal:hover {
  background: var(--v-xr2L1-base);
}

:hover::-webkit-scrollbar-thumb {
  background: var(--v-xr2L1-base);
}
/*------------------------------------------------------------------*/
.pDevPanelAudio{
  max-width: 150px;
  color: black;
  font-size: 12px;
  margin-bottom: 2px;
}

.pDevPanelAudioTit{
  max-width: 168px;
  color: black;
  font-size: 12px;
}

.pDevPanelAudioFull{
  max-width: 100%;
  width: 100%;
  font-size: 12px;
}

.titleDevTypeStats{
  font-size: 16px;
  margin-bottom: 2px;
  padding-left: 0px;
  background: #00000014;
  color: black;
}
</style>
