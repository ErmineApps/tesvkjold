<template>
    <XrTabulator
      ref="tabulator"
      :tabulatorOptions="tabulatorOptions"
      :tableData="tableData"
      :tableSearch="true"
      searchClass="xs5"
      name="telCallOper"
      :showId="false"
      @onrefreshclick="onRefresh"
      class="pt-2 fullHeight"
      :showSets="true"
      @oninit="refreshFullTable"
    >
    </XrTabulator>
  </template>

  <script>
  var  This

  export default {
    name: "TableAddOperLink",
    data() {
      return {
        tabulatorOptions: null, //variable to hold your table
        tableData: [], //data for table to display

        selected: 7,
        tab: null,
        items: [
          'web', 'shopping'
        ],
        text: '',
        activeTab: 0,
        ticketId: null,
        ticket: null,
        connected: null,
        connectMsg: null,
        grCall: null,

        groupItem: [],
        grouplValue: null,
      }
    },


    methods: {
      init(groupItem, grouplValue, grCall) {
        This = this

        this.groupItem = groupItem
        // console.log(groupItem)
        // console.log(this.groupItem[0].id+" "+this.groupItem[0].name)
        this.grouplValue = groupItem[0].id
        // console.log(this.grouplValue)
        this.grCall = groupItem[0].id

        console.log('this.grCall  '+this.grCall)

        this.refreshFullTable()
        // .then((data) => this.tabulatorOptions = this.getTableOptions())
        // // Отрисовка таблицы
        // .then(() => this.$refs.tabulator.draw());
      },

      async fetchDutyExpert() {
        let uri = 'webcall/listOper'
        this.tableData = await this.$root.restGet(uri)
      },

      // Возвращает объект options для создания объекта Tabulator
      getTableOptions() {
        return {
          fullHeight: true,
          data: this.tableData,
          // paginationSize: 25,
          pagination: false,
          columnMinWidth: 29,
          resizableColumns: true,
          movableColumns: true,
          sortOrderReverse: true,
          tableSearch: true,
          // height:350,
          initialSort: [{column: 'partDisconn', dir: 'desk'}, {column: "name_oper", dir: "asc"}],
          columns: [
            {
              title: this.$t('operator'),
              field: 'nameOper',
              headerSort: true,
              sortable: true,
              minWidth: 150,
            }
            , {
              title: this.$tc('dev', 0),
              field: 'name',
              headerSort: true,
              sortable: true,
              minWidth: 150,
            },
            // {
            //   title: 'Онлайн',
            //   hozAlign: 'center',
            //   field: 'online',
            //   headerSort: true,
            //   sortable: true,
            //   width: 40,
            //   formatter: (cell) => {
            //     if (cell.getRow().getData().online) {
            //       return '<i class="fa fa-check"></i>'
            //     }
            //   },
            // },
            {
              title: this.$t('action'),
              hozAlign: 'center',
              field: 'partDisconn',
              headerSort: false,
              headerFilter: false,
              sortable: false,
              formatter: this.stepsFormatter,
              cellClick: this.callOper,
              width: 200,
            }
          ],
        }
      },

      // Возвращает элемент для ссылки на страницу шагов процесса
      stepsFormatter(cell, formatterParams) {
        if (cell.getRow().getData().free) {
          let _title1 = this.$t('gc.link-go') // Перейти на ссылку
          let _title2 = this.$t('gc.connect-goGC') // Подключить
          return `
        <div>
        <button type="button" class="v-btn v-btn_callex v-btn--router v-size--small theme--dark v-btn--tile v-btn--depressed v-btn--rounded mybrnect"
                            title="${_title1}"
                            style="">
<!--                            <span  style="margin-left: 5px; font-size: 14px" class="mdi mdi-24px mdi-account-group v-btn_ot_ex_sp v-span-video"  aria-hidden="true"></span>-->
                            <span class="v-btn_callex" style="padding-left: 5px; padding-right: 5px">${_title2}</span>
                         </button>
         </div>
         `

        } else {
          return `<p style=" margin-top: 13px;">${this.$t('expert.no-oper')}</p>`
        }
      },

      callOper(e, cell) {
        if(cell.getRow().getData().free){
          this.$emit('emitAddUser',cell.getRow().getData().id, cell.getRow().getData().nameOper)
        }
      },
      // ondblclick(id) {
      //   this.$emit('ondblclick', id)
      // },

      redraw() {
        try{
          this.$refs.tabulator.redraw()
        }catch (e) {
        }

      },

      selectShapeGR(shapeValue) {
        console.log(shapeValue)
        this.grCall = shapeValue
        this.$emit('selectShapeGR', shapeValue)
      },

      onRefresh(data) {
        this.fetchDutyExpert().then(data.callback)
          .then(() => {
            this.$root.showSucc(this.$t('confirm.refresh'))
          })
      },
      refreshFullTable(data) {
        this.fetchDutyExpert()
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

  <style scoped>

  </style>
