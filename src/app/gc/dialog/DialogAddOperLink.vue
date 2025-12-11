<template>
    <XrDialog ref="callOperDlg"
              :title="$t('gc.dialog.addUsertitle')"
              :width="800"
              :height="500"
              :resize="true"
              :fullscreen="false"
              @onresizestop="onResize"
    >
      <TableAddOperLink ref="callOperTable" @emitAddUser="emitAddUser" @emitCallGroup="emitCallGroup" @selectShapeGR="selectShapeGR"/>
    </XrDialog>
  </template>

  <script>

  import TableAddOperLink from "@/app/gc/dialog/TableAddOperLink";

  export default {
    name: "DialogAddOperLink",
    components: {TableAddOperLink},

    data() {
      return {
        title: this.$t('expert.dialog.call-oper.title'),
        valid: false,
        displayed: false,

        groupItem: [],
        grouplValue: null,
        grCall: null,
      }
    },
    created() {
      //document.title = this.title;
    },
    mounted() {
    },

    methods: {
      show(groupItem) {
        console.log('show groupItem')
        console.log(groupItem)

        this.$refs.callOperDlg.open()

        this.groupItem = groupItem
        this.grouplValue = groupItem[0]
        if(this.grouplValue == undefined){
          this.$root.showInfo(this.$t('expert.dialog.call-oper.mess'))
        }
        this.grCall = this.grouplValue.value



        setTimeout(() => this.$refs.callOperTable.init(this.groupItem, this.grouplValue, this.grCall))
      },

      emitAddUser(id, nameUser){
        this.$emit('emitAddUser', id, nameUser)
        this.onClear()
      },

      emitCallGroup(dvId, nameOper) {
        console.log(nameOper)
        this.$emit('emitCallGroupMain', dvId, nameOper)
        this.onClear()
      },

      /** выбор группы от которой будет звонок*/
      selectShapeGR(shapeValue){
        this.grCall = shapeValue
      },

      onClear(){
        this.$refs.callOperDlg.close()
      },

      onResize() {
        this.$refs.callOperTable.redraw()
      },

    }
  }
  </script>

  <style scoped>

  </style>

