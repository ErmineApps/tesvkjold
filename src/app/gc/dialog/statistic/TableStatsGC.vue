<!--suppress ALL -->
<template>
  <XrTabulator
    ref="tabulator"
    :is-interactive="rw"
    :isActiveRow="false"
    :isShowAddBtn="false"
    :ouIndex="5"
    :serverFetch="fetchGroup"
    :serverInitSort="[{column: 'name', dir: 'asc'}]"
    :serverPageSize="30" :serverSFP="true"
    :showSets="true"
    :table-search="true"
    name="expertGroups"

    v-bind:tableData="tableData"
    v-bind:tabulatorOptions="tabulatorOptions"
    @ondblclick="ondblclick"
    @oninit="refreshFullTable"
    @onrefreshclick="onRefresh"
    @onsave="saveAll"
  />
</template>

<script>
import expertRU from '@/lang/expert-ru.json'
import expertEN from '@/lang/expert-en.json'

let This

export default {

  props: ['data', 'ex', 'isMedia', 'isVideo'],

  data() {
    return {
      rw: true,
      tabulatorOptions: null,
      tableData: [],
      id: null,
    }
  },

  created() {
    this.$root.setLocaleModule(expertRU, expertEN)
    This = this
  },

  mounted() {
    this.init(this.data)
    this.tabulatorOptions = this.getTableOptions()
    this.$nextTick(() => this.refreshFullTable())
  },

  methods: {
    init(tabData) {
      this.fetchDutyExpert(tabData)
        .then((data) => this.tabulatorOptions = this.getTableOptions())
        // Отрисовка таблицы
        .then(() => this.$refs.tabulator.draw());
    },

    async fetchDutyExpert(tabData) {
      this.tableData = tabData;
      return tabData
    },

    // Сохраняет данные табулятора на сервере
    async saveAll(data) {
      let _lang = this.$i18n.locale
      const uri = '/expert/grid/' + _lang
      let res = await this.$root.restPost(uri, data)
      this.$root.showSucc(this.$t('expert.group.succ.save')) // Группы экспертов успешно сохранены
      //await this.fetchGroup(this.$refs.tabulator.getOptionsSFP())
      this.$refs.tabulator.afterSavedGrid(res)
    },

    fetchGroup(conditions) {
      if (typeof this.$refs.tabulator !== 'undefined') {
        this.$refs.tabulator.showSpinner()
      }
      let pars = {
        conditions: conditions
      }
      let opt = {
        headers: {
          'Content-Type': 'application/json',
          'charset': 'UTF-8'
        }
      }
      // return this.$root.restPost('/expert/table', pars, opt)
      //   .then((res) => {
      //     this.tableData = res
      //   })
      //   .catch(() => {
      //     this.$refs.tabulator.hideSpinner()
      //   })
    },

    async ondblclick(id) {
      this.$emit('ondblclick', id)
    },

    tableUpdate() {
      this.fetchGroup(this.$refs.tabulator.getOptionsSFP())
    },

    // Возвращает объект options для создания объекта Tabulator
    getTableOptions() {
      return {
        //data: this.tableData,
        dataTree: false,
        // pagination: false,
        fullHeight: true,

        columnMinWidth: 40,
        resizableColumns: true,
        movableColumns: true,
        columnHeaderVertAlign: "top",

        initialSort: [
          {column: 'time', dir: 'asc'}
        ],

        columns: [
          {
            title: this.$t('time'),
            field: 'time',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
          },

          {
            title: this.$t('expert.stats.table.resolution-ex'),
            field: 'ex_height',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia,
          },
          {
            title: this.$t('expert.info.Expert-bitrate'),
            field: 'ex_bitrate',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia && this.ex,
          },
          {
            title: this.$t('expert.info.Expert-channel'),
            field: 'bandwidth',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.stats.table.FPS-ex'),
            field: 'ex_framerate',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.stats.table.resolution-oper'),
            field: 'oper_height',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.info.Operator-bitrate'),
            field: 'oper_bitrate',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.info.Operator-channel'),
            field: 'oper_bandwidth',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.stats.table.FPS-ex'),
            field: 'oper_framerate',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.video-codec'),
            field: 'video',
            headerSort: true, headerFilter: true,
            headerFilterPlaceholder: '...',
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.Local-candidate'),
            field: 'localAddress',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            minWidth: 120,
            visible: !this.isMedia
          },
          {
            title: this.$t('expert.Local-Remote'),
            field: 'remoteAddress',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            minWidth: 120,
            visible: !this.isMedia && this.ex,
          },
          {
            title: this.$t('expert.Local-XR'),
            field: 'CPUXR',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: false,
          },
          {
            title: 'packetsLost',
            field: 'oper_packetsLost',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'googAvgEncodeMs',
            field: 'oper_googAvgEncodeMs',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia &&!this.ex,
          },
          {
            title: 'googRtt',
            field: 'oper_googRtt',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia &&!this.ex,
          },
          {
            title: 'googNacksReceived',
            field: 'oper_googNacksReceived',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-Total'),
            field: 'CPUTotalP',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: false
          },
          {
            title: this.$t('expert.Local-Access'),
            field: 'MemUsed',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia &&!this.ex,
          },
          {
            title: this.$t('expert.Local-Use'),
            field: 'MemAvailable',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia &&!this.ex,
          },
          {
            title: this.$t('expert.Local-Cache'),
            field: 'Cached',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-XR-Memory'),
            field: 'MemoryXR',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-Threshold'),
            field: 'Threshold',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-Free'),
            field: 'MemFree',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-WIFI-name'),
            field: 'nameWiFi',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-WIFI-signal'),
            field: 'level',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: this.$t('expert.Local-WIFI-communication'),
            field: 'link',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Тип сети GSM',
            field: 'gsmnetworkType',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Оператор сети',
            field: 'gsmOperator',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Cell ID',
            field: 'gsmCID',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia &&!this.ex,
          },
          {
            title: 'PCI',
            field: 'gsmRCID',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Уровень сигнала (дБм)',
            field: 'gsmsignalGsmStrengthdBm',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Cell локация',
            field: 'gsmLac',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible: !this.isMedia &&!this.ex,
          },
          {
            title: 'Номер радиочастотного канала GSM',
            field: 'gmsArfcn',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: 'Gsm Rssi',
            field: 'gsmsignalGsmRssi',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:!this.isMedia && !this.ex,
          },
          {
            title: '<div title="">googRtt</div>',
            field: 'googRtt',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">bytesSent</div>',
            field: 'bytesSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">framesEncoded</div>',
            field: 'framesEncoded',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">packetsLost</div>',
            field: 'packetsLost',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">qpSum</div>',
            field: 'qpSum',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googAdaptationChanges</div>',
            field: 'googAdaptationChanges',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googAvgEncodeMs</div>',
            field: 'googAvgEncodeMs',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googBandwidthLimitedResolution</div>',
            field: 'googBandwidthLimitedResolution',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googCodecName</div>',
            field: 'googCodecName',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googContentType</div>',
            field: 'googContentType',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googCpuLimitedResolution</div>',
            field: 'googCpuLimitedResolution',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googEncodeUsagePercent</div>',
            field: 'googEncodeUsagePercent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFirsReceived</div>',
            field: 'googFirsReceived',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameHeightInput</div>',
            field: 'googFrameHeightInput',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameHeightSent</div>',
            field: 'googFrameHeightSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameRateInput</div>',
            field: 'googFrameRateInput',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameRateSent</div>',
            field: 'googFrameRateSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameWidthInput</div>',
            field: 'googFrameWidthInput',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googFrameWidthSent</div>',
            field: 'googFrameWidthSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googHasEnteredLowResolution</div>',
            field: 'googHasEnteredLowResolution',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">hugeFramesSent</div>',
            field: 'hugeFramesSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },
          {
            title: '<div title="">googPlisReceived</div>',
            field: 'googPlisReceived',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && this.isVideo,
          },

          {
            title: '<div title="">bytesSent</div>',
            field: 'bytesSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">audioInputLevel</div>',
            field: 'audioInputLevel',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">packetsLost</div>',
            field: 'packetsLost',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googRtt</div>',
            field: 'googRtt',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googRttCandidate</div>',
            field: 'googRttCandidate',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">packetsSent</div>',
            field: 'packetsSent',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">totalAudioEnergy</div>',
            field: 'totalAudioEnergy',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">totalSamplesDuration</div>',
            field: 'totalSamplesDuration',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googJitterReceived</div>',
            field: 'googJitterReceived',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googResidualEchoLikelihood</div>',
            field: 'googResidualEchoLikelihood',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googResidualEchoLikelihoodRecentMax</div>',
            field: 'googResidualEchoLikelihoodRecentMax',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          },
          {
            title: '<div title="">googTypingNoiseState</div>',
            field: 'googTypingNoiseState',
            headerSort: true,
            headerFilter: true, headerFilterPlaceholder: '...',
            visible:this.isMedia && !this.isVideo,
          }

        ],
      }
    },

    onRefresh(data) {
      this.fetchGroup(this.$refs.tabulator.getOptionsSFP())
        .then(data.callback)
        .then(() => {
          this.$root.showSucc(this.$t('confirm.refresh'))
        })
    },
    refreshFullTable(data) {
      let notFilter = data ? data.isResetContext : null
      this.fetchGroup(this.$refs.tabulator.getOptionsSFP(notFilter))
        .then(() => this.tabulatorOptions = this.getTableOptions())
        .then(() => this.$refs.tabulator.draw())
        .then(() => {
          if (data && data.callback) {
            setTimeout(() => data.callback())
          }
        })
    },

  }
}
</script>

