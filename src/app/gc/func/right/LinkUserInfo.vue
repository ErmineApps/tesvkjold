<template>
  <div id="divMainLinkUser" class="divMainLink" style="display: none">
    <p class="pcahtmessagTitle">{{ this.$t('gc.userLink') }}</p>
    <v-btn class="btncloseChat" :title="$t('close')" @click="clickCloseChat">
      <span class="fa-layers fa-fw">
        <i class="fas fa-times" style="font-size: 12px; margin-right: 3px; margin-bottom: 4px;"></i>
      </span>
    </v-btn>

    <v-col class="vcolLinkUser">

      <v-row dense>
        <v-col>
          <!-- Подключить оператора -->
          <XrBtn
            v-if="!isExternalUser"
            :class="'btnAddNewOper'"
            type="hot"
            icon="mdi-account-hard-hat-outline"
            @click="addNewOper"
            :text="$t('gc.addOper')"
            :title="$t('gc.addOperTitle')"
          ></XrBtn>
        </v-col>
      </v-row>

      <div class="divBlockListInfoUser fullHeight">
        <!-------------------------------->
        <!-- Пользователи онлайн -->
        <v-expansion-panels v-model="panel1">
          <v-expansion-panel class="gc-expansion-panel">
            <v-expansion-panel-header>{{ $t('gc.user-online') +' '+numUsers }}
              {{ panel1 == null ? ('(' + itemsUsersInfoCount + ')') : '' }}
            </v-expansion-panel-header>
            <v-expansion-panel-content class="gc-log-info-panel">
              <!-------------------------------->
              <v-list color="transparent">
                <v-list-item
                  v-for="(item, i) in itemsUsersInfo"
                  :key="i"
                  disabled
                  class="info-item-users"
                >
                  <v-list-item-icon class="infoUserLinsIkon">
                    <img v-if="!item.isscrS" :src="item.avatar" class="infoUserLinsIkonImg">
                    <v-icon :color=item.color v-else v-text="item.icon" class="iconInfoUserOS"></v-icon>

                    <div v-if="item.isOper" class="infoUserOperImgGC">
                      <v-icon
                        size="14"
                        style="stroke: green; stroke-width: 10px"
                        color="green"
                      >fas fa-mobile-alt</v-icon>
                    </div>

<!--                    <div style="position: absolute; top: 20px; left: 21px">-->
<!--                      <v-icon-->
<!--                        size="20"-->
<!--                        style="stroke: green; stroke-width: 10px"-->
<!--                        color="green"-->
<!--                      >mdi-hard-hat</v-icon>-->
<!--                    </div>-->

<!--                    <div style="position: absolute; top: 25px; left: 23.5px">-->
<!--                      <v-icon-->
<!--                        size="15"-->
<!--                        style="stroke: black; stroke-width: 10px"-->
<!--                        color="black"-->
<!--                      >mdi-glasses</v-icon>-->
<!--                    </div>-->

                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title v-text="item.senderName" style="color: black"
                                       :color=item.colorText></v-list-item-title>
                  </v-list-item-content>
                  <v-list-item-action v-if="!item.isMy && !isExternalUser">
                    <v-btn color="white" fab
                           class="mr-1 btnInfoLinkUser"
                           v-if="!item.isscrS"
                           :title="$t('gc.isMicUser.dialog.mess')"
                           @click="clickMic(item)">
                      <v-icon v-if="item.isMic" style="font-size: 16px; color: #3d3d3d">fas fa-microphone-alt</v-icon>
                      <v-icon v-else style="font-size: 16px; color: #bf5151 !important;">fas fa-microphone-alt-slash
                      </v-icon>
                    </v-btn>
                  </v-list-item-action>
                  <v-list-item-action class="ml-0" v-if="!item.isMy && !isExternalUser">
                    <v-btn color="white" fab
                           class="mr-1 btnInfoLinkUser"
                           :title="$t('gc.deleteuser.dialog')"
                           @click="clickCloseUser(item)">
                      <v-icon style="font-size: 16px; color: #3d3d3d">fas fa-times</v-icon>
                    </v-btn>
                  </v-list-item-action>
                </v-list-item>
              </v-list>
              <!-------------------------------->
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
        <!-------------------------------->

        <!-------------------------------->
        <!-- Ожидающие подключения -->
        <v-expansion-panels v-model="panel2" v-if="numUsersExternal > 0 && !isExternalUser" class="mt-1">
          <v-expansion-panel class="gc-expansion-panel">
            <v-expansion-panel-header>{{ $t('gc.user-ofline-ex') }}
              {{ panel2 == null ? ('(' + numUsersExternal + ')') : '' }}
            </v-expansion-panel-header>
            <v-expansion-panel-content class="gc-log-info-panel">
              <!-------------------------------->
              <v-list color="transparent">
                <v-list-item
                  v-for="(item, i) in itemsUsersInfoExternal"
                  :key="i"
                  disabled
                  class="info-item-users"
                >
                  <v-list-item-icon class="infoUserLinsIkon">
                    <img class="infoUserLinsIkonImg" v-if="!item.isscrS && !item.isOper" :src="item.avatar">
                    <v-icon :color=item.color v-else v-text="item.icon" class="iconInfoUserOSOg"></v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title v-text="item.senderName" style="color: black"
                                       :color=item.colorText></v-list-item-title>
                  </v-list-item-content>
                  <v-list-item-action>
                    <v-btn color="white" fab
                           class="mr-1 btnInfoLinkUser"
                           v-if="!item.isscrS"
                           :title="$t('gc.yes.newUsersExt')"
                           @click="addUser(item)">
                      <v-icon style="font-size: 16px; color: #65bf51 !important;">fas fa-check</v-icon>
                    </v-btn>
                  </v-list-item-action>
                  <v-list-item-action class="ml-0">
                    <v-btn color="white" fab
                           class="mr-1 btnInfoLinkUser"
                           :title="$t('gc.no.newUsersExt')"
                           @click="disUser(item)">
                      <v-icon style="font-size: 16px; color: #bf5151">fas fa-times</v-icon>
                    </v-btn>
                  </v-list-item-action>
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

var This

export default {
  name: "LinkUserInfo",
  mounted() {
    This = this
  },
  props: {
    itemsUsersInfo: null,         // страница в режиме звонка
    itemsUsersInfoExternal: null, // хотят подключения
    numUsersExternal: 0,           // сколько хотят подключиться
    isExternalUser: false,
    numUsers: 0,
  },
  data() {
    return {
      room: '',
      titleActiveUs: this.$t('gc.user-online'),
      panel1: 0,
      panel2: 0,
    }
  },

  computed: {
    itemsUsersInfoCount: function () {
      return this.itemsUsersInfo.length
    },
  },

  methods: {
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

    clickCloseChat() {
      this.$emit('clickCloseInfoUser')
    },

    addUser(item) {
      console.log('add user ', item)
      this.$emit('addUser', item.senderId, item.senderName)
    },

    disUser(item) {
      this.$emit('disUser', item.senderId, item.senderName)
    }
  }
}
</script>

<style scoped>
i.v-icon.notranslate.divinfo2I.fa.fa-chalkboard-teacher.theme--light.green--text {
  margin-top: 4px;
}

.divMainLink {
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

.info-item-users {
  margin-top: 1px;
  margin-bottom: 1px;
  height: 42px;
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

.v-list-item__icon.infoUserLinsIkon {
  margin-right: 14px !important;
  margin-top: 0;
  padding-top: 4px;
}

.infoUserLinsIkonImg {
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

.divMainLinkUser .toolbarUser {
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
  top: 24px;
  left: 24px;
  background: #ffffff;
}
/*------------------------------------------------------------------*/
</style>
