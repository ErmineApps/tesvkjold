<!--<template>-->
<!--    <XrTabulator-->
<!--      ref="tabulator"-->
<!--      :tabulatorOptions="tabulatorOptions"-->
<!--      :tableData="tableData"-->
<!--      :showId="false"-->
<!--      :table-search="true"-->
<!--      class="fullHeight"-->

<!--      name="expertCallTable"-->
<!--      :tableToolbar=true-->
<!--      :showIdWithFilter="false"-->
<!--      :isShowSpacerRefresh="true"-->
<!--      :isShowSpinner="true"-->
<!--      :tableSearch="true"-->

<!--      :serverSFP="true"-->
<!--      :serverInitSort="[{column: 'id', dir: 'desc'}]"-->
<!--      :showSets="true"-->
<!--      :showSetResize="false"-->
<!--      :serverPageSize="30"-->
<!--    >-->
<!--    </XrTabulator>-->
<!--</template>-->

<!--<script>-->
<!--  export default {-->
<!--    name: "TableStatisticDetail",-->

<!--    props: ['data', 'ex'],-->

<!--    mounted() {-->
<!--      this.init(this.data)-->
<!--    },-->

<!--    data() {-->
<!--      return {-->
<!--        tabulatorOptions: null, //variable to hold your table-->
<!--        tableData: [], //data for table to display-->
<!--      }-->
<!--    },-->
<!--    methods: {-->
<!--      init(tabData) {-->
<!--        this.fetchDutyExpert(tabData)-->
<!--          .then((data) => this.tabulatorOptions = this.getTableOptions())-->
<!--          // Отрисовка таблицы-->
<!--          .then(() => this.$refs.tabulator.draw());-->
<!--      },-->

<!--      async fetchDutyExpert(tabData) {-->
<!--        this.tableData = tabData;-->
<!--        return tabData-->
<!--      },-->

<!--      // Возвращает объект options для создания объекта Tabulator-->
<!--      getTableOptions() {-->
<!--        return {-->
<!--          data: this.tableData,-->
<!--          minHeight: 250,-->
<!--          // height: '350px',-->
<!--          columnMinWidth: 40,-->
<!--          resizableColumns: true,-->
<!--          movableColumns: true,-->
<!--          columnHeaderVertAlign: "top",-->
<!--          paginationSize: 25,-->

<!--          initialSort: [-->
<!--            {column: 'time', dir: 'asc'}-->
<!--          ],-->

<!--          columns: [-->
<!--            {-->
<!--              title: this.$t('time'),-->
<!--              field: 'time',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.stats.table.resolution-ex'),-->
<!--              field: 'ex_height',-->
<!--               headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.info.Expert-bitrate'),-->
<!--              field: 'ex_bitrate',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.info.Expert-channel'),-->
<!--              field: 'bandwidth',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.stats.table.FPS-ex'),-->
<!--              field: 'ex_framerate',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->

<!--              title: this.$t('expert.stats.table.resolution-oper'),-->
<!--              field: 'oper_height',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.info.Operator-bitrate'),-->
<!--              field: 'oper_bitrate',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.info.Operator-channel'),-->
<!--              field: 'oper_bandwidth',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.stats.table.FPS-ex'),-->
<!--              field: 'oper_framerate',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--            },-->
<!--            {-->

<!--              title: this.$t('expert.video-codec'),-->
<!--              field: 'video',-->
<!--              headerSort: true,headerFilter: true, headerFilterPlaceholder: '...',-->

<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-candidate'),-->
<!--              field: 'localAddress',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              minWidth: 120-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Remote'),-->
<!--              field: 'remoteAddress',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              minWidth: 120,-->
<!--              visible: this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-XR'),-->
<!--              field: 'CPUXR',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: false,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Total'),-->
<!--              field: 'CPUTotalP',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: false-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Access'),-->
<!--              field: 'MemUsed',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Use'),-->
<!--              field: 'MemAvailable',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Cache'),-->
<!--              field: 'Cached',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-XR-Memory'),-->
<!--              field: 'MemoryXR',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Threshold'),-->
<!--              field: 'Threshold',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-Free'),-->
<!--              field: 'MemFree',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-WIFI-name'),-->
<!--              field: 'nameWiFi',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-WIFI-signal'),-->
<!--              field: 'level',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: this.$t('expert.Local-WIFI-communication'),-->
<!--              field: 'link',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Тип сети GSM',-->
<!--              field: 'gsmnetworkType',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Оператор сети',-->
<!--              field: 'gsmOperator',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Cell ID',-->
<!--              field: 'gsmCID',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'PCI',-->
<!--              field: 'gsmRCID',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Уровень сигнала (дБм)',-->
<!--              field: 'gsmsignalGsmStrengthdBm',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Cell локация',-->
<!--              field: 'gsmLac',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Номер радиочастотного канала GSM',-->
<!--              field: 'gmsArfcn',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->
<!--            {-->
<!--              title: 'Gsm Rssi',-->
<!--              field: 'gsmsignalGsmRssi',-->
<!--              headerSort: true,-->
<!--              headerFilter: true, headerFilterPlaceholder: '...',-->
<!--              visible: !this.ex,-->
<!--            },-->

<!--          ],-->
<!--        }-->
<!--      },-->

<!--      redraw() {-->
<!--        this.$refs.tabulator.redraw()-->
<!--      },-->
<!--    }-->
<!--  }-->
<!--</script>-->

<!--<style scoped>-->

<!--</style>-->
