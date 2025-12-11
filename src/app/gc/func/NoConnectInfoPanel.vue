<template>
  <div v-if="isShow" class="divNoConnect">

    <div v-if="!isPhone" class="v-toolbarNoConnectGC" style="height: 48px;">
      <img class="pr-3 ml-3 mt-3" height="26" src="/img/IKSAR-Logo-Rus-Yellow-Dot-white.d8fae3af.png">
      <span v-if="isName" :title=nameLink class="v-toolbarNoConnectGCname">{{ nameLink }}</span>
    </div>
    <div v-else class="v-toolbarNoConnectGC" style="height: 48px;">
      <img class="pr-3 ml-3 mt-3" height="26" src="/img/IKSAR-Logo-Rus-Yellow-Dot-white.d8fae3af.png">
      <span v-if="isName" :title=nameLink class="v-toolbarNoConnectGCname">{{ nameLink }}</span>
    </div>

    <v-card v-if="!isPhone"
      class="ma-2 divNoConnectBlock"
      outlined
      tile
    >
      <v-card-title>
        <div class="pl-1 pr-2 ">
          <span>{{ infoTitle }}</span>
        </div>
      </v-card-title>

      <v-card-text class="divNoConnectText">

        <div class="pl-1 pr-2 divNoConnectTextS">
          <span>{{ infoText }}</span>
        </div>
<!--        <v-progress-circular-->
<!--          v-if="isWaitAdd"-->
<!--          class="mainCircularStartWt"-->
<!--          :size="50"-->
<!--          color="primary"-->
<!--          indeterminate-->
<!--        ></v-progress-circular>-->
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
    <v-card v-else
      class="ma-2 divNoConnectBlockPh"
      outlined
      tile
    >
      <v-card-title style="height: auto!important;">
        <div class="pl-1 pr-2 ">
          <span>{{ infoTitle }}</span>
        </div>
      </v-card-title>

      <v-card-text class="divNoConnectText">
        <div class="pl-1 pr-2 divNoConnectTextS">
          <span>{{ infoText }}</span>
        </div>

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
</template>

<script>
import expertRU from '@/lang/expert-ru.json'
import expertEN from '@/lang/expert-en.json'

var This

export default {
  name: "NoConnectInfoPanel",
  data() {
    return {
      isShow: false,
      isPhone: false,
      isName: false,
      infoTitle:'',
      infoText:'',
      nameLink:'',
      paramValueLang: this.$route.params.lang, // данные о языке
    }
  },

  mounted() {
    This = this
    this.$root.setLocaleModule(expertRU, expertEN)
  },

  methods: {
    showInfo(title, mess, nameLink){
      this.isShow = true
      this.isPhone = false
      this.isName = true

      this.nameLink = nameLink
      this.infoTitle = title
      this.infoText = mess

    },
    showLinkError(mess, type, nameLink){
      this.isShow = true
      this.isPhone = false
      this.isName = false

      if(type >= 2 && type <= 4){
        this.isName = true
        this.nameLink = nameLink
      }
      this.infoTitle = this.$t('gc.no.connect.info.1.panel.title')
      this.infoText = mess
    },

    showInfoPhoneName(title, mess, nameLink){
      this.isShow = true
      this.isPhone = true
      this.isName = true
      this.nameLink = nameLink

      this.infoTitle = title
      this.infoText = mess
    },

    showInfoPhone(mess, type, nameLink){
      this.isShow = true
      this.isPhone = true
      this.isName = false

      if(type >= 2 && type <= 4){
        this.isName = true
        this.nameLink = nameLink
      }
      this.infoTitle = this.$t('gc.no.connect.info.1.panel.title.ph')
      this.infoText = mess
    },

    closeInfo(){
      this.isShow = false
    }
  }
}
</script>

<style scoped>
.divNoConnect{
  top: 0;
  width: 100%;
  height: 100%;
  background: var(--v-xr2L1-base);
  position: absolute;
  z-index: 100000;
  left: 0;
}
.divNoConnectBlock {
  position: fixed;
  top: calc(50% - 20vh);
  left: calc(50% - 20vw);
  width: 40vw;
  background: white;
  border-radius: 4px !important;
}
.divNoConnectBlockPh {
  position: fixed;
  top: 32%;
  margin-left: 0px!important;
  width: 100%;
  background: white;
  border-radius: 4px !important;
}
@media screen and (orientation:landscape) {
  .divNoConnectBlockPh {
    position: fixed;
    top: 32%;
    left: calc(50% - 35vw);
    width: 70vw;
    background: white;
    border-radius: 4px !important;
  }
}

.divNoConnectText {
  margin-top: 30px;
  font-size: 18px;
}
.divNoConnectTextS {
  text-align: center
}

.v-toolbarNoConnectGC{
  background: var(--v-xr2D2-base)!important;
}
.v-toolbarNoConnectGCname{
  position: absolute;
  font-size: 24px;
  color: var(--xr-editor-menu-paginator-color);
  margin-top: 8px;
  margin-left: 8px;
  width: -webkit-fill-available;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
</style>
