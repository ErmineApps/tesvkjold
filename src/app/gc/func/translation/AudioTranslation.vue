<!--<template>-->
<!--  <div id="mainAT" class="mainAT">-->
<!--    <v-row no-gutters class="vrowAT">-->
<!--      <v-col class="atclassRM">-->
<!--        <p class="pMainAT">Переводчик</p>-->
<!--        <v-list id="idATlist" class="atvList" :key="componentKey">-->
<!--          <v-list-item-group id="idATlist-item-group" class="atlistItemGroup ">-->
<!--            <v-list-item-->
<!--              v-for="(item, i) in textListTranslate"-->
<!--              :key="i"-->
<!--              :id="'idATlistItem' + (i + 1)"-->
<!--              disabled-->
<!--              class="info-item-users"-->
<!--            >-->

<!--              <v-list-item-content class="atListItemContent">-->
<!--                <v-list-item-title class="atListItemTitle">-->
<!--                  <v-list-item-icon class="atListItemIcon">-->
<!--                    <v-icon v-if="item.avatar == ''" v-text="item.icon" class="atListItemIconI"></v-icon>-->
<!--                    <img v-else class="atListItemIconImg" :src="item.avatar">-->
<!--                  </v-list-item-icon>-->
<!--                  <span class="spanATName">{{ item.senderName }}</span>-->
<!--                </v-list-item-title>-->
<!--                <v-list-item-subtitle class="atListItemSubtitle" v-html="item.text">-->
<!--                </v-list-item-subtitle>-->
<!--              </v-list-item-content>-->
<!--            </v-list-item>-->
<!--          </v-list-item-group>-->
<!--        </v-list>-->
<!--        <div>-->

<!--          <v-row class="vRowBtnSettAT">-->
<!--            <p v-if="isStreamMicSave" class="pInfoAudioSaveAT">{{ textStreamMicSave }}</p>-->
<!--            <div id="idAudioGraph" v-if="isStreamMicSave" class="atAudioGraph body"></div>-->
<!--            <v-progress-circular-->
<!--              v-if="isScrollDownloadAT"-->
<!--              class="atScrollDown"-->
<!--              :size="50"-->
<!--              color="primary"-->
<!--              indeterminate-->
<!--            ></v-progress-circular>-->
<!--            <v-btn color="white" fab-->
<!--                   class="mr-1 xBtnAT"-->
<!--                   :title="isStreamMicSave ? $t('gc.at.title.save') : $t('gc.at.title.nosave')"-->
<!--                   @click="clickEditAudio"-->
<!--                   @mouseover="isMouseBtnATSave = true"-->
<!--                   @mouseout="isMouseBtnATSave = false">-->
<!--              <v-icon v-if="isStreamMicSave" class="iconxBtnAT" style="color:#951818;" >fas fa-stop-circle</v-icon>-->
<!--              <v-icon v-if="!isStreamMicSave" class="iconxBtnAT">fas fa-solid fa-microphone</v-icon>-->
<!--              <p v-if="isStreamMicSave && isMouseBtnATSave" class="pxBtnAT" style="color:#951818;">Остановить</p>-->
<!--              <p v-if="!isStreamMicSave && isMouseBtnATSave" class="pxBtnAT">Начать перевод</p>-->
<!--            </v-btn>-->
<!--            <v-btn color="white" fab-->
<!--                   class="mr-1 xBtnAT flag"-->
<!--                   :title="isEnRu ? $t('gc.at.title.save') : $t('gc.at.title.nosave')"-->
<!--                   @click="clickIsEnRu">-->
<!--              <span v-if="isEnRu" style="height: 20px" class="flag-icon flag-icon-squared flag-icon-gb"></span>-->
<!--              <span v-else style="height: 20px" class="flag-icon flag-icon-squared flag-icon-ru"></span>-->
<!--            </v-btn>-->
<!--          </v-row>-->

<!--        </div>-->
<!--      </v-col>-->
<!--      <v-col v-if="isEditMessage" class="atclassLM">-->


<!--      </v-col>-->
<!--    </v-row>-->
<!--  </div>-->

<!--</template>-->

<!--<script>-->
<!--import $ from "jquery";-->
<!--import iconsChack from "@/assets/img/icons-check.gif"-->

<!--var This-->

<!--export default {-->
<!--  name: "AudioTranslation",-->
<!--  data() {-->
<!--    return {-->
<!--      isShow: false,-->
<!--      isStreamMicSave: false,// статус записи видео-->
<!--      isMouseBtnATSave: false, // интерфейс кнопки для записи видео-->
<!--      textListTranslate: [],//text переведенного текста, пока временно-->
<!--      mediaRecorder: null, // рекордер для записи видео-->
<!--      voiceBlob: null, //блоб с сохраненным аудио-->
<!--      textStreamMicSave: 'Запись началась', //fext info save video-->
<!--      isScrollDownloadAT: false, // скролл загрузки файла-->
<!--      iconsChack_: iconsChack, // иконка чек-->
<!--      isEditMessage: false, // активирован ли режим редактирования-->
<!--      isEnRu: true, // isEnRu true en>ru, false ru>en-->
<!--      spanEn:`<span style="height: 11px; margin-top: 0px; top: 2px;" class="flag-icon flag-icon-squared flag-icon-gb"></span>`,-->
<!--      spanRu:`<span style="height: 11px; margin-top: 0px; top: 2px;" class="flag-icon flag-icon-squared flag-icon-ru"></span>`,-->
<!--      componentKey: 0-->
<!--    }-->
<!--  },-->

<!--  mounted() {-->
<!--    This = this-->
<!--    // this.$i18n.locale = this.paramValueLang-->
<!--  },-->

<!--  methods: {-->
<!--    show(sender, room, mainUser, _oDraw, _delta) {-->
<!--    },-->

<!--    clickEditAudio() {-->
<!--      // This.textListTranslate[This.textListTranslate.length] = {-->
<!--      //   text: `sad;fjsaiod jifj iodjfoidsajoi fjodsagfio ajdgo;idsa jgiojsa<span style="  font-size: 14px; color: #a5a5a5;">  (12:23:23)</span> `,-->
<!--      //   senderId: 123,-->
<!--      //   icon: 'fa-user',-->
<!--      //   color: 'green',-->
<!--      //   avatar: '',-->
<!--      //   senderName: 'Очень много текста в имени Говорящего'-->
<!--      // }-->
<!--      // console.log(This.textListTranslate)-->

<!--      This.isStreamMicSave = !This.isStreamMicSave-->
<!--      This.isScrollDownloadAT = false-->

<!--      if (This.isStreamMicSave) {-->
<!--        navigator.mediaDevices.getUserMedia({audio: true})-->
<!--          .then(stream => {-->
<!--            This.mediaRecorder = new MediaRecorder(stream);-->

<!--            This.audioBarGraph(stream)-->

<!--            let voice = [];-->
<!--            This.mediaRecorder.start()-->
<!--            var tik = 0-->
<!--            setTimeout(function run() {-->
<!--              tik++-->
<!--              if (!This.isStreamMicSave) {-->
<!--                tik = 40-->
<!--              } else if (tik >= 40) {-->
<!--                This.stopSaveAudio()-->
<!--              } else {-->
<!--                This.textStreamMicSave = 'Запись ' + tik + ' сек.'-->
<!--                setTimeout(run, 1000);-->
<!--              }-->

<!--            }, 1000);-->

<!--            This.mediaRecorder.addEventListener("dataavailable", function (event) {-->
<!--              voice.push(event.data);-->
<!--            });-->

<!--            This.mediaRecorder.addEventListener("stop", function () {-->
<!--              This.voiceBlob = new Blob(voice, {-->
<!--                type: 'audio/wav'-->
<!--              });-->
<!--              console.log('This.voiceBlob', This.voiceBlob)-->
<!--              This.sendBlobServer()-->
<!--            });-->

<!--          });-->
<!--      } else {-->
<!--        if (This.mediaRecorder) {-->
<!--          This.stopSaveAudio()-->
<!--        }-->
<!--      }-->
<!--    },-->

<!--    stopSaveAudio() {-->
<!--      This.mediaRecorder.stop()-->
<!--      This.isStreamMicSave = false-->
<!--      This.textStreamMicSave = 'Запись началась'-->
<!--      This.isScrollDownloadAT = true-->
<!--    },-->

<!--    sendBlobServer() {-->
<!--      console.log('отправка аудио на сервер')-->
<!--      let reader = new FileReader();-->

<!--      console.log('This.voiceBlob', This.voiceBlob)-->

<!--      reader.readAsDataURL(This.voiceBlob);-->
<!--      reader.onloadend = function () {-->
<!--        var base64String = reader.result;-->
<!--        var bBase64String = base64String.substr(base64String.indexOf(', ') + 1)-->
<!--        console.log("bBase64String", bBase64String)-->

<!--        This.$emit('sendBlobAudioTranslate', bBase64String, This.isEnRu, '', '')-->
<!--      }-->
<!--    },-->

<!--    restOneATmessage(message) {-->
<!--      console.log('AT restOneATmessage ', message)-->
<!--      This.isScrollDownloadAT = false-->

<!--      if (message.ResultCode == 200) {-->
<!--        console.log('Полученны данные для перевода')-->
<!--        // This.$root.showSucc('Полученны данные для перевода')-->
<!--      } else {-->
<!--        This.$root.showErr('Ошибка после отправки файла для перевода, код ошибки ' + message.ResultCode + ', текст ошибки ' + message.ResultMessage)-->
<!--      }-->
<!--    },-->

<!--    clickIsEnRu() {-->
<!--      if(This.isStreamMicSave){-->
<!--        This.$root.showInfo('Нельзя поменять язык пока идет запись!')-->
<!--      }else{-->
<!--        This.isEnRu = !This.isEnRu-->
<!--      }-->
<!--    },-->

<!--    // полученны данные перевода-->
<!--    restATmessage(message) {-->
<!--      console.log('AT Получен перевод restATmessage ', message)-->

<!--      var time = message.Timestamp.substring(8, 10) + ':' + message.Timestamp.substring(10, 12) + ':' + message.Timestamp.substring(12, 14)-->

<!--      if(message.langSrc == 'EN'){-->
<!--        This.textListTranslate[This.textListTranslate.length] = {-->
<!--          text: `${message.TextTgtFull}<span style="  font-size: 14px; color: #a5a5a5;"> (${time}  `+This.spanEn+`⇨`+This.spanRu+`)</span> `,-->
<!--          senderId: message.SenderId,-->
<!--          icon: 'fa-user',-->
<!--          color: 'green',-->
<!--          avatar: `data:image/jpg;base64,${message.avatar}`,-->
<!--          senderName: message.senderName-->
<!--        }-->
<!--      }else{-->
<!--        This.textListTranslate[This.textListTranslate.length] = {-->
<!--          text: `${message.TextTgtFull}<span style="  font-size: 14px; color: #a5a5a5;"> (${time}  `+This.spanRu+`⇨`+This.spanEn+`)</span> `,-->
<!--          senderId: message.SenderId,-->
<!--          icon: 'fa-user',-->
<!--          color: 'green',-->
<!--          avatar: `data:image/jpg;base64,${message.avatar}`,-->
<!--          senderName: message.senderName-->
<!--        }-->
<!--      }-->
<!--      console.log(This.textListTranslate)-->
<!--      This.componentKey++-->

<!--      setTimeout(function run() {-->
<!--        var element = document.getElementById("idATlistItem"+This.textListTranslate.length);-->
<!--        if(element){-->
<!--          element.scrollIntoView(false);-->
<!--        }else{-->
<!--          setTimeout(run, 500);-->
<!--        }-->
<!--      },500)-->

<!--      // This.isScrollDownloadAT = false-->
<!--      //-->
<!--    },-->

<!--    audioBarGraph(stream) {-->
<!--      /*СОЗДАЁМ ОСНОВНЫЕ ПЕРЕМЕННЫЕ*/-->
<!--      var body, num, array, width, context, logo, myElements, analyser, src, height;-->

<!--      /*ЗАПИСЫВАЕМ В ПЕРЕМЕННУЮ body ЭЛЕМЕНТ body*/-->
<!--      body = document.getElementById('idAudioGraph');-->

<!--      /*УКАЗЫВАЕМ КОЛИЧЕСТВО СТОЛБЦОВ НА ИНДИКАТОРЕ*/-->
<!--      num = 32;-->

<!--      /*СОЗДАЕМ МАССИВ*/-->
<!--      array = new Uint8Array(num * 2);-->

<!--      /*УКАЗЫВАЕМ ДЛИНУ СТОЛБИКОВ В PX*/-->
<!--      width = 1;-->


<!--      if (context) return;-->

<!--      /*ПРОПИСЫВАЕМ ЦИКЛ ВНУТРИ КОТОРОГО БУДЕМ СОЗДАВАТЬ ЭЛЕМЕНТЫ НАШИХ СТОЛБИКОВ*/-->
<!--      for (var i = 0; i < num; i++) {-->
<!--        logo = document.createElement('div'); /*КАЖДЫЙ ЭЛЕМЕНТ БУДЕТ ЗАПИСЫВАТЬСЯ ВНУТРИ ПЕРЕМЕННОЙ logo*/-->
<!--        logo.className = 'logo'; /*ДЛЯ ВЫШЕ СОЗДАННОГО ЭЛЕМЕНТА МЫ ПРОПИСЫВАЕМ КЛАСС logo*/-->
<!--        logo.style.background = '#951818';-->
<!--        logo.style.minWidth = width + 'px'; /*УКАЗЫВАЕМ ЕГО ШИРИНУ*/-->
<!--        body.appendChild(logo); /*ДОБАВЛЯЕМ ЭЛЕМЕНТ ВО ВНУТРЬ ЭЛЕМЕНТА body*/-->
<!--      }-->

<!--      myElements = document.getElementsByClassName('logo');/*ЗАПИСЫВАЕМ ЭЛЕМЕНТ logo В ПЕРЕМЕННУЮ myElement*/-->
<!--      context = new AudioContext(); /*СОЗДАЕМ НОВЫЙ ЭКЗЕМПЛЯР КЛАССА AudioContext*/-->

<!--      /*СОЗДАЁМ АНАЛАЙЗЕР*/-->
<!--      analyser = context.createAnalyser();-->

<!--      src = context.createMediaStreamSource(stream);-->
<!--      src.connect(analyser);-->
<!--      loop();-->

<!--      function loop() {-->
<!--        if(myElements.length > 0){-->
<!--          window.requestAnimationFrame(loop);-->
<!--          analyser.getByteFrequencyData(array);-->
<!--          for (var i = 0; i < num; i++) {-->
<!--            height = array[i + num];-->
<!--            if(height / 4 >45){-->
<!--              myElements[i].style.minHeight = '45px';-->
<!--            }else{-->
<!--              myElements[i].style.minHeight = height / 4 + 'px';-->
<!--            }-->
<!--            myElements[i].style.opacity = 0.008 * height;-->
<!--          }-->
<!--        }-->
<!--      }-->
<!--    }-->

<!--  }-->
<!--}-->
<!--</script>-->

<!--<style scoped>-->
<!--.mainAT {-->
<!--  position: fixed;-->
<!--  left: 0;-->
<!--  bottom: 0px;-->
<!--  width: 100%;-->
<!--  height: 200px;-->
<!--  z-index: 100000;-->
<!--  background: #1b1b1b;-->
<!--}-->

<!--.pMainAT {-->
<!--  font-size: 18px;-->
<!--  color: wheat;-->
<!--  margin-bottom: 0;-->
<!--  margin-left: 12px;-->
<!--}-->

<!--.pInfoAudioSaveAT {-->
<!--  margin-top: 16px;-->
<!--  left: 0;-->
<!--  text-align: center;-->
<!--  color: red;-->
<!--  font-size: 16px;-->
<!--}-->

<!--.pxBtnAT {-->
<!--  margin-top: 14px;-->
<!--  margin-left: 8px;-->
<!--  pointer-events: none;-->
<!--}-->

<!--i.v-icon.notranslate.iconxBtnAT.fas {-->
<!--  padding: 0;-->
<!--  pointer-events: none;-->
<!--  font-size: 22px;-->
<!--}-->

<!--button.mr-1.xBtnAT {-->
<!--  right: 4px;-->
<!--  top: 4px;-->
<!--  width: auto;-->
<!--  height: 45px;-->
<!--  border-radius: 4px;-->
<!--  font-size: 16px;-->
<!--}-->

<!--button.mr-1.xBtnAT.flag {-->
<!--  right: 4px;-->
<!--  top: 4px;-->
<!--  width: 45px;-->
<!--  height: 45px;-->
<!--  border-radius: 4px;-->
<!--  font-size: 16px;-->
<!--}-->

<!--.vrowAT {-->
<!--  width: 100%;-->
<!--  height: 100%;-->
<!--}-->

<!--.pTextAT {-->
<!--  width: 100%;-->
<!--  height: 150px;-->
<!--  color: white;-->
<!--  padding-left: 8px;-->
<!--  overflow-y: auto;-->
<!--}-->

<!--.v-list.atvList.v-sheet.theme&#45;&#45;light {-->
<!--  background: #1b1b1b;-->
<!--}-->

<!--.atlistItemGroup {-->
<!--  height: 165px;-->
<!--  overflow-y: auto;-->
<!--  min-width: 720px;-->
<!--}-->

<!--::-webkit-scrollbar {-->
<!--  height: 6px; /* высота для горизонтального скролла */-->
<!--  width: 6px;-->
<!--}-->

<!--::-webkit-scrollbar-track {-->
<!--  background: #343434;-->
<!--}-->

<!--::-webkit-scrollbar-thumb {-->
<!--  background: #318de2;-->
<!--  border-radius: 2px;-->
<!--}-->

<!--::-webkit-scrollbar-thumb:horizontal:hover {-->
<!--  background: #47c0ff;-->
<!--}-->

<!--:hover::-webkit-scrollbar-thumb {-->
<!--  background: #318de2;-->
<!--}-->

<!--.avsdfwerwer {-->

<!--}-->

<!--.atListItemTitle {-->
<!--  color: #cbc8c8;-->
<!--  margin: 0;-->
<!--  padding: 0;-->
<!--  font-size: 17px;-->
<!--}-->

<!--.atListItemIcon {-->
<!--  color: #ffffff;-->
<!--  margin: 0 !important;-->
<!--  padding: 0 !important;-->
<!--}-->

<!--.atListItemIconI {-->

<!--}-->

<!--.atListItemSubtitle {-->
<!--  font-size: 16px;-->
<!--  color: white;-->
<!--  -webkit-box-flex: 1;-->
<!--  -ms-flex: 1 1 100%;-->
<!--  flex: 1 1 100%;-->
<!--  overflow: hidden;-->
<!--  text-overflow: initial;-->
<!--  white-space: normal;-->
<!--}-->

<!--.atSpanTextTime {-->

<!--}-->

<!--.atclassRM {-->

<!--}-->

<!--.atclassLM {-->

<!--}-->

<!--.vRowBtnSettAT {-->
<!--  position: absolute;-->
<!--  right: 0;-->
<!--  top: 0;-->
<!--  width: auto;-->
<!--}-->

<!--.atListItemContent {-->
<!--  margin-bottom: 8px;-->
<!--}-->

<!--img.atListItemIconImg {-->
<!--  width: 34px;-->
<!--  height: 34px;-->
<!--  border-radius: 25px;-->
<!--}-->

<!--.spanATName {-->
<!--  margin-left: 10px;-->
<!--  padding-bottom: -73px;-->
<!--  position: absolute;-->
<!--  margin-top: 6px;-->
<!--}-->

<!--.v-progress-circular.atScrollDown.v-progress-circular&#45;&#45;visible.v-progress-circular&#45;&#45;indeterminate.primary&#45;&#45;text[data-v-f4ffdf90] {-->
<!--  /* position: absolute; */-->
<!--  top: 8px;-->
<!--  right: -43px;-->
<!--  height: 44px !important;-->
<!--  width: 44px !important;-->
<!--  z-index: 3;-->
<!--  pointer-events: none;-->
<!--  color: #5ac95a !important;-->
<!--}-->

<!--/*.body {*/-->
<!--/*  margin: 0;*/-->
<!--/*  min-height: 100vh;*/-->
<!--/*  display: flex;*/-->
<!--/*  justify-content: center;*/-->
<!--/*  align-items: center;*/-->
<!--/*}*/-->

<!--.atAudioGraph {-->
<!--  float: right;-->
<!--}-->

<!--.body {-->
<!--  margin-top: 7px!important;-->
<!--  margin-right: 0px;-->
<!--  display: -webkit-box;-->
<!--  right: 0px;-->
<!--  display: -ms-flexbox;-->
<!--  -webkit-box-pack: center;-->
<!--  -ms-flex-pack: center;-->
<!--  justify-content: center;-->
<!--  -webkit-box-align: center;-->
<!--  -ms-flex-align: center;-->
<!--  align-items: center;-->
<!--  width: 45px;-->
<!--  max-height: 45px;-->
<!--}-->

<!--.logo {-->
<!--margin: 2px;-->
<!--border-radius: 30px;-->
<!--}-->

<!--</style>-->
