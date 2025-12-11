<!--suppress ALL -->
<template>
  <div>
    <XrToolbar :breadcrumbs="breadcrumbs" :isMini="true" :toolbarTitle="toolbarTitle">

      <XrBtn v-t="'expert.stats.btn.return'" :title="$t('expert.stats.btn.return.title')" icon="mdi mdi-arrow-left-bold"
             @click="closeStats"/>

      <template v-slot:xrTabs>
        <v-tabs v-model="currentTab" centered grow>
          <v-tab key='chartCall' v-t="'expert.stats.tab.graph'"></v-tab>
<!--          <v-tab key='detailTable' v-t="'expert.stats.tab.data-oper'"></v-tab>-->
        </v-tabs>
      </template>

    </XrToolbar>
    <!--      style="height: calc(80% - 4px);"-->
    <v-tabs-items v-model="currentTab">
      <v-tab-item key='chartCall'>

        <div class="tabs_border" style="width: 100%;">

          <div class="row">
            <div class="col col-12 apex-col-auto row">
              <div id="idDivInfo" class="col col-3 apex-col-auto">
                <div><p v-t="'expert.info.info-call'" class="vinfocall"></p></div>
                <div class="wr">
                  <div class="item">
                    <svg class="item_block" viewBox="0 0  400 200">
                      <foreignObject class="item_block_mu" height="100%" width="100%">
                        <table cellpadding="4" cellspacing="0" style="width: 100%; border: solid 0px #02020236;">
                          <tr>
                            <td v-t="'gc.stats.graph.main.info.name.gc'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{gcName}}</td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.main.info.name.us'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{gcUserName}}</td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.main.info.name.date'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{dateStart}}</td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.main.info.name.dateSS'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL" valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">
                              {{timeCall}}
                            </td>
                          </tr>
                          <tr>
                            <td v-t="'ip:port'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL" valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{ipPort}}</td>
                          </tr>
                        </table>
                      </foreignObject>
                    </svg>
                  </div>
                </div>

                <div><p v-t="'expert.info.analysis-data'" class="vinfocall"></p></div>
                <div class="wr">
                  <div class="item">
                    <svg class="item_block" viewBox="0 0  400 300">
                      <foreignObject class="item_block_mu" height="100%" width="100%">

                        <table cellpadding="4" cellspacing="0" style="width: 100%; border: solid 0px #02020236;">
                          <tr>
                            <td v-t="'expert.info.net-name'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">
                              {{infoCallNameNet}}
                            </td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.info.call.net'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td :title="titleNoise" class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN"
                                valign="top">
                              {{infoCallNet}}
                            </td>
                            <!--                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{data1}}</td>-->
                          </tr>
                          <tr >
                            <td v-t="'gc.stats.info.call.video'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td :title="titleSend" class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN"
                                valign="top">
                              {{infoCallVideo}}
                            </td>
                            <!--                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{data1}}</td>-->
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.info.call.audio'" class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL"
                                valign="top">
                            </td>
                            <td :title="titleLink" class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN"
                                valign="top">
                              {{infoCallAudio}}
                            </td>
                          </tr>
                          <!--                          <tr>-->
                          <!--                            <td class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL" valign="top">Канал связи-->
                          <!--                            </td>-->
                          <!--                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">Канал низкий-->
                          <!--                            </td>-->
                          <!--                          </tr>-->
                          <!--                          <tr>-->
                          <!--                            <td class="t-Report-cell-xr_sett-h" headers="PARAM_LABEL" valign="top">Настройки соединения-->
                          <!--                            </td>-->
                          <!--                            <td class="t-Report-cell-xr_sett-2" headers="PARAM_VALUE_PLAN" valign="top">{{data5}}</td>-->
                          <!--                          </tr>-->
                        </table>
                      </foreignObject>
                    </svg>
                  </div>
                </div>

                <div><p v-t="'expert.info.Comm-channel-and-bit'" class="vinfocall"></p></div>
                <div class="wr">
                  <div class="item">
                    <svg class="item_block" viewBox="0 0  400 270">
                      <foreignObject class="item_block_mu" height="100%" width="100%">

                        <table cellpadding="4" cellspacing="0"
                               style="width: 100%; border: solid 0.5px rgb(233, 238, 242);">
                          <tr>
                            <th v-t="'expert.info.Value'" align="left" class="t-Report-cell-xr_sett-hed"
                                colspan="1"></th>
                            <th v-t="'expert.info.Norm'" align="left" class="t-Report-cell-xr_sett-hed"
                                colspan="1"></th>
                            <th v-t="'expert.info.Average'" align="left" class="t-Report-cell-xr_sett-hed"
                                colspan="2"></th>
                          </tr>


                          <tr>
                            <td v-t="'gc.stats.graph.bitEncode'" class="t-Report-cell-xr_sett" headers="PARAM_LABEL">
                            </td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_PLAN">{{2500}}</td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_AVG">{{midBitrate}}</td>
                            <td align="center" class="t-Report-cell-xr_sett" headers="GRAPH" style="width: 60px">

                              <button
                                class="v-btn_ot_stats btn_stats_chars"
                                title=""
                                type="button"
                                @click="getSett(1)">
                                <span aria-hidden="true" class="fa fa-chart-bar v-btn_ot_stats_sp"></span>
                              </button>
                            </td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.rttJitterV'" class="t-Report-cell-xr_sett" headers="PARAM_LABEL">
                            </td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_PLAN">{{30}}</td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_AVG">{{midRttVideo}}</td>
                            <td align="center" class="t-Report-cell-xr_sett" headers="GRAPH">
                              <button
                                class="v-btn_ot_stats btn_stats_chars"
                                title=""
                                type="button"
                                @click="getSett(2)">
                                <span aria-hidden="true" class="fa fa-chart-bar v-btn_ot_stats_sp"></span>
                              </button>
                            </td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.rttJitterA'" class="t-Report-cell-xr_sett"
                                headers="PARAM_LABEL">
                            </td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_PLAN">{{30}}</td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_AVG">{{midRttAudio}}</td>
                            <td align="center" class="t-Report-cell-xr_sett" headers="GRAPH">
                              <button
                                class="v-btn_ot_stats btn_stats_chars"
                                title=""
                                type="button"
                                @click="getSett(3)">
                                <span aria-hidden="true" class="fa fa-chart-bar v-btn_ot_stats_sp"></span>
                              </button>
                            </td>
                          </tr>
                          <tr>
                            <td v-t="'gc.stats.graph.packerLast'" class="t-Report-cell-xr_sett"
                                headers="PARAM_LABEL">
                            </td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_PLAN">{{0}}</td>
                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_AVG">{{0}}</td>
                            <td align="center" class="t-Report-cell-xr_sett" headers="GRAPH">
                              <button
                                class="v-btn_ot_stats btn_stats_chars"
                                title=""
                                type="button"
                                @click="getSett(4)">
                                <span aria-hidden="true" class="fa fa-chart-bar v-btn_ot_stats_sp"></span>
                              </button>
                            </td>
                          </tr>
                          <!--                          <tr>-->
                          <!--                            <td class="t-Report-cell-xr_sett" headers="PARAM_LABEL">FPS эксперта (от-->
                          <!--                              оператора)-->
                          <!--                            </td>-->
                          <!--                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_PLAN">{{data11s}}</td>-->
                          <!--                            <td class="t-Report-cell-xr_sett" headers="PARAM_VALUE_AVG">{{data11}}</td>-->
                          <!--                            <td class="t-Report-cell-xr_sett" align="center" headers="GRAPH">-->
                          <!--                              <button-->
                          <!--                                class="v-btn_ot_stats btn_stats_chars"-->
                          <!--                                type="button"-->
                          <!--                                @click="getSett(1)"-->
                          <!--                                title="">-->
                          <!--                                <span class="fa fa-chart-bar v-btn_ot_stats_sp" aria-hidden="true"></span>-->
                          <!--                              </button>-->
                          <!--                            </td>-->
                          <!--                          </tr>-->
                        </table>
                      </foreignObject>
                    </svg>
                  </div>
                </div>
              </div>
              <div id="idDivGraph" class="col col-9 apex-col-auto">
                <div class="col col-12 apex-col-auto row">
                  <div class="col col-6 apex-col-auto" style="padding: 0">
                    <div id="graph-quality"
                         class="t-Region chart-region t-Region--noUI t-Region--scrollBody js-apex-region">
                      <div class="t-Region-header">
                        <div class="t-Region-headerItems t-Region-headerItems--title">
                          <div id="chart-line"></div>
                          <!--                      <apexchart  type="area" :group="social" :options="optionsGrafStats"></apexchart>-->
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col col-6 apex-col-auto" style="padding: 0">
                    <div id="graph-wifi"
                         class="t-Region chart-region t-Region--noUI t-Region--scrollBody js-apex-region">
                      <div class="t-Region-header">
                        <div class="t-Region-headerItems t-Region-headerItems--title">
                          <div id="chart-line3"></div>
                          <!--                      <apexchart type="area" :group="social"  :options="optionsWiFiStats"></apexchart>-->
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="col col-12 apex-col-auto row">
                  <div class="col col-6 apex-col-auto" style="padding: 0">
                    <div
                      id="graph-params"
                      class="t-Region chart-region param-graph t-Region--removeHeader t-Region--noUI t-Region--scrollBody js-apex-region">
                      <div class="t-Region-header">
                        <div class="t-Region-headerItems t-Region-headerItems--title">
                          <div id="chart-line4"></div>
                          <!--                            <apexchart type="area" :options="optionsParStats"></apexchart>-->
                        </div>
                        <div class="t-Region-headerItems t-Region-headerItems--buttons"><span
                          class="js-maximizeButtonContainer"></span></div>
                      </div>
                    </div>

                  </div>
                  <div class="col col-6 apex-col-auto" style="padding: 0">
                    <div id="graph-cpu"
                         class="t-Region chart-region t-Region--noUI t-Region--scrollBody js-apex-region">
                      <div class="t-Region-header">
                        <div class="t-Region-headerItems t-Region-headerItems--title">
                          <div id="chart-line2"></div>
                          <!--                      <apexchart type="area" :group="social"  :options="optionsCpuStats"></apexchart>-->
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </v-tab-item>
<!--      <v-tab-item key='detailTable'>-->

<!--        <TableStatsGC ref="statsDetailGCTable" :data="dataStats" :ex=true :isMedia=false :isVideo=false-->
<!--                        name="telStatsGC"></TableStatsGC>-->

<!--      </v-tab-item>-->
    </v-tabs-items>
  </div>

</template>

<script>

import expertRU from '@/lang/expert-ru.json'
import expertEN from '@/lang/expert-en.json'

var This

  export default {
    name: "statisticGC",

    data() {
      return {
        titleName: this.$t('expert.info.Call-statistics'),
        currentTab: 0,
        sessionId: this.$route.params.sessionId,
        dateStart: '',
        gcName: '',
        gcUserName: '',
        ipPort: '',
        timeCall: '',
        dataStats: {},
        arBitrate: [],
        midBitrate: '',
        arEncodeSpeed: [],
        midEncodeSpeed: '',
        arJitterAudio: [],
        arRttAudio: [],
        midRttAudio: '',
        arPacketsLostAudio: [],
        arPacketsLostVideo: [],
        arJitterVideo: [],
        midJitterVideo: '',
        arRttVideo: [],
        midRttVideo: '',
        arAudioLevel: [],
        arTotalAudioEnergy: [],
        arWidth: [],
        arHeight: [],
        arFPS: [],
        arWifiNoise: [],
        arWifiLink: [],
        arWifiLevel: [],
        arSimType: "",
        arSimRSSI: [],
        arSimRSRP: [],

        optionsGrafStats: {},
        optionsCpuStats: {},
        optionsWiFiStats: {},
        optionsNoWiFiStats: {},
        optionsParStatsBitrateEncode: {},
        optionsParStatsRTTJitterVideo: {},
        optionsParStatsRTTJitterAudio: {},
        optionsParStatsPacketLost: {},

        infoCallNameNet: '',
        infoCallNet: '',
        infoCallVideo: '',
        infoCallAudio: '',

        heightChart: 50,

        dialog: false,
        dataFps2: [32, 31, 30],

        dialogDetail: false,
        tabDataDetail: [],
        tabOptDetail: null,
        tdd: [],
        nameDetail: this.$t('expert.info.Detailed-statistics'),

        dataEx: [],   // данные эксперта
        dataOper: [], // данные оператора
        dataOperVideo:[],
        dataOperAudio:[],
        arTime: ['0:00', '0:01', '0:02', '0:03', '0:04', '0:05'],   // вермя

        startDate: "",
        duration: "",
        nameOper: "",
        nameExpert: "",
        data5: "",
        data6: "",
        data7: "",
        data8: "",
        data9: "",
        data10: "",
        data11: "",
        data6s: "",
        data7s: "",
        data8s: "",
        data9s: "",
        data10s: "",
        data11s: "",

        arRtt: [],
        arRttVideo: [],
        arRttAudio: [],
        arPacketsLost: [],
        arTargetEncBitrate: [],
        arActualEncBitrate: [],
        arBucketDelay: [],

        chartStats: null,
        chartLineCPU: null,
        chartAreaWiFi: null,
        chartCountData: null,

        o_o_framerate: [],
        o_o_link: [],
        o_o_noise: [],

        o_o_cpu: [],
        o_o_memory: [],
        o_o_memoryx: [],

        e_o_framerate: [],
        e_o_oper_height: [],
        sett_fps: [],
        sett_height: [],

        dataParamChart: {},

        sDv: {},
        sEx: {},

        ar6: [],
        ar7: [],
        ar8: [],
        ar9: [],
        ar10: [],
        ar11: [],
        arTime: [],

        wChart: window.innerWidth * 0.3,
        hChart: window.innerHeight * 0.3,

        optionsParStats: {},

        options: {
          chart: {
            id: 'vuechart-example'
          },
          xaxis: {
            categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998]
          }
        },

        series2: [],
        cpuGraf: [],
        cpuGraf: [],
        WiFiGraf: [],

        titleNoise: this.$t('expert.info.info.title1'),
        titleLink: this.$t('expert.info.info.title2'),
        titleVideo: this.$t('expert.info.info.title3'),

        titleSend: this.$t('expert.info.info.data.net-info'),

        infoNameWiFi: this.$t('expert.no-data'),
        infoNoiseWiFi: '',
        infoLinkWiFi: '',
        infoQualityVideo: '',
        isWiFi: true,

        mediumBitrateExp: 0,
        mediumBandwidthrExp: 0,
        mediumBitrateOper: 0,
        mediumBandwidthrOper: 0,
        mediumRtt: 0,

        normBitrate: 2500,
        normBandwidthr: 2500,
        normRtt: 150,
      }
    },

    created() {
      This = this
      this.$root.setLocaleModule(expertRU, expertEN)
      this.lang = this.$i18n.locale

      this.titleName = this.$t('expert.info.Call-statistics')
      this.titleNoise = this.$t('expert.info.info.title1')
      this.titleLink = this.$t('expert.info.info.title2')
      this.titleVideo = this.$t('expert.info.info.title3')

      document.title = this.titleName;
      this.updateStatsNew(this.sessionId)

      setTimeout(() => {
        $('#idDivInfo').css("height", window.innerHeight - 202 + "px")
        $('#idDivGraph').css("height", window.innerHeight - 202 + "px")
        $('#idDivInfo').css("overflow", 'auto')
        $('#idDivGraph').css("overflow", 'auto')
      })

      window.onresize = () => {
        This.updateWidth()
      }
    },

    mounted() {
      This = this
    },

    computed: {
      toolbarTitle() {
        return this.titleName
      },

      breadcrumbs() {
        let res = [{
          text: this.$t('remoteAssistant'),
          disabled: false,
          href: `#/webCallMain/${this.wpId}/ex`,
        }]

        // res.push({
        //   text: this.wpId,
        //   disabled: false,
        //   href: `#/webCallWpStats/${this.wpId}`,
        // })

        return res
      },


    },

    methods: {

      updateWidth() {
        $('#idDivInfo').css("height", window.innerHeight - 202 + "px")
        $('#idDivGraph').css("height", window.innerHeight - 202 + "px")
        $('#idDivInfo').css("overflow", 'auto')
        $('#idDivGraph').css("overflow", 'auto')
      },


      showStats(wp_id, sessionId) {
        // alert(wp_id)
        // this.wpId = wp_id
        // this.sessionId = sessionId
        // this.updateStatsNew(this.sessionId)
      },

      closeStats() {
        const uriW = `/webCallMain/${this.wpId}/ex`
        let routeData = this.$router.resolve(uriW)
        window.open(routeData.href, this.$t('remoteAssistant'))
        window.close()
      },

      getSett(id) {
        this.renderChart(id)
      },

      openDetailDialog(type) {
        if (type == 'ex') {
          this.tabDataDetail = this.dataEx
          this.nameDetail = 'Подробная статистика эксперта'

          //this.updateTableStats()
        } else if (type == 'oper') {
          this.tabDataDetail = this.dataOper
          this.nameDetail = 'Подробная статистика оператора'
          //this.updateTableStats()
        } else {
          this.tabDataDetail = this.dataEx
          this.nameDetail = 'Подробная статистика эксперта'
          //this.updateTableStats()
        }

        this.$refs.statsDetailDlg.show(this.tabDataDetail, this.nameDetail)

        //this.dialogDetail = true
      },

      updateStatsNew(sessionId) {
        this.tabData = []

        return this.$root.restGet(`/groupCall/statsGCinfo/${sessionId}`)
          .then(r => {
            console.log('updateStatsNew')
            console.log(r)

            this.dataStats = r.dataStats;

            this.dateStart = r.dateStart;
            this.gcName = r.gcName;
            this.gcUserName = r.gcUserName;
            this.ipPort = r.ipPort;
            this.timeCall = r.timeCall;

            this.infoCallNameNet= r.infoCallNameNet ;
            this.infoCallNet = r.infoCallNet ;
            this.infoCallVideo = r.infoCallVideo ;
            this.infoCallAudio = r.infoCallAudio ;

            this.arBitrate = r.arBitrate;
            this.midBitrate = r.midBitrate;
            this.arEncodeSpeed = r.arEncodeSpeed;
            this.midEncodeSpeed = r.midEncodeSpeed;
            this.arJitterAudio = r.arJitterAudio;
            this.arRttAudio = r.arRttAudio;
            this.midRttAudio = r.midRttAudio;
            this.arPacketsLostAudio = r.arPacketsLostAudio;
            this.arPacketsLostVideo = r.arPacketsLostVideo;
            this.arJitterVideo = r.arJitterVideo;
            this.midJitterVideo = r.midJitterVideo;
            this.arRttVideo = r.arRttVideo;
            this.midRttVideo = r.midRttVideo;
            this.arAudioLevel = r.arAudioLevel;
            this.arTotalAudioEnergy = r.arTotalAudioEnergy;
            this.arWidth = r.arWidth;
            this.arHeight = r.arHeight;
            this.arFPS = r.arFPS;
            this.arTime = r.arTime;

            this.arWifiNoise = r.arWifiNoise;
            this.arWifiLink = r.arWifiLink;
            this.arWifiLevel = r.arWifiLevel;

            this.arSimType = r.arSimType
            this.arSimRSSI = r.arSimRSSI
            this.arSimRSRP = r.arSimRSRP

            this.o_o_cpu = r.arCpu
            this.o_o_memory = r.arMemory
            this.o_o_memoryx = r.arMemoryx

            // this.dataOper = r.oper
            // console.log(r.oper)
            // console.log('dataOperVideo', r.jsonOperVideo)
            // console.log('jsonOperAudio', r.jsonOperAudio)
            // this.dataOperVideo = r.jsonOperVideo
            // this.dataOperAudio = r.jsonOperAudio
            //
            // this.dataEx = r.ex
            //
            // this.infoNameWiFi = r.infoNameWiFi
            // this.infoNoiseWiFi = r.infoNoiseWiFi
            // this.infoLinkWiFi = r.infoLinkWiFi
            // this.infoQualityVideo = r.infoQualityVideo
            // this.isWiFi = r.isWiFi
            //
            // this.mediumBitrateExp = r.mediumBitrateExp
            // this.mediumBandwidthrExp = r.mediumBandwidthrExp
            // this.mediumBitrateOper = r.mediumBitrateOper
            // this.mediumBandwidthrOper = r.mediumBandwidthrOper
            // this.mediumRtt = r.mediumRtt
            //
            // // this.normBitrate = r.normBitrate
            // // this.normBandwidthr = r.normBandwidthr
            //
            //
            // this.startDate = r.startDate
            // this.duration = r.duration
            // this.nameOper = r.nameOper
            // this.nameExpert = r.nameExpert
            // this.data5 = r.data5
            //
            // this.data6 = r.data6
            // this.data7 = r.data7
            // this.data8 = r.data8
            // this.data9 = r.data9
            // this.data10 = r.data10
            // this.data11 = r.data11
            //
            // this.sEx = r.sEx
            // this.sDv = r.sDv
            //
            // try{
            //   this.data7s = this.sEx.max_video_recv_band
            // }catch (e){
            //   this.data7s =1
            //     console.log('ошибка sEx.max_video_recv_band',e)
            // }
            //
            // try{
            //   this.data9s = this.sDv.max_video_recv_band
            // }catch (e){
            //   this.data9s = 1
            //   console.log('this.sDv.max_video_recv_band',e)
            // }
            //
            // try{
            //   this.data6s = this.sEx.max_output_bitrate
            //   console.log('data6s',this.data6s)
            // }catch (e){
            //   this.data6s = 1
            //     console.log('ошибка this.sEx.max_output_bitrate',e)
            // }
            //
            // try{
            //   this.data8s = this.sDv.max_output_bitrate
            // }catch (e){
            //   this.data8s = 1
            //   console.log('ошибка sDv.max_output_bitrate',e)
            // }
            //
            // this.data10s = 0
            //
            // try{
            //   this.data11s = this.sEx.fps
            //   console.log('data11s',this.data11s)
            // }catch (e){
            //   this.data11s = 1
            //   console.log('this.sEx.fps',e)
            // }
            //
            // this.dataParamChart = r
            //
            // this.ar6 = r.ar6
            // this.ar7 = r.ar7
            // this.ar8 = r.ar8
            // this.ar9 = r.ar9
            // this.ar10 = r.ar10
            // this.ar11 = r.ar11
            //
            // this.arRtt = r.arRtt
            // this.arRttVideo = r.arRttVideo
            // this.arRttAudio = r.arRttAudio
            // this.arPacketsLost = r.arPacketsLost
            //
            // console.log('arRtt',r.arRtt)
            // console.log('arRttVideo',r.arRttVideo)
            // console.log('arRttAudio',r.arRttAudio)
            // console.log('arPacketsLost',r.arPacketsLost)
            //
            // this.arTargetEncBitrate = r.arTargetEncBitrate
            // this.arActualEncBitrate = r.arActualEncBitrate
            // this.arBucketDelay = r.arBucketDelay
            //
            // console.log('arBucketDelay',r.arBucketDelay)
            // console.log('arActualEncBitrate',r.arActualEncBitrate)
            // console.log('arTargetEncBitrate',r.arTargetEncBitrate)
            //
            // this.arTime = r.time
            // var time = r.time
            // this.arTime = r.time
            //
            // this.o_o_framerate = r.o_o_framerate
            // this.o_o_link = r.o_o_link
            // this.o_o_noise = r.o_o_noise
            //
            // this.o_o_cpu = r.o_o_cpu
            // this.o_o_memory = r.o_o_memory
            // this.o_o_memoryx = r.o_o_memoryx
            //
            // this.e_o_framerate = r.e_o_framerate
            // this.e_o_oper_height = r.e_o_oper_height
            //
            // console.log('e_o_framerate',r.e_o_framerate)
            // console.log('e_o_oper_height',r.e_o_oper_height)
            //
            // this.sett_fps = []
            // this.sett_height = []
            //
            // if(r.ex){
            //   for (var i = 0; i < r.ex.length; i++) {
            //     // e_o_framerate[i] = r.ex[i].oper_framerate;
            //     // e_o_oper_height[i] = r.ex[i].oper_height;
            //     this.sett_fps[i] = this.sDv.fps
            //     this.sett_height[i] = this.sDv.height
            //   }
            // }

          }).then(() => {
            this.onResize(1)
            $(document).ready(() => {
              This.onResize(2)
            })
            $(window).resize(() => {
              This.onResize(3)
            })
          })
      },

      getOptionsGrafStats() {
        this.optionsGrafStats = {
          chart: {
            id: '1',
            height: this.heightChart,
            type: 'area',
            stacked: false,
            group: 'social',
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },
          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a', '#3fcb5c', '#FEB019', '#ccdeff'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "vertical",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [
            {
            index: 1,
            name: this.$t('gc.stats.graph.height'),
            type: 'area',
            categories: 'resOpesr',
            data: this.arHeight
            },
            {
              index: 2,
              seriesIndex: 2,
              name: this.$t('gc.stats.graph.frp'),
              type: 'line',
              data: this.arFPS
            }],
          stroke: {
            width: [1, 2, 2, 3, 3],
            curve: 'monotoneCubic',
            dashArray: [0, 0, 0, 2, 0]
          },
          title: {
            text: this.$t('expert.stats.graph.Video-quality-graph'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500
            },
          },
          annotations: {
            yaxis: [
          //     {
          //     y: 720,
          //     y2: 730,
          //     borderColor: '#309fdb',
          //     label: {
          //       borderColor: 'rgba(48,159,219,0)',
          //       position: 'left',
          //       offsetX: 90,
          //       style: {
          // fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
          //         color: 'rgba(255,255,255,0)',
          //         width: '12px',
          //         background: 'rgba(48,159,219,0)',
          //       },
          //       text: '',
          //     }
          //   },
              {
              y: 12,
              y2: 18,
              seriesName: this.$t('gc.stats.graph.frp'),
              yAxisIndex: 2,
              borderColor: '#cb2b2a',
              fillColor: '#FEB019',
              opacity: 0.2,
              label: {
                borderColor: '#cb2b2a',
                offsetX: -5,
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  fontSize: '10px',
                  color: '#111',
                  background: '#FEB019',
                },
                text: this.$t('expert.stats.graph.Norm-fps'),
              }
            }],
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              id: "resOpesr",
              seriesName: this.$t('expert.stats.graph.Oper-perm'),
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('expert.stats.graph.Resolution'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            },

            {
              min: 0,
              max: 30,
              seriesName: this.$t('expert.stats.graph.FPS-ex'),
              opposite: true,
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#fe0909'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                },
              },
              title: {
                text: this.$t('expert.stats.graph.fps'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                }
              }
            },
            {
              seriesName: this.$t('expert.stats.graph.FPS-ex'),
              show: false
            },
            {
              seriesName: this.$t('expert.stats.graph.FPS-ex'),
              show: false
            },
            {
              seriesName: this.$t('expert.stats.graph.Oper-perm'),
              show: false
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },
      getOptionsCpuStats() {
        this.optionsCpuStats = {
          chart: {
            id: '2',
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },
          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "vertical",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('expert.stats.graph.CPU-load'),
            type: 'area',
            data: this.o_o_cpu
          }, {
            name: this.$t('expert.stats.graph.Free-memory'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.o_o_memory
          }],
          stroke: {
            width: [1, 2, 66],
            curve: 'monotoneCubic'
          },
          // series: [{
          //   name: 'Загрузка CPU (%)',
          //   type: 'area',
          //   data: this.o_o_cpu
          // }, {
          //   name: 'Свободная память (%)',
          //   type: 'line',
          //   fill: {
          //     type: 'gradient',
          //     gradient: {
          //       shadeIntensity: 1,
          //       opacityFrom: 1,
          //       opacityTo: 1,
          //       stops: [100, 100, 100]
          //     }
          //   },
          //   data: this.o_o_memory
          // }, {
          //   name: 'Кэш',
          //   type: 'line',
          //   fill: {
          //     type: 'gradient',
          //     gradient: {
          //       shadeIntensity: 1,
          //       opacityFrom: 1,
          //       opacityTo: 1,
          //       stops: [100, 100, 100]
          //     }
          //   },
          //   data: this.o_o_memory
          // }],
          // stroke: {
          //   width: [1, 2, 2]
          // },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('expert.stats.graph.CPU-load-graph'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500,
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('expert.stats.graph.CPU-load'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            },

            {
              seriesName: this.$t('expert.stats.graph.Free-memory'),
              opposite: true,
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#fe0909'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                },
              },
              title: {
                text: this.$t('expert.stats.graph.Free-memory'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                }
              }
            },
            {
              seriesName: this.$t('expert.stats.graph.Free-memory'),
              show: false
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },
      getOptionsWiFiStats() {
        this.optionsWiFiStats = {
          chart: {
            id: '3',
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },
          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "vertical",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('expert.stats.graph.Network-speed'),
            type: 'area',
            data: this.arWifiLink
          }, {
            name: this.$t('expert.stats.graph.Wi-Fi-signal'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.arWifiLevel
          }],
          stroke: {
            width: [1, 2, 66],
            curve: 'monotoneCubic'
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('expert.stats.graph.Wi-Fi-quality-graph'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500,
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('expert.stats.graph.Network-speed'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            },

            {
              min: -100,
              max: 0,
              seriesName: 'Revenue',
              opposite: true,
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#fe0909'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                },
              },
              title: {
                text: this.$t('expert.stats.graph.Wi-Fi-signal'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                }
              }
            },
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },
      getOptionsNoWiFiStats() {
        this.optionsNoWiFiStats = {
          chart: {
            id: '3',
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },
          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['rgba(48,159,219,0.51)', 'rgba(203,94,90,0.5)'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "vertical",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [
            {
              name: this.$t('expert.stats.graph.Input-Signal-Rate'),
              type: 'area',
              data: this.arSimRSRP,
            },
            {
              name: this.$t('expert.stats.graph.Outgoing-Signal-Rate'),
              type: 'line',
              data: this.arSimRSSI
            },
            // {
            //   name: 'Скорость исходящего сигнала (Кбит/c)',
            //   type: 'area',
            //   data: this.o_o_noise
            // }
            ],
          stroke: {
            width: [3, 1],
            curve: 'monotoneCubic'
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('expert.stats.graph.Internet-speed-graph'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500,
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('expert.stats.graph.Signal-rate'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },
      getOptionsParStatsBitrateEncode() {
        this.optionsParStatsBitrateEncode = {
          chart: {
            id: 4,
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            group: 'social',
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },
          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "horizontal",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('gc.stats.graph.bit'),
            type: 'area',
            data: this.arBitrate
          }, {
            name: this.$t('gc.stats.graph.eуncode'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.arEncodeSpeed
          }],
          stroke: {
            curve: 'monotoneCubic',
            width: [3, 3, 66]
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('gc.stats.graph.bitEncode'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('gc.stats.graph.bit'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            },

            {
              seriesName: 'Revenue',
              opposite: true,
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#fe0909'
              },
              labels: {
                style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                },
              },
              title: {
                text: this.$t('gc.stats.graph.eуncode'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#fe0909',
                }
              }
            },
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },//канал битрейта
      getOptionsParStatsRTTJitterVideo() {
        this.optionsParStatsRTTJitterVideo = {
          chart: {
            id: 5,
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            group: 'social',
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },

          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a', '#3fcb5c', '#ffc033', '#ffc033'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "horizontal",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('gc.stats.graph.rttV'),
            type: 'line',
            data: this.arRttVideo
          }, {
            name: this.$t('gc.stats.graph.jitterV'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.arJitterVideo
          }
          ],
          stroke: {
            width: [3, 3],
            curve: 'monotoneCubic'
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('gc.stats.graph.rttJitterV'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('gc.stats.graph.rttV'),
                style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
          fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },// jitter rtt Video
      getOptionsParStatsRTTJitterAudio() {
        this.optionsParStatsRTTJitterAudio = {
          chart: {
            id: 5,
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            group: 'social',
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },

          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a', '#3fcb5c', '#ffc033', '#ffc033'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "horizontal",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('gc.stats.graph.rttA'),
            type: 'line',
            data: this.arRttAudio
          }, {
            name: this.$t('gc.stats.graph.jitterA'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.arJitterAudio
          }
          ],
          stroke: {
            width: [3, 3],
            curve: 'monotoneCubic'
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('gc.stats.graph.rttJitterA'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('gc.stats.graph.rttA'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
            fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },// jitter rtt Video
      getOptionsParStatsPacketLost() {
        this.optionsParStatsPacketLost = {
          chart: {
            id: 5,
            height: this.heightChart,
            type: 'area',
            group: 'social',
            stacked: false,
            group: 'social',
            toolbar: {
              show: false,
              zoomIn: false,
              zoomOut: false,
              reset: false,
              pan: false,
            },

          },
          dataLabels: {
            enabled: false
          },
          grid: {
            show: true,
            borderColor: 'rgba(0,0,0,0.07)',
            strokeDashArray: 0,
            position: 'front',
            yaxis: {
              lines: {
                show: true
              }
            },
            row: {
              colors: undefined,
              opacity: 0.5
            },
            column: {
              colors: undefined,
              opacity: 0.5
            },
          },
          colors: ['#309fdb', '#cb5e5a', '#3fcb5c', '#ffc033', '#ffc033'],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: "horizontal",
              shadeIntensity: 0.5,
              gradientToColors: undefined,
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 0, 50],
              colorStops: []
            }
          },
          series: [{
            name: this.$t('gc.stats.graph.rttA'),
            type: 'line',
            data: this.arPacketsLostVideo
          }, {
            name: this.$t('gc.stats.graph.jitterA'),
            type: 'line',
            fill: {
              type: 'gradient',
              gradient: {
                shadeIntensity: 1,
                opacityFrom: 1,
                opacityTo: 1,
                stops: [100, 100, 100]
              }
            },
            data: this.arPacketsLostAudio
          }
          ],
          stroke: {
            width: [3, 3],
            curve: 'monotoneCubic'
          },
          // stroke: {
          //   width: [5, 7, 5],
          //   curve: 'straight',
          //   dashArray: [0, 0, 5]
          // },
          title: {
            text: this.$t('gc.stats.graph.rttJitterA'),
            offsetX: 70,
            style: {
              fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              color: '#173b6a',
              fontSize: '16px',
              fontWeight: 500
            },
          },
          xaxis: {
            type: 'category',
            categories: this.arTime,
            labels: {
              show: false,
              rotate: -90,
              rotateAlways: false,
              hideOverlappingLabels: true,
              showDuplicates: false,
              trim: true,
              minHeight: undefined,
              maxHeight: 120,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                colors: [],
                fontSize: '10px',
                cssClass: 'apexcharts-xaxis-label',
              },
              offsetX: 0,
              offsetY: 0,
              format: undefined,
              formatter: undefined,
              datetimeFormatter: {
                year: 'yyyy',
                month: "MMM 'yy",
                day: 'dd MMM',
                hour: 'HH:mm',
              },
            },
            axisBorder: {
              show: true,
              color: '#78909C',
              height: 1,
              width: '100%',
              offsetX: 0,
              offsetY: 0
            },
            axisTicks: {
              show: true,
              borderType: 'solid',
              color: '#78909C',
              height: 6,
              offsetX: 0,
              offsetY: 0
            },
            tickAmount: undefined,
            tickPlacement: 'between',
            min: undefined,
            max: undefined,
            range: undefined,
            floating: false,
            position: 'bottom',
            title: {
              text: undefined,
              offsetX: 0,
              offsetY: 0,
              style: {
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                color: undefined,
                fontSize: '12px',
                cssClass: 'apexcharts-xaxis-title',
              },
            },
            crosshairs: {
              show: true,
              width: 1,
              position: 'back',
              opacity: 0.9,
              stroke: {
                color: '#b6b6b6',
                width: 0,
                dashArray: 0,
              },
              fill: {
                type: 'solid',
                color: '#B1B9C4',
                gradient: {
                  colorFrom: '#D8E3F0',
                  colorTo: '#BED1E6',
                  stops: [0, 100],
                  opacityFrom: 0.4,
                  opacityTo: 0.5,
                },
              },
              dropShadow: {
                enabled: false,
                top: 0,
                left: 0,
                blur: 1,
                opacity: 0.4,
              },
            },
            tooltip: {
              enabled: true,
              formatter: undefined,
              offsetY: 0,
              style: {
                fontSize: 0,
                fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
              },
            },
          },
          markers: {
            size: 0
          },
          yaxis: [
            {
              axisTicks: {
                show: true,
              },
              axisBorder: {
                show: true,
                color: '#309fdb'
              },
              labels: {
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              title: {
                text: this.$t('gc.stats.graph.rttA'),
                style: {
                  fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
                  color: '#309fdb',
                }
              },
              tooltip: {
                enabled: true
              }
            }
          ],
          tooltip: {
            fixed: {
              enabled: true,
              position: 'topLeft', // topRight, topLeft, bottomRight, bottomLeft
              offsetY: 30,
              offsetX: 60
            },
          },
          legend: {
            fontFamily: 'Rooftop-Regular, Helvetica, Arial, sans-serif',
            horizontalAlign: 'left',
            offsetX: 40
          }
        }
      },


      renderChart(type) {
        var time = this.dataParamChart.time

        if (type == 1) {
          console.log("tender 1")
          try {
            this.chartCountData.destroy()
          } catch (e) {
          }
          this.getOptionsParStatsBitrateEncode()

          this.chartCountData = null
          this.chartCountData = new ApexCharts(document.querySelector("#chart-line4"), this.optionsParStatsBitrateEncode);
          this.chartCountData.render();
        } else if (type == 2) {
          console.log("tender 2")
          try {
            this.chartCountData.destroy()
          } catch (e) {
          }
          this.getOptionsParStatsRTTJitterVideo()
          this.chartCountData = null
          this.chartCountData = new ApexCharts(document.querySelector("#chart-line4"), this.optionsParStatsRTTJitterVideo);
          this.chartCountData.render();
        } else if (type == 3) {
          console.log("tender 3")
          try {
            this.chartCountData.destroy()
          } catch (e) {
          }
          this.getOptionsParStatsRTTJitterAudio()
          this.chartCountData = null
          this.chartCountData = new ApexCharts(document.querySelector("#chart-line4"), this.optionsParStatsRTTJitterAudio);
          this.chartCountData.render();
        } else if (type == 4) {
          console.log("tender 4")
          try {
            this.chartCountData.destroy()
          } catch (e) {
          }
          this.getOptionsParStatsPacketLost()
          this.chartCountData = null
          this.chartCountData = new ApexCharts(document.querySelector("#chart-line4"), this.optionsParStatsPacketLost);
          this.chartCountData.render();
        } else if (type == 5) {

        } else if (type == 6) {

        }
      },


      closeDialogDetail() {
        this.dialogDetail = false
      },

      addOptions() {
        this.tdd = []
        console.log('webcall/listOper 2')
        return this.$root.restGet('webcall/listOper')
          .then(r => {
            this.tdd = r
          })
      },

      updateTableStats() {
        this.addOptions().then(() => this.tabOptDetail = {
          height: this.heightChart,
          data: this.tabDataDetail,
          paginationSize: 25,
          columnMinWidth: 85,
          sortOrderReverse: true,
          columns: [
            {
              title: 'Время',
              field: 'time',
              headerSort: false
            },
            {
              title: 'Разрешение<br>Эксперта',
              field: 'ex_height',
              headerSort: false
            },
            {
              title: 'Битрейт<br>эксперта<br>(Кбит/с) ',
              field: 'ex_bitrate',
              headerSort: false
            },
            {
              title: 'Канал<br>Эксперта<br>(Кбит/с)',
              field: 'bandwidth',
              headerSort: false
            },
            {
              title: 'FPS<br>эксперта',
              field: 'ex_framerate',
              headerSort: false
            },
            {

              title: 'Разрешение<br>Оператора',
              field: 'oper_height',
              headerSort: false
            },
            {
              title: 'Битрейт<br>оператора<br>(Кбит/с)',
              field: 'oper_bitrate',
              headerSort: false
            },
            {
              title: 'Канал<br>Оператора<br>(Кбит/с)',
              field: 'oper_bandwidth',
              headerSort: false
            },
            {
              title: 'FPS<br>оператора ',
              field: 'oper_framerate',
              headerSort: false
            },
            {

              title: 'Видео<br>кодек',
              field: 'video',
              headerSort: false
            },
            {
              title: 'Локальный<br>кандидат',
              field: 'localAddress',
              headerSort: false,
              minWidth: 120
            },
            {
              title: 'Удаленный<br>кандидат',
              field: 'remoteAddress',
              headerSort: false,
              minWidth: 120
            },
            {
              title: 'XR (%)',
              field: 'CPUXR',
              headerSort: false
            },
            {
              title: ' Сум. исп. CPU (%)',
              field: 'CPUTotalP',
              headerSort: false
            },
            {
              title: 'Доступ. память (Байт) ',
              field: 'MemUsed',
              headerSort: false
            },
            {
              title: 'Исп. память (Байт)',
              field: 'MemAvailable',
              headerSort: false
            },
            {
              title: 'Кэш (Байт)',
              field: 'Cached',
              headerSort: false
            },
            {
              title: 'Память XR (Байт) ',
              field: 'MemoryXR',
              headerSort: false
            },
            {
              title: 'Порог (Байт)',
              field: 'Threshold',
              headerSort: false
            },
            {
              title: 'Своб. память (Байт) ',
              field: 'MemFree',
              headerSort: false
            },
            {
              title: 'Имя WIFI',
              field: 'nameWiFi',
              headerSort: false
            },
            {
              title: 'Сигнал WIFI<br>(дБм)',
              field: 'level',
              headerSort: false
            },
            {
              title: 'Скорость связи WIFI<br>(Мбит/с) ',
              field: 'link',
              headerSort: false
            },
          ],
        }).then(() => setTimeout(() => this.$refs.tabulator.draw(), 0))
      },

      onResize(t) {

        console.log('onResize ' + t)

        this.heightChart = window.outerHeight * 0.4
        $('#divRowStatsCall1').height(This.heightChart)
        $('#divRowStatsCall2').height(This.heightChart)
        this.heightChart = window.outerHeight * 0.3
        console.log('heightChart '+this.heightChart)


        this.getOptionsGrafStats()
        this.getOptionsCpuStats()
        this.getOptionsWiFiStats()
        this.getOptionsNoWiFiStats()
        this.getOptionsParStatsBitrateEncode()
        this.getOptionsParStatsRTTJitterVideo()
        this.getOptionsParStatsRTTJitterAudio()
        this.getOptionsParStatsPacketLost()

        try {
          this.chartStats.destroy();
          this.chartLineCPU.destroy();
          this.chartAreaWiFi.destroy();
          this.chartCountData.destroy();
        } catch (e) {

        }

        this.chartStats = null;
        this.chartLineCPU = null;
        this.chartAreaWiFi = null;
        this.chartCountData = null;

        this.chartStats = new ApexCharts(document.querySelector("#chart-line"), this.optionsGrafStats);
        this.chartLineCPU = new ApexCharts(document.querySelector("#chart-line2"), this.optionsCpuStats);
        if (this.isWiFi) {
          this.chartAreaWiFi = new ApexCharts(document.querySelector("#chart-line3"), this.optionsWiFiStats);
        } else {
          this.chartAreaWiFi = new ApexCharts(document.querySelector("#chart-line3"), this.optionsNoWiFiStats);
        }
        this.chartCountData = new ApexCharts(document.querySelector("#chart-line4"), this.optionsParStatsBitrateEncode);

        this.chartStats.render();
        this.chartLineCPU.render();
        this.chartAreaWiFi.render();
        this.chartCountData.render();

        $(".fullHeight").each((index, value) => {
          console.log(index)
          console.log(value)
        })
      }
    }
  }
</script>

<style>

  .vinfocall {
    color: #173b6a;
    font-size: 16px;
    padding-left: 25px;
  }

  .textinfocallname {
    flex-grow: 0;
    font-size: 14px;
    min-width: 210px;
  }

  .textinfocall {
    color: #1b1b1b;
    font-size: 14px;
  }

  .t-Report-cell-xr_sett-hed {
    padding: 5px;
    font-size: 12px;
  }

  .t-Report-cell-xr_sett {
    border: 1px solid rgb(233, 238, 242);
    border-right-width: 0;
    padding: 5px;
    font-size: 12px;
  }

  .t-Report-cell-xr_sett-h {
    border-bottom: 1px solid rgb(233, 238, 242);
    /*border-right-width: 0;*/
    padding: 5px;
    font-size: 13px;
  }

  .t-Report-cell-xr_sett-2 {
    border-bottom: 1px solid rgb(233, 238, 242);
    border-right-width: 0;
    padding: 5px;
    font-weight: bold;
    font-size: 13px;
    color: #424242;
  }

  .btn_stats_chars {
    height: 30px;
    font-weight: 700;
    font-size: 12px;
    border: solid 1px #02020236;
    padding: 5px;
    padding-right: 8px;
    padding-left: 8px;
  }

  .btn_stats_chars {
    height: 30px;
    font-weight: 700;
    font-size: 12px;
    border: solid 1px #02020236;
    padding: 5px;
    padding-right: 8px;
    padding-left: 8px;
  }

  .tabs_border {
    border: 1px solid rgb(201, 201, 201);
    border-radius: 5px;
  }


  .wr {
    display: flex;
    flex-wrap: wrap;
    justify-content: spase-between;
    width: 100%;
    margin: 0 auto;
  }

  .item {
    width: 100%;
    max-width: 400px;
    height: auto;
  }

  .item_block {
    display: block;
    width: 100%;
    height: 100%;
  }

  .item_block_mu {
    position: relative;
  }
</style>
