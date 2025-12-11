<template>
  <div id="mainChatMessage" class="divMainChat" style="display: none">
    <p class="pcahtmessagTitle">{{ this.$t('gc.chat.title') }}</p>
    <v-btn :title="$t('close')" class="btncloseChat" @click="clickCloseChat">
            <span class="fa-layers fa-fw">
              <i class="fas fa-times" style="font-size: 12px;     margin-right: 3px;
    margin-bottom: 4px;"></i>
            </span>
    </v-btn>

    <!--    <XrChatMessage></XrChatMessage>-->

    <chat-window
      :accepted-files="acceptedFiles"
      :current-user-id="currentSenderID"
      :message-actions="messageActions"
      :menu-actions="menuActions"
      :messages="messages"
      :rooms="rooms"
      :link-options="{ disabled: false, target: '_blank' }"
      :messageSelectionEnabled=true
      :message-selection-enabled=false
      :show-add-room=true
      :show-audio=false
      :show-emojis=false
      :show-reaction-emojis=false
      :show-search=true
      :single-room=true
      :text-messages="textMessages"
      @send-message="sendMessage"
      @message-action-handler="messageActionHandler"
      @toggle-rooms-list="toggleRoomsList"
      @room-info="roomInfo"
      @fetch-messages="fetchMessages"
      @edit-message="editMessage"
      @delete-message="deleteMessage"
      @open-file="openfile"
      @open-user-tag="openusertag"
      @open-failed-message="openfailedmessage"
      @menu-action-handler="menuactionhandler"
      @message-selection-action-handler="messageselectionactionhandler"
      @send-message-reaction="sendmessagereaction"
      @typing-message="typingmessage"
      @textarea-action-handler="textareaactionhandler"
    />
    <!--      :link-options=true-->
    <!--      :text-formatting=true-->

    <div v-if="isImgCloseBtn" id="idImgChatFull" class="divFullImg">
      <v-btn id="idbtncloseChatimg" class="btncloseChatimg" @click="clickCloseChatimg">
            <span class="fa-layers fa-fw">
              <i class="fas fa-times" style="font-size: 12px; margin-right: 3px; margin-bottom: 4px;"></i>
            </span>
      </v-btn>
      <img id="imgFullImgChat" class="imgFullImg"></img>

    </div>

    <v-row id="idRowSettingLang" class="rowSettLangUpdate">
      <p id="idPLengthMessage" class="pLengthMessage" style="display: none">{{ textMessageLength }}</p>
      <div id="divSelectSrc" style="width: 50%; margin-right: 2px; margin-left: -2px">
        <v-select
          id="idSelectSrcLang"
          v-model="langSrc"
          :height="12"
          :items="langSelect"
          :label="$t('gc.at.select.src')"
          :menu-props="{ contentClass: 'at-item-type' }"
          class="vselectSettingsAT"
          dense
          outlined
        ></v-select>
      </div>
      <div id="divSelectTgt" style="width: 50%;">
        <v-select
          id="idSelectTgtLang"
          v-model="langTgt"
          :items="langSelect"
          :label="$t('gc.at.select.tgt')"
          :menu-props="{ contentClass: 'at-item-type' }"
          class="vselectSettingsAT"
          dense
          outlined
        ></v-select>
      </div>

    </v-row>

    <div v-if="isSendFileAudioTranslate" class="centerATt">
      <svg
        height="34px"
        style="width: 32px;"
        version="1.1"
        viewBox="0 0 22.577778 22.577778"
        width="34px">
        <g
          transform="translate(0,-274.42223)">
          <path
            d="m 4.4042704,275.39237 c -0.7927264,0 -1.4426225,0.64993 -1.4426261,1.44263 v 17.75227 c 3.6e-6,0.79269 0.6498997,1.44263 1.4426261,1.44261 H 18.17351 c 0.792722,2e-5 1.442623,-0.64992 1.442623,-1.44261 v -13.0441 c 0,-0.56074 -0.231049,-1.09621 -0.641013,-1.47878 l -4.37793,-4.08107 c -0.442843,-0.40575 -0.936479,-0.59095 -1.494768,-0.59095 z m 0,0.71193 h 9.0659366 v 3.99137 c 0,0.59261 0.487114,1.07971 1.079707,1.07971 h 4.354292 v 13.41189 c 0,0.40787 -0.322666,0.73069 -0.730696,0.73069 H 4.4042704 c -0.4080299,0 -0.730002,-0.32282 -0.730002,-0.73069 V 276.835 c 0,-0.40788 0.3219721,-0.7307 0.730002,-0.7307 z m 9.7778626,0.46233 4.174224,3.89682 h -3.806443 c -0.207907,0 -0.367781,-0.15996 -0.367781,-0.36778 z m -8.3296764,2.37772 c -0.1957797,7.1e-4 -0.354194,0.15949 -0.3545748,0.35527 -7.12e-4,0.19659 0.1579799,0.35659 0.3545748,0.35735 h 5.7989994 c 0.196865,-3.5e-4 0.356038,-0.16049 0.355269,-0.35735 -3.56e-4,-0.19606 -0.159219,-0.35489 -0.355269,-0.35527 z m 0,1.77912 c -0.1965949,7.1e-4 -0.3553437,0.16076 -0.3545748,0.35736 7.119e-4,0.19551 0.1590656,0.35381 0.3545748,0.35457 h 1.4502722 c 0.1965914,7.2e-4 0.3565825,-0.15798 0.3573514,-0.35457 7.12e-4,-0.19769 -0.1596708,-0.35813 -0.3573514,-0.35736 z m 2.9005409,0 c -0.1976806,-7.1e-4 -0.3581274,0.15967 -0.3573514,0.35736 7.118e-4,0.19659 0.16076,0.35534 0.3573514,0.35457 h 3.6235945 c 0.19578,-3.6e-4 0.354504,-0.15879 0.355269,-0.35457 7.12e-4,-0.19687 -0.158404,-0.35698 -0.355269,-0.35736 z m -2.9005409,1.78191 c -0.1955092,7.1e-4 -0.3538131,0.15906 -0.3545748,0.35457 7.119e-4,0.19551 0.1590656,0.35381 0.3545748,0.35457 h 5.7989994 c 0.19578,-3.6e-4 0.354507,-0.15879 0.355269,-0.35457 -7.12e-4,-0.19578 -0.159489,-0.3542 -0.355269,-0.35457 z m 7.2499654,0 c -0.196591,-7.2e-4 -0.356582,0.15797 -0.357351,0.35457 7.12e-4,0.19659 0.160756,0.35534 0.357351,0.35457 h 4.708172 c 0.196866,0.001 0.357284,-0.1577 0.35805,-0.35457 -7.12e-4,-0.19687 -0.161184,-0.35573 -0.35805,-0.35457 z m -7.2499654,1.77842 c -0.1957797,7.1e-4 -0.354194,0.15949 -0.3545748,0.35527 -7.12e-4,0.19659 0.1579799,0.35659 0.3545748,0.35735 H 17.810594 c 0.197952,10e-4 0.358826,-0.1594 0.35805,-0.35735 -3.57e-4,-0.19714 -0.160914,-0.35643 -0.35805,-0.35527 z m 2.1086616,3.70215 C 7.8047223,287.69457 7.5168336,287.48747 7.166459,287.48747 H 6.8563831 c -0.5342259,0 -0.9198017,0.48176 -0.9198052,1.0088 v 0.23915 H 5.4422625 c -0.6903656,0 -1.2034616,0.64215 -1.2034616,1.39257 0,0.75043 0.513096,1.39535 1.2034616,1.39535 h 0.4943154 v 0.23916 c 3.5e-6,0.52703 0.3855793,1.00671 0.9198052,1.00671 H 7.166459 c 0.3505881,0 0.6383273,-0.2076 0.7946592,-0.49849 l 3.8954328,1.92789 c 0.535903,0.25328 1.011576,-0.15969 1.011576,-0.80648 v -1.60878 h 0.207879 c 0.846513,0 1.487125,-0.77292 1.487118,-1.66023 -4e-6,-0.88732 -0.640616,-1.66093 -1.487118,-1.66093 h -0.207879 v -1.59489 c 0,-0.67497 -0.616482,-1.00886 -1.058851,-0.79117 z m 4.1269338,5.5348 -0.55967,-0.27739 v -6.23284 l 0.570099,-0.28295 z m -1.272287,-6.15634 v 5.52578 l -2.727429,-1.35087 v -2.82058 z m 5.746858,0.40116 c -0.138825,0.13807 -0.140068,0.36235 -0.0028,0.50196 1.12302,1.12971 1.123003,2.64893 0,3.77864 -0.137284,0.13961 -0.136042,0.36388 0.0028,0.50196 0.138847,0.13804 0.363114,0.13804 0.501961,0 1.3613,-1.36942 1.36132,-3.41315 0,-4.78256 -0.190732,-0.16364 -0.351905,-0.11866 -0.501961,0 z m -9.9141331,0.73071 c 0,-0.19356 0.1175533,-0.29687 0.2078789,-0.29687 l 0.310076,-10e-6 c 0.090219,0 0.2096694,0.10305 0.2099648,0.29617 v 3.26693 c 0,0.19357 -0.1196359,0.29409 -0.2099648,0.29409 h -0.310076 c -0.09033,0 -0.2078789,-0.1005 -0.2078789,-0.29409 z m 8.5820511,0.2468 c -0.156126,0.12148 -0.183293,0.34689 -0.06049,0.50195 0.171207,0.21848 0.27601,0.56232 0.27601,0.91215 0,0.34985 -0.104805,0.69576 -0.27601,0.91425 -0.12091,0.15461 -0.09385,0.37793 0.06049,0.49919 0.154613,0.12095 0.377919,0.0939 0.499182,-0.0605 0.297958,-0.38023 0.428269,-0.86948 0.428269,-1.35295 0,-0.48345 -0.130312,-0.97338 -0.428269,-1.35362 -0.179064,-0.17361 -0.336785,-0.16549 -0.499182,-0.0605 z m -2.362428,0.43104 h 0.207879 c 0.402594,0 0.775192,0.39516 0.775192,0.949 3e-6,0.55382 -0.372601,0.94831 -0.775192,0.94831 h -0.207879 z m -7.4258647,0.27323 h 0.4943153 v 1.36406 H 5.4422483 c -0.2464582,0 -0.4915353,-0.23319 -0.4915353,-0.68342 0,-0.4502 0.2450771,-0.68064 0.4915353,-0.68064 z"
            style="color:#bf7411;display:inline;fill:#bf7411;stroke-width:1;stroke-linecap:round;stroke-linejoin:round;-inkscape-stroke:none"
          />
        </g>
      </svg>

      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <!--      <v-icon style="font-size: 26px;-->
      <!--    right: 15px;-->
      <!--    position: absolute;-->
      <!--    color: #bf7411;">far fa-file-alt</v-icon>-->
      <svg
        height="34px"
        style="width: 32px;"
        viewBox="0 0 22.577778 22.577778"
        width="34px">
        <g transform="translate(0,-274.42223)">
          <path
            d="m 17.449261,285.83695 c 7.84e-4,0.1966 0.160762,0.35534 0.357354,0.35458 0.195508,-7.2e-4 0.353809,-0.15907 0.354573,-0.35458 v -1.5761 c 0.721289,-0.0237 1.249144,0.11718 1.597662,0.32259 0.398696,0.23501 0.570951,0.54301 0.596517,0.84402 0.02556,0.301 -0.09539,0.6163 -0.410192,0.88017 -0.314801,0.26388 -0.830683,0.46721 -1.565681,0.46721 H 6.4151002 c -0.5855667,0 -1.0692801,0.48441 -1.0692801,1.06997 v 5.33945 c 0,0.58556 0.4837134,1.06649 1.0692801,1.06649 H 18.379494 c 0.860605,0 1.544736,-0.23755 2.01272,-0.62502 0.467984,-0.38748 0.712967,-0.93094 0.67508,-1.47182 l -0.0028,-6.78694 c -0.04624,-0.5444 -0.379654,-1.06303 -0.943441,-1.39535 -0.488418,-0.28788 -1.143526,-0.44557 -1.959883,-0.42132 v -2.00714 c 0.0023,-0.71522 -0.221052,-1.08938 -0.643792,-1.47878 l -4.375149,-4.08107 c -0.442827,-0.40587 -0.939057,-0.59095 -1.497548,-0.59095 H 2.9492814 c -0.7927228,0 -1.4426261,0.64993 -1.4426226,1.44263 v 17.75227 c 0,0.79269 0.6498998,1.44263 1.4426226,1.44261 H 16.718568 c 0.396377,0 0.758488,-0.16272 1.019222,-0.4234 0.137594,-0.13876 0.137594,-0.3625 0,-0.50126 -0.138845,-0.13806 -0.363117,-0.13806 -0.501964,0 -0.132707,0.13265 -0.313258,0.21274 -0.517258,0.21274 H 2.9493299 c -0.4080335,0 -0.7327822,-0.3228 -0.7327822,-0.73069 v -17.75227 c 0,-0.40789 0.3247487,-0.7307 0.7327822,-0.7307 2.96716,0 9.0659361,0 9.0659361,0 v 3.99137 c 0,0.59261 0.487115,1.07971 1.079708,1.07971 h 4.300064 z m -4.722073,-9.26755 4.173529,3.89404 H 13.09497 c -0.207908,0 -0.367782,-0.15996 -0.367782,-0.36778 z m -8.3296755,2.37494 c -0.1960502,3.5e-4 -0.3548881,0.15921 -0.355269,0.35527 -7.118e-4,0.19686 0.1584036,0.35697 0.355269,0.35735 h 1.4134236 c 0.197681,7.1e-4 0.3581278,-0.15967 0.3573535,-0.35735 -3.915e-4,-0.19686 -0.1604888,-0.35604 -0.3573535,-0.35527 z m 2.8636955,0 c -0.1957794,7.1e-4 -0.3541922,0.15949 -0.3545728,0.35527 -7.832e-4,0.19659 0.1579786,0.35659 0.3545728,0.35735 h 3.660441 c 0.196594,-7e-4 0.355343,-0.16076 0.354573,-0.35735 -3.92e-4,-0.19578 -0.158794,-0.3545 -0.354573,-0.35527 z m -2.7642749,1.77912 c -0.196595,7.1e-4 -0.3553438,0.16076 -0.3545749,0.35736 7.119e-4,0.19551 0.1590657,0.35381 0.3545749,0.35457 h 6.4247159 c 0.195508,-7.1e-4 0.353809,-0.15906 0.354573,-0.35457 7.83e-4,-0.1966 -0.157979,-0.35659 -0.354573,-0.35736 z m 8.9004669,4.62752 c 1.783,0 3.082001,-1.15619 3.082001,-1.15619 0.162244,-0.14184 0.162244,-0.39418 0,-0.53602 0,0 -1.299,-1.1534 -3.082001,-1.1534 H 3.8726057 c -0.476421,0.0136 -0.7995252,0.45163 -0.7995252,0.91423 v 1.01436 c 0,0.46995 0.3335872,0.91702 0.8224671,0.91702 z m -9.5018347,-2.13647 h 0.958737 v 1.42455 h -0.958737 c -0.02716,0 -0.1105412,-0.0558 -0.1105412,-0.2051 v -1.01436 c 0,-0.14932 0.083366,-0.20509 0.1105412,-0.20509 z m 1.6685781,0 h 7.4759216 v 1.42455 H 5.5641434 Z m 8.1878476,0.0946 c 0.883768,0.10683 1.488591,0.37165 1.844473,0.61737 -0.355603,0.24564 -0.960157,0.51117 -1.844473,0.61807 z m 6.60617,8.89212 c 0.02095,0.29904 -0.105289,0.61423 -0.42062,0.87531 -0.315323,0.26109 -0.828319,0.46234 -1.558034,0.46234 H 6.4151138 c -0.2008092,0 -0.3573539,-0.15376 -0.3573539,-0.35457 v -5.33945 c 0,-0.20081 0.1565447,-0.35736 0.3573539,-0.35736 H 18.379507 c 0.846455,0 1.508701,-0.24115 1.975873,-0.61737 z M 7.8208941,288.39408 c -0.1965942,7.1e-4 -0.3553426,0.16075 -0.3545727,0.35735 7.475e-4,0.19551 0.1590649,0.35381 0.3545727,0.35458 h 0.9517843 v 3.1981 c -7.831e-4,0.19659 0.1579786,0.35659 0.3545728,0.35735 0.1976808,7.2e-4 0.3581277,-0.15967 0.3573535,-0.35735 v -3.1981 h 0.9483083 c 0.196593,7.1e-4 0.356586,-0.15799 0.357353,-0.35458 7.83e-4,-0.19767 -0.159673,-0.35812 -0.357353,-0.35735 z m 8.1621239,0 c -0.196867,3.6e-4 -0.356038,0.16049 -0.355268,0.35735 7.83e-4,0.19578 0.159488,0.35419 0.355268,0.35458 h 0.951088 v 3.1981 c -7.83e-4,0.19659 0.157979,0.35659 0.354573,0.35735 0.197681,7.2e-4 0.358128,-0.15967 0.357354,-0.35735 v -3.19813 h 0.948307 c 0.196593,7.1e-4 0.356587,-0.15797 0.357354,-0.35457 7.83e-4,-0.19768 -0.159673,-0.35813 -0.357354,-0.35736 z m -4.472483,0.0889 c -0.150454,0.12686 -0.169451,0.35168 -0.04241,0.50195 l 1.290367,1.52676 -1.290367,1.52674 c -0.127042,0.15028 -0.108044,0.37513 0.04241,0.50196 0.150281,0.127 0.375128,0.10803 0.501964,-0.0424 l 1.212499,-1.43427 1.212501,1.43427 c 0.126841,0.15045 0.351683,0.16945 0.501962,0.0424 0.150456,-0.12687 0.169452,-0.35168 0.04241,-0.50196 l -1.290367,-1.5267 1.290367,-1.52676 c 0.127044,-0.15027 0.108047,-0.37513 -0.04241,-0.50195 -0.150279,-0.12701 -0.375127,-0.10804 -0.501964,0.0424 l -1.212499,1.43429 -1.2125,-1.43429 c -0.172188,-0.18656 -0.359404,-0.15845 -0.501963,-0.0424 z"
            style="color:#bf7411;display:inline;fill:#bf7411;stroke-width:1;stroke-linecap:round;stroke-linejoin:round;-inkscape-stroke:none"
          />
        </g>
      </svg>

      <v-btn :title="$t('gc.at.btn.cancelSendAT')" class="mr-1 btnATSetIsLangSEND"
             color="white"
             fab
             @click="cancelSendAT">
          <span class="fa-layers fa-fw">
              <i class="fas fa-times" style="font-size: 16px; margin-right: 3px; margin-bottom: 4px;"></i>
          </span></v-btn>
    </div>

    <div id="idVacSvgButton5" :title="$t('gc.at.btn.textSendDis')"
         class="vac-svg-button vac-send-disabled btnATBar sendTextATd ml-1"
    >
      <v-progress-circular
        v-if="isSendTextAT"
        id="idProcBtnSendMessageATdisabled"
        :size="28"
        class="circularAT"
        color="#bf7411"
        indeterminate
      ></v-progress-circular>
      <svg v-if="!isSendTextAT" height="24" version="1.1" viewBox="0 0 24 24" width="24">
        <path d="M2,21L23,12L2,3V10L17,12L2,14V21Z" style="fill:#9ca6af"></path><!----></svg>
    </div>

    <div id="idDivAudioMicMess" class="divAudioMicMess">
      <div id="idVacBtnSendConvertBlob" :title="$t('gc.at.btn.audio1')" class="vac-svg-button vacBtnAudioMic">
        <v-icon class="iconVacBtnMic">mdi-message-check-outline</v-icon>
      </div>
      <div id="idVacBtnPauseRecordBlob" :title="$t('gc.at.btn.audio2')" class="vac-svg-button vacBtnAudioMic">
        <v-icon class="iconVacBtnMic">mdi-pause</v-icon>
      </div>
      <div id="idVacBtnPlayRecordBlob" :title="$t('gc.at.btn.audio3')" class="vac-svg-button vacBtnAudioMic">
        <v-icon class="iconVacBtnMic">mdi-play</v-icon>
      </div>
      <div id="idDivInfoSaveBlob" class="divInfoSaveBlob">
        <div id="idAudioGraphAT" class="atAudioGraph bodyAT"></div>
        <div id="idtextStreamMicSave" class="vac-dot-audio-record-time">
          {{ textStreamMicSave }}
        </div>
      </div>
      <div id="idVacBtnDeleteBlob" :title="$t('gc.at.btn.audio4')" class="vac-svg-button vacBtnAudioMicDel">
        <v-icon class="iconVacBtnMicD">mdi-delete</v-icon>
      </div>
    </div>
    <div id="divATrow" class="row" style="height: 44px; padding-right: 6px; margin-left: 6px; display: none"></div>
    <div id="idVacBtnDeleteBlobSearch" :title="$t('gc.at.btn.audio4')" class="vac-svg-button vacBtnBlobSearch">
      <v-icon class="iconVacBtnMicD">mdi-delete</v-icon>
    </div>
    <v-progress-circular
      id="idProcBlobSearch"
      :size="28"
      class="circularATSearch"
      color="rgb(64 64 64)"
      indeterminate
    ></v-progress-circular>

    <!--    <v-btn id="mybtn" color="transparent" text @click="btnClick" style="position: absolute;-->
    <!--top: 60px; left: 60px; width: 369px; height: 263px;"> Click Me-->
    <!--    </v-btn>-->
    <v-dialog v-model="dialog" v-if="dialog" fullscreen class="custom-dialog" id="viewer_dialog">
      <v-carousel style="background-color: rgba(0, 0, 0, 0.8)" height="100%" :show-arrows="false">
        <v-carousel-item
          reverse-transition="fade-transition"
          transition="fade-transition"
        >
          <v-toolbar
            density="compact"
            max-width="200"
            class="ml-auto"
            style="z-index: 5000; background-color: rgba(0, 0, 0, 0.3); right: 0px"
          >
            <v-spacer></v-spacer>
            <v-btn elevation="6" outlined fab dark small @click="sendFileWithMarks"
                   :title="$t('chat.send')" v-if="hasSvgChild">
              <v-icon left size="24" color="white">mdi-send</v-icon>
            </v-btn>
            <v-btn elevation="6" outlined fab dark small @click="dialog=false" :title="$t('close')">
              <v-icon left size="25" color="white">mdi-close</v-icon>
            </v-btn>
          </v-toolbar>
          <XrMediaImgViewer :src="src" :rw="true" marks-url="none" ref="imgViewer"
                            :show-eye=false :save-to-d-b="false" :other-color="false"></XrMediaImgViewer>
        </v-carousel-item>
      </v-carousel>
    </v-dialog>

    <!--   --------------------------------------------------------------------- -->



    <v-row id="showLoadTip"
           style="height: 32px;
          display: none;
          padding-top: 3px;
          padding-left: 2px;
          background: #0000000f;">

      <v-progress-circular :color="'primary'"
                           :size=24
                           indeterminate
                           style="margin-left: 8px"></v-progress-circular>
      <!--          <v-toolbar-->
      <!--            density="compact"-->
      <!--            class="ml-auto"-->
      <!--            style="z-index: 5000; right: 0px; top:0px; position: absolute;-->
      <!--          justify-content: center">-->
      <!--            <v-btn elevation="6"-->
      <!--                   density="compact"-->
      <!--                   max-width="12"-->
      <!--                   max-height="20"-->
      <!--                   class="mr-2"-->
      <!--                   fab-->
      <!--                   color="grey-lighten-1"-->
      <!--                   @click="showTip=false"-->
      <!--            style="top: 0px; right: 0px; position: absolute">-->
      <!--              <v-icon size="15" color="grey-darken-1">mdi-close</v-icon>-->
      <!--            </v-btn>-->
      <!--          </v-toolbar>-->
      <div style="font-family: Rooftop; font-size: 14px; padding-left: 8px; padding-top: 4px;">
        {{$t('gc.load.file.chat')}}
      </div>
    </v-row>

  </div>
</template>

<script>
import ChatWindow from 'vue-advanced-chat'
import 'vue-advanced-chat/dist/vue-advanced-chat.css'
import $ from "jquery";

import expertRU from '@/lang/expert-ru.json'
import expertEN from '@/lang/expert-en.json'

import XrChatMessage from "@/app/global/XrChatMessage";
import XrMediaImgViewer from "@/app/global/media/XrMediaImgViewer.vue";
import XrMediaViewer from "@/app/global/media/XrMediaViewer.vue";

import XrSpinner from "@/app/global/XrSpinner.vue";


var This
var AUDIO_STREAM
var noIdTranslateSET = new Set()

export default {
  name: "ChatMessageGC",
  components: {
    XrMediaViewer,
    // DialogAddOperLink,
    XrMediaImgViewer,
    XrChatMessage,
    ChatWindow,
    XrSpinner
  },
  mounted() {
    This = this
    this.$root.setLocaleModule(expertRU, expertEN)

    setTimeout(() => {
      This.addElTranslate()
    }, 1000)
  },
  props: {
    wsURL: '',
    senderId: '',
    isExternalUser: false
  },
  watch: {
    langSrc: {
      handler: function () {
        if (this.langSrc == this.langTgt) {
          this.langTgt = this.oldlangSrc
        }
        this.settUpdate()
      },
    },
    langTgt: {
      handler: function () {
        if (this.langSrc == this.langTgt) {
          this.langSrc = this.oldlangTgt
        }
        this.settUpdate()
      },
    },
    dialog: {
      handler: function (newValue) {
        //console.log(newValue + ' dialog change')
        if (newValue) {
          setTimeout(() => {
            this.watchSvgChild()
          }, 1000)
        }
      }
    },
  },
  data() {
    return {
      loadTipText: 'загрузка большого файла..',
      spinnerSize: 10,

      sheet: false,
      screenshots: [], //массив скриншотов
      openAllPhotosBool: false,
      itemNum: 0,
      showToolbar: false,
      inToolbar: false,
      deleteFromDB: false,
      sendTo: false,

      dialog: false, // открытие диалога
      src: null,
      blobFiles: [],
      hasSvgChild: false,

      name: '',

      roomId: '',
      rooms: [],
      messages: [],
      currentSenderID: '',
      currentSenderName: "",
      currentAvatar: '',
      messageActions: [],
      menuActions: [],
      // acceptedFiles: 'image/png, image/jpeg',
      acceptedFiles: '',
      textMessageLength: '',
      textMessages: {
        ROOMS_EMPTY: this.$t('gc.ROOMS_EMPTY'),
        ROOM_EMPTY: this.$t('gc.ROOM_EMPTY'),
        NEW_MESSAGES: this.$t('gc.NEW_MESSAGES'),
        MESSAGE_DELETED: this.$t('gc.MESSAGE_DELETED'),
        MESSAGES_EMPTY: this.$t('gc.MESSAGES_EMPTY'),
        CONVERSATION_STARTED: this.$t('gc.CONVERSATION_STARTED'),
        TYPE_MESSAGE: this.$t('gc.TYPE_MESSAGE'),
        SEARCH: this.$t('gc.SEARCH'),
        IS_ONLINE: this.$t('gc.IS_ONLINE'),
        LAST_SEEN: this.$t('gc.LAST_SEEN'),
        IS_TYPING: this.$t('gc.IS_TYPING')
      },
      isImgCloseBtn: false,
      isShowTranslate: false,// отображать или нет элементы перевода
      isTextTextArreaMessage: false, // есть ли текст в поле для сообщений
      isAudioSaveBlob: false, // едет ли запись аудио в блоб
      isStreamMicSave: false,// статус записи видео
      isPauseRecordMic: false, // стоп во время записи аудио
      isMouseBtnATSave: false, // интерфейс кнопки для записи видео
      textListTranslate: [],//text переведенного текста, пока временно
      mediaRecorder: null, // рекордер для записи видео
      voiceBlob: null, //блоб с сохраненным аудио
      textStreamMicSave: '00:20', //fext info save video
      isScrollDownloadAT: false, // скролл загрузки файла
      isEditMessage: false, // активирован ли режим редактирования
      userLang: 'RU',
      langSrc: 'RU', // родной язык
      langTgt: 'EN', // язык перевода
      langSelect: [],
      oldlangSrc: 'RU',
      oldlangTgt: "EN",
      spanTranslate: `<svg width="10" height="10" xmlns="http://www.w3.org/2000/svg">
         <g>
          <path stroke="#000" id="svg_2" d="m3.95033,9.74537c-0.24739,-0.18414 -0.21494,-0.29565 0.38655,-1.32788c0.30372,-0.52122 0.55222,-0.96282 0.55222,-0.98133c0,-0.01851 -0.83868,-0.03366 -1.86374,-0.03366l-1.86374,0l0,-0.9762l0,-0.9762l1.87851,0c1.44844,0 1.86234,-0.02941 1.80789,-0.12845c-0.03884,-0.07065 -0.30379,-0.53944 -0.58878,-1.04176c-0.5372,-0.94689 -0.53704,-1.20082 0.00083,-1.27953c0.26768,-0.03917 4.43506,2.97042 4.57831,3.30635c-1.38812,1.19944 -3.0474,2.45495 -4.57586,3.56196c-0.08059,0 -0.22108,-0.05549 -0.31219,-0.12331l0,0z" fill="#000000"/>
         </g>
        </svg>`,
      spanLang: {
        'RU': `<span style="height: 11px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-ru"></span>`,
        'RU_22': `<span style="height: 22px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-ru"></span>`,
        'EN': `<span style="height: 11px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-gb"></span>`,
        'EN_22': `<span style="height: 22px;    margin-top: 0px;    top: 0px;    margin-right: 2px;    margin-left: 2px;" class="flag-icon flag-icon-squared flag-icon-gb"></span>`,
      },
      isSendFileAudioTranslate: false, // статус, отправлять ли аудио на сервер
      isSetLangSetting: false,
      idSecondMessAT: '',// ID ПОСЛЕДНЕГО СООБЩЕНИЯ НА ПЕРЕВОД
      isSendBlob: true,// отправка аудио для распознования
      isEnterAT: false,// событе ентер когда только перевод
      isSendTextAT: false,//
      tTimestamp: '',
      title1: 't1',

      audiostartATs: new Audio('/mdb/audio/startAts.mp3'),
      audiostopNoSendATs: new Audio('/mdb/audio/stopNoSaveATs.mp3'),
      audiosecATs: new Audio('/mdb/audio/5secATs.mp3'),
      audiostopATs: new Audio('/mdb/audio/stopNoSaveATs.mp3'),
      audioPauseRecordMic: new Audio('/mdb/audio/pauseRecordMic.wav'),
      audioResumeRecordMic: new Audio('/mdb/audio/resumeRecordMic.wav'),
      // audiostartATs: new Audio(''),
      // audiostopNoSendATs: new Audio(''),
      // audiosecATs: new Audio(''),
      // audiostopATs: new Audio(''),
      // audioPauseRecordMic: new Audio(''),
      // audioResumeRecordMic: new Audio(''),
      isTranslate: false,
      urlRest: '',
      messageIdSend: -1,
      isSearchToText: false,
      pdfMap: {},
      pdfArray: [],//хранение base64 пдф, чтобы потом их можно было скачать
      f: false
    }
  },
  methods: {
    watchSvgChild() {
      const svgElement = document.querySelector('.viewer-canvas svg');
      //console.log(svgElement)

      var observer = new MutationObserver(function (mutations) {
        mutations.forEach(function (mutation) {
          if (mutation.type === "childList") {
            var numOfChildElements = svgElement.childElementCount;
            if (numOfChildElements === 0) {
              This.hasSvgChild = false;
            } else {
              This.hasSvgChild = true;
            }
          }
        });
      });
      var observerConfig = {childList: true, subtree: true};
      observer.observe(svgElement, observerConfig);
    },

    sendFileWithMarks() {
      let milliseconds = Date.now();
      // let currDate = new Date();
      // currDate.setSeconds(0);
      // let currFormattedDate = currDate.toLocaleString()
      // let parts = currFormattedDate.split(", ");
      // let time = parts[1].slice(0, -3);

      let newString = "_" + milliseconds;

      let svg = document.querySelector('.viewer-canvas svg');
      let fO = document.querySelectorAll('.viewer-canvas svg foreignObject');

      this.$refs.imgViewer.onRemoveCircles()

      for (let i = 0; i < fO.length; i++) {
        var textRect = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
        var g = document.createElementNS("http://www.w3.org/2000/svg", "g");

        var x = parseFloat(fO[i].getAttribute('x'));
        var y = parseFloat(fO[i].getAttribute('y'));
        var endX = parseFloat(fO[i].getAttribute('x')) + parseFloat(fO[i].getAttribute('width'))
        var width = parseFloat(getComputedStyle(fO[i]).width);
        var height = parseFloat(getComputedStyle(fO[i]).height);
        let color = fO[i].getAttribute('style').split(' ')[2].replace(';', '');
        let style = fO[i].children[0].getAttribute('style').split(';')[3]
        var lineWidth = width

        textRect.setAttribute('x', x + '%');
        textRect.setAttribute('y', y + '%');
        textRect.setAttribute('width', width + 5 + '');
        textRect.setAttribute('height', height + 5 + '');
        textRect.setAttribute('fill', color);

        var textTemp = document.createElementNS('http://www.w3.org/2000/svg', 'text');
        var text = document.createElementNS('http://www.w3.org/2000/svg', 'text');

        textTemp.setAttribute('x', x + '%');
        textTemp.setAttribute('y', y + '%');
        textTemp.setAttribute('style', style + ';')
        textTemp.style.visibility = 'hidden'
        textTemp.textContent = fO[i].children[0].innerText

        text.setAttribute('x', x + '%');
        text.setAttribute('y', y + '%');
        text.setAttribute('style', style + ';')

        fO[i].remove()
        g.appendChild(textRect)
        g.appendChild(text)
        svg.appendChild(g)

        svg.appendChild(textTemp)

        var words = fO[i].children[0].innerText.split(' ')
        var currContent = words[0]
        var tspanTemp = document.createElementNS('http://www.w3.org/2000/svg', 'tspan');
        tspanTemp.setAttribute('x', x + '%');
        tspanTemp.setAttribute('y', y + '%');
        tspanTemp.style.visibility = 'hidden'
        textTemp.appendChild(tspanTemp)

        let lineCount = 0;
        for (let j = 1; j < words.length; j++) {
          currContent = currContent + ' ' + words[j]
          //console.log('currContent в начале ' + j + ' итерации: ' + currContent)
          tspanTemp.textContent = currContent
          //console.log(tspanTemp.textContent + ' width ' + tspanTemp.getBBox().width)

          if (tspanTemp.getBBox().width <= lineWidth) {
            continue
          } else {
            var tspan = document.createElementNS('http://www.w3.org/2000/svg', 'tspan');

            const lastSpaceIndex = currContent.lastIndexOf(' '); // индекс последнего пробела
            currContent = currContent.substring(0, lastSpaceIndex); // отрезаем последнее слово
            tspan.textContent = currContent
            text.appendChild(tspan)

            let tspanWidth = tspan.getBBox().width * 100 / parseFloat(svg.style.width)
            let tspanHeight = tspan.getBBox().height;

            let startX = x + (endX - x - tspanWidth) / 2
            let dy = lineCount === 0 ? 1.2 * tspanHeight : tspanHeight

            tspan.setAttribute('x', startX + '%');
            tspan.setAttribute('dy', dy + '');

            currContent = words[j]
            //console.log(currContent + ' после создания tspan')
            //console.log(tspan)
            lineCount += 1
          }
        }
        textTemp.remove()
        var tspan = document.createElementNS('http://www.w3.org/2000/svg', 'tspan');
        tspan.textContent = currContent
        text.appendChild(tspan)

        let tspanWidth = tspan.getBBox().width * 100 / parseFloat(svg.style.width)
        let tspanHeight = tspan.getBBox().height;

        tspan.setAttribute('x', x + (endX - x - tspanWidth) / 2 + '%');
        tspan.setAttribute('dy', tspanHeight + '');

        //console.log(tspan)
      }

      const svgString = new XMLSerializer().serializeToString(svg);
      let svgBlob = new Blob([svgString], {type: 'image/svg+xml;charset=utf-8'});
      let svgFile = new File([svgBlob], 'marks.svg', {type: 'image/svg+xml;charset=utf-8'});

      //console.log(This.name, 'This.name')
      const formData = new FormData();
      formData.append('svgFile', svgFile);
      formData.append('roomId', This.roomId);
      formData.append('name', This.name);

      This.$root.restPost(`${This.urlRest}/exgc/mergingFiles/0`, formData)
        .then((res) => {
          //console.log("это строка после выполнения пост-запроса " + res.imageData)

          const imageData = Uint8Array.from(atob(res.imageData), c => c.charCodeAt(0));
          const mergedBlob = new Blob([imageData], {type: "image/png"});

          let file = {
            blob: mergedBlob,
            extension: 'png',
            localUrl: URL.createObjectURL(mergedBlob),
            preview: '',
            name: 'image_' + newString,
            size: mergedBlob.size,
            type: 'image/png'
          }
          This.blobFiles = []
          This.blobFiles.push(file)

          let message = {
            messageId: -1,
            roomId: This.roomId,
            indexId: This.currentSenderID + This.messages.length,
            content: '',
            contentSrc: '',
            senderId: This.currentSenderID,
            username: This.currentSenderName,
            avatar: This.currentAvatar,
            date: This.currentDate(),
            timestamp: This.currentTime(),
            system: false,
            saved: true,
            distributed: true,
            seen: true,
            deleted: false,
            disableActions: false,
            disableReactions: false,
            files: This.blobFiles,
            replyMessage: null,
            langSrc: '',
            langTgt: ''
          }
          This.sendMessage(message);
          This.messageIdSend = -1;

          This.dialog = false
          This.hasSvgChild = false
        })
    },

    formAndSendMessage(blob) {
      let milliseconds = Date.now();
      // let currDate = new Date();
      // currDate.setSeconds(0);
      // let currFormattedDate = currDate.toLocaleString()
      // let parts = currFormattedDate.split(", ");
      // let time = parts[1].slice(0, -3);

      let newString = "_" + milliseconds;

      let file = {
        blob: blob,
        extension: 'png',
        localUrl: URL.createObjectURL(blob),
        preview: '',
        name: 'image_' + newString,
        size: blob.size,
        type: 'image/png'
      }
      This.blobFiles = []
      This.blobFiles.push(file)

      let message = {
        messageId: -1,
        roomId: This.roomId,
        indexId: This.currentSenderID + This.messages.length,
        content: '',
        contentSrc: '',
        senderId: This.currentSenderID,
        username: This.currentSenderName,
        avatar: This.currentAvatar,
        date: This.currentDate(),
        timestamp: This.currentTime(),
        system: false,
        saved: true,
        distributed: true,
        seen: true,
        deleted: false,
        disableActions: false,
        disableReactions: false,
        files: This.blobFiles,
        replyMessage: null,
        langSrc: '',
        langTgt: ''
      }
      This.sendMessage(message);
      This.messageIdSend = -1;
    },

    addMeUserChat(roomId, senderId, senderName, avatar, url) {

      this.langSelect = [
        {text: this.$t('gc.at.ru'), value: 'RU'},
        {text: this.$t('gc.at.en'), value: 'EN'}
      ]

      this.roomId = roomId
      this.userLang = (this.$route.params.lang).toUpperCase()
      //console.log('this.userLang', this.userLang)

      This.urlRest = url.replace('wss://', '').replace('wss//', '').replace('ws://', '').replace('ws//', '')

      var hostname = window.location.host;
      //console.log("host "+hostname)
      if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
        This.urlRest = window.location.protocol+'//'+window.location.host
        //console.log("location.hostname "+hostname)
      }else if (location.host == 'localhost:8080') {
        This.urlRest = ''
      }else{
        This.urlRest = 'https://' + This.urlRest
      }

      var urlAvatar = This.urlRest + avatar

      //console.log('This.urlRest ', This.urlRest)
      //console.log('urlAvatar ', urlAvatar)

      this.textMessages = {
        ROOMS_EMPTY: this.$t('gc.ROOMS_EMPTY'),
        ROOM_EMPTY: this.$t('gc.ROOM_EMPTY'),
        NEW_MESSAGES: this.$t('gc.NEW_MESSAGES'),
        MESSAGE_DELETED: this.$t('gc.MESSAGE_DELETED'),
        MESSAGES_EMPTY: this.$t('gc.MESSAGES_EMPTY'),
        CONVERSATION_STARTED: this.$t('gc.CONVERSATION_STARTED'),
        TYPE_MESSAGE: this.$t('gc.TYPE_MESSAGE'),
        SEARCH: this.$t('gc.SEARCH'),
        IS_ONLINE: this.$t('gc.IS_ONLINE'),
        LAST_SEEN: this.$t('gc.LAST_SEEN'),
        IS_TYPING: this.$t('gc.IS_TYPING')
      }
      // пока не нужно
      // this.messageActions=[
      //   {
      //     name: 'addIsPlane',
      //     title: this.$t('gc.chat.menu.isplan'),
      //     onlyMe: true
      //   }
      // ]

      this.currentSenderID = senderId
      this.currentSenderName = senderName
      this.currentAvatar = urlAvatar

      this.rooms = [
        {
          roomId: 1,
          roomName: 'Room 1',
          avatar: this.currentAvatar,
          unreadCount: 4,
          index: 3,
          lastMessage: {
            content: 'Last message received',
            contentSrc: '',
            senderId: this.currentSenderID,
            username: this.currentSenderName,
            timestamp: '10:20',
            saved: true,
            distributed: false,
            seen: false,
            new: true
          },
          users: [
            {
              _id: this.currentSenderID,
              username: this.currentSenderName,
              avatar: '',
              status: {
                state: 'online',
                lastChanged: 'today, 14:30'
              }
            },
            {
              _id: this.currentSenderID + '1',
              username: this.currentSenderName + '1',
              avatar: '',
              status: {
                state: 'online',
                lastChanged: 'today, 14:30'
              }
            },
            {
              _id: this.currentSenderID + '2',
              username: this.currentSenderName + '2',
              avatar: '',
              status: {
                state: 'online',
                lastChanged: 'today, 14:30'
              }
            }
          ],
          typingUsers: [4321]
        }
      ]
    },

    // получение и отображение сообщения от другого пользователя
    addUserChat(senderId, senderName, avatar) {

      //console.log('addUserChat ' + senderId + ' ' + senderName)

      if (this.rooms.length == 0) {
        setTimeout(() => {

          var urlAvatar = This.urlRest + avatar

          var user = {
            _id: senderId,
            username: senderName,
            senderId: senderId,
            avatar: urlAvatar,
            status: {
              state: 'online',
              lastChanged: 'today, 14:30'
            }
          }
          this.rooms[0].users = [...this.rooms[0].users, user]
        }, 0)

      } else {
        var urlAvatar = This.urlRest + avatar

        var user = {
          _id: senderId,
          username: senderName,
          avatar: urlAvatar,
          status: {
            state: 'online',
            lastChanged: 'today, 14:30'
          }
        }
        this.rooms[0].users = [...this.rooms[0].users, user]
      }
    },

    randomInteger() {
      var min = 0;
      var max = 9999999;
      let rand = min + Math.random() * (max + 1 - min);
      return Math.floor(rand);
    },

    toggleRoomsList(e) {
      //console.log('toggleRoomsList', e)
    },
    roomInfo(e) {
      //console.log('roomInfo', e)
    },
    fetchMessages(e) {
      //console.log('fetchMessages', e)
    },
    editMessage(e) {
      //console.log('', e)
    },
    deleteMessage(e) {
      //console.log('editMessage', e)
    },
    openfile(e) {
      //console.log('openfile', e)
    },
    openusertag(e) {
      //console.log('openusertag', e)
    },
    openfailedmessage(e) {
      //console.log('openfailedmessage', e)
    },
    menuactionhandler(e) {
      //console.log('menuactionhandler', e)
    },
    messageselectionactionhandler(e) {
      //console.log('messageselectionactionhandler', e)
    },
    sendmessagereaction(e) {
      //console.log('sendmessagereaction', e)
    },
    typingmessage(e) {
      var textValue = e.message
      if (textValue != null) {
        if (textValue.replace(/ /g, '').length == 0) {
          This.isAudioTextMessBtn(true)

          $('#idVacBtnSaveBlobAT0').hide()
          This.isTextTextArreaMessage = false
          $('#idVacBtnSendMess').css('padding-left', '6px')
          This.textLengthAT(0, false)// нет текста
        } else {
          This.isAudioTextMessBtn(false)

          This.isTextTextArreaMessage = true
          $('#idVacBtnSendMess').css('padding-left', '4px')

          This.textLengthAT(textValue.length, true)// ввод текста
        }
      } else {
        This.isAudioTextMessBtn(true)

        $('#idVacBtnSaveBlobAT0').hide()
        This.isTextTextArreaMessage = false
        $('#idVacBtnSendMess').css('padding-left', '6px')
        This.textLengthAT(0, false)// нет текста
      }

      if (This.isShowTranslate && !This.isStreamMicSave) {
        if (This.isTextTextArreaMessage) {
          $('#idVacSvgButton5').hide()
          $('#idVacSvgButton4').show()
        } else {
          $('#idVacSvgButton5').show()
          $('#idVacSvgButton4').hide()
        }
      }
    },
    textareaactionhandler(e) {
      //console.log('textareaactionhandler', e)
    },

    messageActionHandler(e) {
      //console.log('messageActionHandler e', e)
      if (e.action.name == 'addIsPlane') {
        This.$emit('chatIsPlan', e.message._id, true)
      }
    },

    sendMessage(message) {
      //console.log('sendMessage isEnterAT ', message)

      if (!This.isEnterAT) {
        if (This.isShowTranslate && !This.isStreamMicSave) {
          $('#idVacSvgButton5').show()
          $('#idVacSvgButton4').hide()
        }

        if (message.files != null) {
          try {
            This.sendFileMessage(message)
          } catch (e) {
            console.error(e)
          }
        } else {

          let body = {
            messageId: message.messageId ? message.messageId : -1,
            roomId: This.roomId,
            indexId: This.currentSenderID + This.messages.length,
            content: message.content,
            contentSrc: '',
            senderId: This.currentSenderID,
            username: This.currentSenderName,
            avatar: This.currentAvatar,
            date: This.currentDate(),
            timestamp: This.currentTime(),
            system: false,
            saved: true,
            distributed: true,
            seen: true,
            deleted: false,
            disableActions: false,
            disableReactions: false,
            files: message.files,
            replyMessage: null,
            langSrc: '',
            langTgt: ''
          }
          //console.log('bbbody ', body)
          //console.log(`${This.urlRest}/exgc/sendChatMess`)
          //отправка сообщения
          This.$root.restPost(`${This.urlRest}/exgc/sendChatMess`, body)
            .then((res) => {
              //console.log('res sendChatMess отправка сообщения', res)
              var newMess = {
                _id: res.gcChatId,
                indexId: res.gcChatId,
                content: message.content,
                contentSrc: '',
                senderId: This.currentSenderID,
                username: This.currentSenderName,
                avatar: This.currentAvatar,
                date: This.currentDate(),
                timestamp: This.currentTime(),
                system: false,
                saved: true,
                distributed: true,
                seen: true,
                deleted: false,
                disableActions: false,
                disableReactions: false,
                files: message.files,
                replyMessage: null,
                langSrc: '',
                langTgt: '',
                plan: false
              }
              This.messages = [...this.messages, newMess] // отправка сообщения
              This.funcMessages(newMess._id, false, true, newMess)

              This.textLengthAT(0, false)// отправка сообщения в чате
              This.$emit('sendMessage', This.currentSenderID, newMess)

              this.newSelect()
            })
        }
      }
      // if(message.files == null){}else{}
    },

    bytesToSize(bytes) {
      var sizes = ['Bytes', 'KB', 'MB', 'GB', 'TB'];
      if (bytes == 0) return '0 Byte';
      var i = parseInt(Math.floor(Math.log(bytes) / Math.log(1024)));
      return Math.round(bytes / Math.pow(1024, i), 2) + ' ' + sizes[i];
    },

    // отображение новых сообщений
    newRecvMessage(newMess) {
      if (newMess.langSrc != '') {
        //console.log('newRecvMessage > ', newMess)
        var content = newMess.content
        newMess.contentTgt = content
        newMess.content = '...'
        //console.log('newRecvMessage < ', newMess)
        setTimeout(() => {
          $('#id_span_text' + newMess._id).text(newMess.contentTgt)
        }, 500)
      }

      //console.log(' отображение новых сообщений ', newMess)
      //console.log(' newMess.files ', newMess.files)

      This.pdfArray = []
      if(newMess.files != null){
        newMess.files.forEach(file => {
          //console.log('file.extension',file.extension)
          if (file.extension !== 'png' || file.extension !== 'jpg' || file.extension !== 'jpeg') {
            //console.log(' не рисунок ',file)
            This.pdfArray.push(decodeURIComponent(file.name), file.extension, file.url) //для скачивания пдф
          }
        });
        This.pdfMap[newMess._id] = This.pdfArray
      }


      this.messages = [...this.messages, newMess] // получение сообщения
      This.funcMessages(newMess._id, newMess.langSrc != '', false, newMess)
      this.newSelect()
      This.elMessageTranslate(newMess)
    },

    elMessageTranslate(parsedMessage) {
      ////console.log('elMessageTranslate',parsedMessage)
      setTimeout(() => {

        if (parsedMessage.langSrc != '') {
          This.parsedMess(parsedMessage)
        }
        // else if(parsedMessage.langSrc == 'EN' || parsedMessage.langSrc == 'RU'){
        // }else{
        // }
      }, 50)
    },

    parsedMess(parsedMessage) {
      //console.log('parsedMessage', parsedMessage)
      //console.log('_id ' + parsedMessage._id)
      setTimeout(() => {
        //console.log($('#' + parsedMessage._id))

        $('#' + parsedMessage._id).find('div.vac-message-box').find('div.vac-message-container').find('div.vac-message-card').css("cssText", "background: #f3b563 !important;")


        //console.log($('#' + parsedMessage._id).find('div.vac-message-box').find('div.vac-message-container').find('div.vac-format-message-wrapper'))
        //console.log($('#' + parsedMessage._id).find('div.vac-message-box').find('div.vac-message-container').find('div.vac-format-message-wrapper').find('span').find('span'))
        $('#' + parsedMessage._id).find('div.vac-message-box').find('div.vac-message-container').find('div.vac-format-message-wrapper')
          .find('span').find('span').attr("id", "id_span_text" + parsedMessage._id).css('marginRight', '20px');

        //console.log($('#arrows_svg_' + parsedMessage._id))

        $('#spanStatusTranslate_' + parsedMessage.senderId).hide()

        if (parsedMessage.langSrc == 'RU') {
          var timeSt = $('#' + parsedMessage._id)
            .find('div.vac-message-box')
            .find('div.vac-message-container')
            .find('div.vac-message-card')
            .find('div.vac-text-timestamp')
          var divLang = `<div  id="divLang_${parsedMessage._id}" style="width: 38px; height: 0px;"></div>`
          timeSt.prepend(divLang);
          $('#divLang_' + parsedMessage._id).prepend(This.spanLang['EN']);
          $('#divLang_' + parsedMessage._id).prepend(This.spanTranslate);
          $('#divLang_' + parsedMessage._id).prepend(This.spanLang['RU']);
        } else {
          var timeSt = $('#' + parsedMessage._id)
            .find('div.vac-message-box')
            .find('div.vac-message-container')
            .find('div.vac-message-card')
            .find('div.vac-text-timestamp')
          var divLang = `<div  id="divLang_${parsedMessage._id}" style="width: 38px; height: 0px;"></div>`
          timeSt.prepend(divLang);
          $('#divLang_' + parsedMessage._id).prepend(This.spanLang['RU']);
          $('#divLang_' + parsedMessage._id).prepend(This.spanTranslate);
          $('#divLang_' + parsedMessage._id).prepend(This.spanLang['EN']);
        }
      }, 10)
    },

    newSelect() {

      //console.log('newSelect')

      setTimeout(() => {

        document.getElementsByClassName('vac-message-image').forEach((item) => {

          if (item.classList.contains("eventDBL")) {
            ////console.log('_n +' + item)
          } else {
            item.classList.add("eventDBL")
            // var cl = true

            // item.addEventListener("click", function imgCl(event) {
            //   // if(cl){
            //   //   cl = false
            //   setTimeout(() => {
            //     ////console.log('click img')
            //     ////console.log('item')
            //     ////console.log(item)
            //
            //     var urlImg = item.style.backgroundImage
            //     ////console.log(urlImg)
            //     var l = urlImg.length - 7
            //     ////console.log(l)
            //     urlImg = urlImg.substr(5, l)
            //     ////console.log(urlImg)
            //     This.isImgCloseBtn = true
            //
            //     setTimeout(() => {
            //
            //       $('#participants').append($('#idImgChatFull'));
            //       $('#imgFullImgChat').attr("src", urlImg);
            //       $('#imgFullImgChat').attr("download", "chart.png");
            //       // const img = document.getElementById('imgFullImgChat')
            //       // img.onload = function(){
            //       //   const a = document.createElement("a");
            //       //   a.href = img.src
            //       //   a.download = "imageName.png"
            //       //   a.click();
            //       // }
            //
            //     }, 0)
            //
            //     ////console.log($('#imgFullImgChat'))
            //
            //
            //     // if(item.classList.contains("fullIMG")){
            //     //   item.classList.remove("fullIMG")
            //     //   This.isImgCloseBtn = false
            //     // }
            //     // else{
            //     //   document.getElementsByClassName('vac-message-image').forEach((item1) => {
            //     //     item1.classList.remove("fullIMG")
            //     //   });
            //     //   // document.getElementsByClassName('vac-message-image').remove("fullIMG")
            //     //   This.isImgCloseBtn = true
            //     //   item.classList.add("fullIMG")
            //     //   // var bb = document.getElementById('idbtncloseChatimg') ///$('#idbtncloseChatimg')
            //     //   // var b2 = $('#idbtncloseChatimg')
            //     //   //
            //     //   // ////console.log(bb)
            //     //   // item.append(bb)
            //     //   // ////console.log(b2)
            //     //   // item.append(b2)
            //     //
            //     // }
            //     // cl = true
            //   }, 100)
            //   // }
            // });
          }
        });
      }, 500)
    },

    currentDate() {
      return new Date().toLocaleString().slice(0, -14);
    },

    currentTime() {
      return new Date().toLocaleTimeString().slice(0, -3);
    },

    blobToBase64(blob) {
      var reader = new FileReader();
      reader.readAsDataURL(blob);
      reader.onloadend = function () {
        return reader.result;
      }
    },

    clickCloseChat() {
      $('#mainChatMessage').append($('#idImgChatFull'));
      this.$emit('clickCloseChat')
    },

    clickCloseChatimg() {
      $('#mainChatMessage').append($('#idImgChatFull'));
      This.isImgCloseBtn = false
      document.getElementsByClassName('vac-message-image').forEach((item1) => {
        item1.classList.remove("fullIMG")
      });
    },

    showChat() {
      console.log('showChat')
      $('#idTextAreaMess').focus()

      // let keyDownHandler = function(event) {
      //   event.preventDefault();
      // };
      //
      // var element = document.getElementById('idTextAreaMess');
      // element.addEventListener('keydown', keyDownHandler, {capture: false, once: false, passive: false});

      setTimeout(async () => {

        console.log('element', element)
        // element.addEventListener('keydown', handleKeyDown, false);
        // element.removeEventListener('keydown', handleKeyDown, false);

        // This.f =!This.f
        // if(This.f){
        //   element.addEventListener('keydown', function(event) {
        //     console.log('keydown clicked!');
        //   }, {useCapture: true, once: true, passive: true});
        // }else{
        //   element.removeEventListener('keydown', function(event) {
        //     console.log('keydown clicked!');
        //   }, {useCapture: true, once: true, passive: true});
        // }


        // element.removeEventListener('keydown', keyDownHandler, {capture: false, once: false, passive: false});
        //
        //
        // element.removeEventListener('keydown', function(event) {
        //   console.log('keydown clicked!');
        // }, {capture: false, once: false, passive: false});



        // var eventListeners = getEventListeners(element);
        // const eventListeners = await element.getEventListeners();
        // console.log(eventListeners);

        function handleKeyDown(event) {
          console.log('Key down event fired');
        }
      }, 4000 )

    },

    isAudioTextMessBtn(isAudio) {
      if (isAudio) {
        $('#idVacBtnAudioMess').show()
        $('#idVacBtnSendMess').hide()
      } else {
        $('#idVacBtnSendMess').show()
        $('#idVacBtnAudioMess').hide()
      }
    },

    //region_____________________TRANSLATE_____________________________
    addElTranslate() {

      var iatt = 0
      document.getElementsByClassName('vac-svg-button').forEach((item) => {
        switch (iatt) {
          case 0://!
            item.setAttribute("id", "idVacBtnFileInput");//!
            item.setAttribute("title", this.$t('gc.chatSendFile'));
            item.classList.add("btnChatBar")
            break;
          case 1://!
            item.setAttribute("id", "idVacBtnSendMess");//!
            item.setAttribute("title", this.$t('gc.chatSend'));
            setTimeout(() => {
              item.style.border = '1px solid rgb(25 118 210)';
              item.style.background = '#ffffff6b ';
              item.style.paddingLeft = '6px';
            }, 1000)
            break;
        }
        iatt++
      })

      setTimeout(() => {
        This.isAudioTextMessBtn(true)
      }, 500)

      $("#room-footer").bind("DOMSubtreeModified", function (e) {
        // //console.log('e',e)
        // //console.log('e',e.target)
        if (e.target._prevClass == 'vac-room-file-container') {
          This.isAudioTextMessBtn(false)
        }
      });
      // vac-room-files-container

      $('#idVacBtnSendMess').hide()
      $('#idVacBtnAudioMess').show()

      $('#room-footer').append($('#divATrow'))
        .append(This.createNewElements('vacDivisSearchToText'))

      $('#idPTextSearchToText').text(this.$t('gc.at.p.audiototext'),)

      $('#idDivisSearchToText').append($('#idVacBtnDeleteBlobSearch'))
      $('#idDivisSearchToText').append($('#idProcBlobSearch'))

      $('#idDivisSearchToText').hide()

      //console.log('localStorage[\'gc.langSrc\']', localStorage['gc.langSrc'])
      //console.log('localStorage[\'gc.langTgt\']', localStorage['gc.langTgt'])

      if (localStorage['gc.langSrc'] && localStorage['gc.langTgt']) {
        This.langSrc = localStorage['gc.langSrc']
        This.langTgt = localStorage['gc.langTgt']
      } else {
        //console.log('localStorage[\'gc.langTgt\'] == undefined')
        localStorage['gc.langSrc'] = 'RU'
        localStorage['gc.langTgt'] = 'EN'
        This.langSrc = 'RU'
        This.langTgt = 'EN'
        //console.log('This.langSrc = ' + This.langSrc)
      }

      document.getElementsByClassName('vac-box-footer vac-box-footer-border')[0].id = 'idVacBoxFooter';

      $('#idVacBoxFooter').append($('#idPLengthMessage'));
      $('#idVacBoxFooter').prepend(This.createNewElements('vacIconTextAreaLeft'));

      var textAreaMess = document.getElementsByClassName('vac-textarea')
      textAreaMess[0].id = 'idTextAreaMess';

      // textAreaMess[0].addEventListener('keydown', function (e) {
      //   if (e.keyCode === 13) {
      //     if (This.isEnterAT || This.isShowTranslate) {
      //       This.sendTextTranslateAT()
      //     }
      //     // можете делать все что угодно со значением текстового поля
      //     //console.log('ENTER');
      //   }
      // });

      setTimeout(() => {
        $('#idTextAreaMess').css('height', '20px') //18
        $('#idVacBoxFooter').css('padding', '8px 6px 4px 6px')
      }, 1500)

      $('#idTextAreaMess').css("cssText", "background: #ffffffc9 !important;")
        .css('min-height', '20px')
        .css('max-height', '200px')
        // .css('overflow-y', 'auto')
        .css('padding', '8px')


      //   <svg width="10" height="24" style="padding: 0; margin: 0!important;">
      //     <g>
      //     <path stroke="#bf7411" fill="#bf7411" transform="rotate(90.2262 5.24247 13.6409)" d="m-3.65223,16.1663l12.19272,0l-0.0051,-4.62721l-1.86896,0l3.73791,-2.10178l3.73791,2.10178l-1.86896,0l0,6.30535l-15.93063,0l0.0051,-1.67814z"/>
      //     <rect stroke="#bf7411" fill="#bf7411" height="0" width="0.12579" y="12.65409" x="3.49056" stroke-width="2"/>
      //     <path stroke="#bf7411" fill="#bf7411" stroke-width="2" d="m1.45455,1.63636l1.54545,0l0,18.54544l-1.54545,0l0,-18.54544z"/>
      //     <path stroke="#bf7411" fill="#bf7411" d="m0.90909,1l7.90908,0l0,3.63636l-7.90908,0l0,-3.63636z"/>
      //     </g>
      // </svg>


      // включить запись аудио для перевода в текст
      // var btnSaveAudioBlobAT = This.createNewElements('btnSaveAudioBlobAT')
      // var btnSaveAudioBlobATClear = This.createNewElements('btnSaveAudioBlobATClear')
      // var btnSaveAudioBlobATSave = This.createNewElements('btnSaveAudioBlobATSave')
      var btnSendMessageAT = This.createNewElements('btnSendMessageAT')
      var btnShowAT = This.createNewElements('btnShowAT')
      var btnSettingAT = This.createNewElements('btnSettingAT')
      var divRowSaveAudioAT = This.createNewElements('divRowSaveAudioAT')

      // $('#idVacIconTextareaLeft').prepend(This.createNewElements('vacBtnAudioMess'))
      $('#idVacIconTextareaLeft').prepend($('#idVacBtnFileInput'))
      $('.vac-icon-textarea').append(This.createNewElements('vacBtnAudioMess'))
      $('#idVacBoxFooter').append($('#idDivAudioMicMess')) // элементы записи аудио


      $('#divATrow').append($('#idRowSettingLang'))
        // .append(This.createNewElements('btnSaveAudioBlobAT'))
        // .append(This.createNewElements('btnSaveAudioBlobATClear'))
        .append(This.createNewElements('divRowSaveAudioAT'))
        // .append(This.createNewElements('btnSaveAudioBlobATSave'))
        .append(This.createNewElements('btnSendMessageAT'))
        .append($('#idVacSvgButton5'))
        .css('-webkit-box-align', 'end')
        .css('align-items', 'flex-end')
        .css('padding-bottom', '4px')

      // $('#idDivRowSaveAudioAT')
      //   .append(This.createNewElements('divAudioSaveInfo'))
      //   .append($('#idtextStreamMicSave'))

      This.elSelects()

      // $('#idVacIconTextareaLeft').prepend(This.createNewElements('btnCloseAT'))

      // $('#idVacSvgButton4langSrc').prepend(This.spanLang[This.langSrc]);
      // // $('#idVacSvgButton4').prepend(This.spanTranslate);
      // $('#idVacSvgButton4langTgt').prepend(This.spanLang[This.langTgt]);

      try {

        $('#idVacBtnDeleteBlobSearch').on('click', function () {
          This.clickSearchToTextClose();
        });
        $('#idVacBtnSendConvertBlob').on('click', function () {
          This.clickSendConvertBlob();
        });
        $('#idVacBtnPauseRecordBlob').on('click', function () {
          This.clickPausePlayRecordBlob(true);
        });
        $('#idVacBtnPlayRecordBlob').on('click', function () {
          This.clickPausePlayRecordBlob(false)
        });
        $('#idVacBtnAudioMess').on('click', function () {
          This.clickRecordAudioMic()
        });
        $('#idVacBtnDeleteBlob').on('click', function () {
          This.clickRecordDelete()
        });
        $('#idVacSvgButton2').on('click', function () {
          This.isSendBlob = false
          This.clickEditAudio(2) // отключить запись без сохранения блоба
        });
        $('#idVacSvgButton3').on('click', function () {
          This.isSendBlob = true
          This.clickEditAudio(3) // отключить запиьс с сохранением и отправкой блоба
        });
        $('#idVacSvgButton4').on('click', function () {
          This.sendTextTranslateAT()
        });
        // $('#idVacSvgButton9').on('click', function () {
        //   This.clickShowElTranslate()
        // });
        // $('#idVacBtnSaveBlobAT0').on('click', function () {
        //   // This.sendBlobServer()
        // });
        //$('#idVacSvgButton0').on('click', function () {
        //   This.clickShowElTranslate()
        // })
        // $('#idVacBtnSaveBlobAT').on('click', function () {
        //   This.clickEditAudio(1)
        // });
      } catch (e) {

      }


      $('#idSvgBtnSendMessageATdisabled').show()
      $('#idProcBtnSendMessageATdisabled').hide()

      $('#idVacBtnFileInput').show().css('height', '36px').css('width', '36px')
        .css('max-height', '100%')
        .css('border-radius', '4px')
        .css('border', '1px solid rgb(225, 228, 232)')
        .css('padding-top', '6px')
        .css('padding-left', '4px')

      $('#idVacBtnSendMess').show().css('height', '36px').css('width', '36px')
        .css('max-height', '100%')
        .css('border-radius', '4px')
        .css('border', '1px solid black')
        .css('padding-top', '6px')
        .css('padding-left', '4px')
        .css('margin-left', '4px')

//$('#idVacSvgButton8').hide() // открыть настройки языка
//       $('#idVacSvgButton9').hide() // закрыть функции перевода
      //$('#idVacSvgButton0').show() // вкл функции АТ
      // $('#idVacBtnSaveBlobAT').hide() // вкл запись аудио
      $('#idVacSvgButton2').hide() // удалить блоб аудио
      // $('#idAudioGraphAT').hide() //
      $('#idDivRowSaveAudioAT').hide()
      // $('#idtextStreamMicSave').hide() // удалить
      $('#idVacSvgButton3').hide() // сохранить блоб аудио
      $('#idVacSvgButton4').hide() // отправить сообщение для перевода дис
      $('#idVacSvgButton5').hide() // отправить сообщение для перевода

      $('#idVacBtnSendMess').hide()
      $('#idVacBtnAudioMess').show()
      $('#idVacBtnFileInput').show()
      $('#idTextAreaMess').show()
      $('#idDivAudioMicMess').hide()
    },

    setTranslation(isTranslate) {

      This.isTranslate = isTranslate
      if (isTranslate) {
        This.isShowTranslate = true
        This.clickShowElTranslate()
        This.elStopSaveAudioBlob()
        //$('#idVacSvgButton0').show()
      } else {
        This.isShowTranslate = false
        This.clickShowElTranslate()
        This.elStopSaveAudioBlob()
        //$('#idVacSvgButton0').hide()
      }
    },

    textLengthAT(length, isShow) {
      This.textMessageLength = `${length} (${500})`

      if (length < 500) {
        $('#idPLengthMessage').css('color', 'black')
      } else {
        $('#idPLengthMessage').css('color', 'red')
      }

      if (isShow && This.isShowTranslate) {
        $('#idPLengthMessage').css('display', 'flex')
      } else {
        $('#idPLengthMessage').css('display', 'none')
      }
    },

//включить и выключить функции AT
    clickShowElTranslate() {

      if (This.isShowTranslate) {
        This.textLengthAT($('#idTextAreaMess').val().length, true)// включить функции АТ
        $('#divATrow').css('display', 'flex')
        if(!$('#divATrow').hasClass("isFlexRightPanel")){
          $('#divATrow').removeClass('isHideRightPanel')
          $('#divATrow').addClass('isFlexRightPanel')
        }
        //$('#idVacSvgButton8').show()
        // $('#idVacSvgButton9').show()
        //$('#idVacSvgButton0').hide()
        // $('#idVacBtnSaveBlobAT').show()
        if (This.isTextTextArreaMessage) {
          $('#idVacSvgButton4').show()
          $('#idVacSvgButton5').hide()
        } else {
          $('#idVacSvgButton4').hide()
          $('#idVacSvgButton5').show()
        }
      } else {
        $('#divATrow').css('display', 'none')

        if(!$('#divATrow').hasClass("isHideRightPanel")){
          $('#divATrow').removeClass('isFlexRightPanel')
          $('#divATrow').addClass('isHideRightPanel')
        }

        This.textLengthAT(0, false)// выключить функции АТ
        This.isSetLangSetting = false
        //$('#idVacSvgButton8').hide() // открыть настройки языка
        // $('#idVacSvgButton9').hide() // закрыть функции перевода
        //$('#idVacSvgButton0').show() // вкл функции АТ
        // $('#idVacBtnSaveBlobAT').hide() // вкл запись аудио
        $('#idVacSvgButton2').hide() // удалить блоб аудио
        // $('#idAudioGraphAT').hide() //
        $('#idDivRowSaveAudioAT').hide()
        // $('#idtextStreamMicSave').hide() // удалить
        $('#idVacSvgButton3').hide() // сохранить блоб аудио
        $('#idVacSvgButton4').hide() // отправить сообщение для перевода дис
        $('#idVacSvgButton5').hide() // отправить сообщение для перевода


      }
    }
    ,

    setTimestamp() {
      const dateTime = new Date();
      var month = dateTime.getMonth() + 1 < 10 ? '0' + (dateTime.getMonth() + 1) : (dateTime.getMonth() + 1)
      var date = dateTime.getDate() < 10 ? '0' + dateTime.getDate() : dateTime.getDate()
      var hours = dateTime.getHours() < 10 ? '0' + dateTime.getHours() : dateTime.getHours()
      var minutes = dateTime.getMinutes() < 10 ? '0' + dateTime.getMinutes() : dateTime.getMinutes()
      var seconds = dateTime.getSeconds() < 10 ? '0' + dateTime.getSeconds() : dateTime.getSeconds()
      //console.log('setTimestamp ', dateTime)
      //console.log('Timestamp ' + dateTime.getFullYear() + month + date + hours + minutes + seconds)
      return '' + dateTime.getFullYear() + month + date + hours + minutes + seconds;
    }
    ,

// отправить текст сообщения на перевод
    sendTextTranslateAT() {

      if (This.isTranslate) {
        if ($('#idTextAreaMess').val().length < 500) {
          This.isSendTextAT = true

          var tTimestamp = This.setTimestamp()
          This.tTimestamp = tTimestamp

          setTimeout(() => {
            if (This.isSendTextAT && This.tTimestamp == tTimestamp) {
              This.isSendTextAT = false
            }
          }, 20000)

          This.textLengthAT(0, false)// отправить сообщение на перевод
          This.$emit('sendTextTranslate', $('#idTextAreaMess').val(), This.langSrc, This.langTgt, tTimestamp, This.messageIdSend)
          $('#idTextAreaMess').val('').focus()
          This.inputClickTextArea()

          if (This.isShowTranslate && !This.isStreamMicSave) {
            $('#idVacSvgButton5').show()
            $('#idVacSvgButton4').hide()
            $('#idVacBtnFileInput').show()

          }

          // setTimeout(() => {
          //   This.isEnterAT = false
          // }, 300)

          //console.log('sendTextTranslateAT')
        } else {
          alert('Превышен допустимый лимит символов для перевода')
        }
      }
    }
    ,

    restOneATmessage(parsedMessage) {
      //console.log('restOneATmessage', parsedMessage)
    }
    ,

    restATmessage(parsedMessage) {
      if (This.isTranslate) {
        This.isSendTextAT = false

        //console.log('restATmessage(parsedMessage) ', parsedMessage)
        //console.log('parsedMessage.data.result.details.resultTask ', parsedMessage.data.result.details.resultTask)

        if (parsedMessage.data.result.details.resultTask == 'Complete') {

          // {
          //   "data": {
          //   "whoami": "asrt_ws",
          //     "task": {
          //     "userInfo": {
          //       "senderId": "23464",
          //         "senderName": "Александр Кондратков",
          //         "deviceId": "-1",
          //         "deviceName": "Chrome 119.0.0.0 (Windows NT 10.0)"
          //     },
          //     "taskData": {
          //       "textSrcFull": "124 1243"
          //     },
          //     "messageId": 2346,
          //       "mode": "textNoCheck",
          //       "roomId": "1018042002",
          //       "timestamp": "20231117142415",
          //       "langSrc": "RU",
          //       "langTgt": "EN",
          //       "execTTS": "YES",
          //       "config": "[\"configOnlineYandex\",\"configOffline\"]"
          //   },
          //   "messageId": 2346,
          //     "result": {
          //     "finalText": "124 1243",
          //       "translationText": "124 1243",
          //       "mode": "textNoCheck",
          //       "details": {
          //       "userInfo": {
          //         "senderId": "23464",
          //           "senderName": "Александр Кондратков",
          //           "deviceId": "-1",
          //           "deviceName": "Chrome 119.0.0.0 (Windows NT 10.0)"
          //       },
          //       "taskData": {
          //         "textSrcFull": "124 1243"
          //       },
          //       "roomId": "1018042002",
          //         "timestamp": "20231117142415964",
          //         "langSrc": "RU",
          //         "langTgt": "EN",
          //         "mode": "textNoCheck",
          //         "execTTS": "YES",
          //         "audioSrc": {
          //         "path": ""
          //       },
          //       "audioTgt": {
          //         "path": "/mnt/data/iksar-asrt/asrt-server/./../data/processed/1018042002/20231117142415964/.RU.EN.wav"
          //       },
          //       "textSrc": {
          //         "path": "/mnt/data/iksar-asrt/asrt-server/./../data/processed/1018042002/20231117142415964/.RU",
          //           "result": {
          //           "1": {
          //             "sentence": "124 1243",
          //               "start": 0,
          //               "end": 0,
          //               "prob": 1
          //           }
          //         }
          //       },
          //       "textTgt": {
          //         "1": {
          //           "sentence": "124 1243",
          //             "start": 0,
          //             "end": 0,
          //             "prob": 1
          //         }
          //       },
          //       "textSrcFull": "124 1243",
          //         "textTgtFull": "124 1243",
          //         "configParam": {
          //         "ASR": "",
          //           "Punctuation": "NOTHING",
          //           "Translation": "ONLINE-YANDEX",
          //           "TTS": "ONLINE-YANDEX"
          //       },
          //       "dataSize": {
          //         "input": {
          //           "char": "8",
          //             "sec": "",
          //             "byte": ""
          //         },
          //         "output": {
          //           "char": "8",
          //             "sec": "",
          //             "byte": ""
          //         }
          //       },
          //       "resultMode": "Final",
          //         "resultTask": "Complete",
          //         "resultTaskPartNo": "",
          //         "resultTaskTotalParts": "",
          //         "resultCode": 200,
          //         "resultMessage": {
          //         "code": 200,
          //           "ru": "Всё в порядке",
          //           "en": "OK"
          //       },
          //       "messageId": 2346
          //     },
          //     "resultCode": {
          //       "code": 200,
          //         "ru": "Всё в порядке",
          //         "en": "OK"
          //     }
          //   },
          //   "punctuationResult": {
          //     "mode": "NOTHING"
          //   },
          //   "translationResult": {
          //     "data": {
          //       "whoami": "transl_ws",
          //         "task": {
          //         "recogResList": [
          //           [
          //             "124 1243",
          //             0,
          //             0,
          //             1
          //           ]
          //         ],
          //           "langSrc": "RU",
          //           "langTgt": "EN",
          //           "mode": "ONLINE-YANDEX"
          //       },
          //       "messageId": 2346,
          //         "result": {
          //         "textTgtRes": [
          //           [
          //             "124 1243 ",
          //             0,
          //             0,
          //             1
          //           ]
          //         ],
          //           "text": "124 1243",
          //           "mode": "ONLINE-YANDEX",
          //           "resultCode": {
          //           "code": 200,
          //             "ru": "Всё в порядке",
          //             "en": "OK"
          //         }
          //       },
          //       "stat": {
          //         "start": {
          //           "time": 1700220256.026958,
          //             "time_formatted": "17.11.2023 14:24:16.026960"
          //         },
          //         "end": {
          //           "time": 1700220256.269281,
          //             "time_formatted": "17.11.2023 14:24:16.269284"
          //         },
          //         "diff": {
          //           "time": {
          //             "spent": 242.3229217529297,
          //               "unit": "ms"
          //           }
          //         }
          //       }
          //     },
          //     "errorCode": 0,
          //       "ok": true
          //   },
          //   "generationResult": {
          //     "data": {
          //       "whoami": "tts_ws",
          //         "task": {
          //         "textFilePath": "/mnt/data/iksar-asrt/asrt-server/./../data/in_process/1018042002/20231117142415964/no.wav.RU.EN",
          //           "lang": "EN",
          //           "mode": "ONLINE-YANDEX"
          //       },
          //       "messageId": 2346,
          //         "mode": "ONLINE-YANDEX",
          //         "result": {
          //         "text": {
          //           "whole": {
          //             "fileName": "/mnt/data/iksar-asrt/asrt-server/./../data/in_process/1018042002/20231117142415964/no.wav.RU.EN.wav",
          //               "partNo": 1,
          //               "totalParts": 1,
          //               "isFinal": "YES",
          //               "text": "124 1243",
          //               "resultCode": {
          //               "code": 200,
          //                 "ru": "Всё в порядке",
          //                 "en": "OK"
          //             }
          //           }
          //         },
          //         "mode": "ONLINE-YANDEX"
          //       },
          //       "stat": {
          //         "start": {
          //           "time": 1700220256.2733102,
          //             "time_formatted": "17.11.2023 14:24:16.273313"
          //         },
          //         "end": {
          //           "time": 1700220257.3317702,
          //             "time_formatted": "17.11.2023 14:24:17.331774"
          //         },
          //         "diff": {
          //           "time": {
          //             "spent": 1058.459997177124,
          //               "unit": "ms"
          //           }
          //         }
          //       }
          //     },
          //     "errorCode": 0,
          //       "ok": true
          //   },
          //   "stat": {
          //     "start": {
          //       "time": 1700220256.0245657,
          //         "time_formatted": "17.11.2023 14:24:16.024570"
          //     },
          //     "end": {
          //       "time": 1700220257.3372688,
          //         "time_formatted": "17.11.2023 14:24:17.337271"
          //     },
          //     "diff": {
          //       "time": {
          //         "spent": 1312.7031326293945,
          //           "unit": "ms"
          //       }
          //     }
          //   }
          // },
          //   "errorCode": 0,
          //   "ok": true,
          //   "id": "asrtTranlsateResult",
          //   "senderName": "Александр Кондратков",
          //   "avatar": "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"
          // }

          //console.log('SEND ??? parsedMessage.data.result.details.resultTask', parsedMessage.data.result.details.resultTask)
          //console.log('SEND ??? parsedMessage.data.task.roomId', parsedMessage.data.task.roomId)
          //console.log('SEND ??? parsedMessage.data.task.roomId', parsedMessage.data.task.roomId)

          //console.log('SEND ??? parsedMessage.data.messageId', parsedMessage.data.messageId)
          //console.log('SEND ??? This.messageIdSend', This.messageIdSend)

          if (This.messageIdSend == -1) {
            let body = {
              messageId: parsedMessage.data.messageId ? parsedMessage.data.messageId : -1,
              roomId: This.roomId,
              indexId: parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp,
              content: parsedMessage.data.result.finalText,
              contentSrc: parsedMessage.data.result.details.textSrcFull,
              senderId: This.currentSenderID,
              username: This.currentSenderName,
              avatar: This.currentAvatar,
              date: This.currentDate(),
              timestamp: This.currentTime(),
              system: false,
              saved: true,
              distributed: true,
              seen: true,
              deleted: false,
              disableActions: false,
              disableReactions: false,
              files: null,
              replyMessage: null,
              langSrc: parsedMessage.data.task.langSrc,
              langTgt: parsedMessage.data.task.langTgt
            }
            //console.log('restPost sendChatMess body', body)
            //restATmessage отправка сообщения перевода
            This.$root.restPost(`${This.urlRest}/exgc/sendChatMess`, body)
              .then((res) => {
                //console.log('res sendChatMess отправка сообщения перевода', res)
                var newMess = {
                  _id: res.gcChatId,
                  indexId: res.gcChatId,
                  content: parsedMessage.data.result.finalText,
                  contentSrc: parsedMessage.data.result.details.textSrcFull,
                  senderId: This.currentSenderID,
                  username: This.currentSenderName,
                  avatar: This.currentAvatar,
                  date: This.currentDate(),
                  timestamp: This.currentTime(),
                  system: false,
                  saved: true,
                  distributed: true,
                  seen: true,
                  deleted: false,
                  disableActions: false,
                  disableReactions: false,
                  files: null,
                  replyMessage: null,
                  langSrc: parsedMessage.data.task.langSrc,
                  langTgt: parsedMessage.data.task.langTgt,
                  plan: false
                }
                This.$emit('sendMessage', This.currentSenderID, newMess)

                var content = newMess.content
                newMess.contentTgt = content
                newMess.content = '...'

                //обход ошибки с ссылкой в сообщении
                This.messages = [...this.messages, newMess] //сообщения перевода -1
                This.funcMessages(newMess._id, newMess.langSrc != '', true, newMess)
                This.elMessageTranslate(newMess)
                This.textLengthAT(0, false)// отправка сообщения в чате с переводом

                setTimeout(() => {
                  $('#id_span_text' + newMess._id).text(newMess.contentTgt)
                  console.log('сообщения перевода -1 newMess ', newMess)
                }, 500)

                setTimeout(() => {

                  $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp).find('div.vac-message-box.vac-offset-current').find('div.vac-message-container').find('div.vac-message-card.vac-message-current').css("cssText", "background: #f3b563 !important;")
                  if (parsedMessage.data.task.langSrc == 'RU') {
                    var timeSt = $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp)
                      .find('div.vac-message-box.vac-offset-current')
                      .find('div.vac-message-container')
                      .find('div.vac-message-card.vac-message-current')
                      .find('div.vac-text-timestamp')
                    // var divLang = `<div  id="divLang_${parsedMessage._id}" style="width: 0px"></div>`
                    // timeSt.prepend(divLang);
                    // divLang.prepend(This.spanEn);
                    // divLang.prepend(This.spanTranslate);
                    // divLang.prepend(This.spanRu);
                  } else {
                    var timeSt = $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp)
                      .find('div.vac-message-box.vac-offset-current')
                      .find('div.vac-message-container')
                      .find('div.vac-message-card.vac-message-current')
                      .find('div.vac-text-timestamp')
                    // var divLang = `<div id="divLang_${parsedMessage._id}" style="width: 0px"></div>`
                    // timeSt.prepend(divLang);
                    // divLang.prepend(This.spanRu);
                    // divLang.prepend(This.spanTranslate);
                    // divLang.prepend(This.spanEn);

                  }
                }, 100)

                this.newSelect()
              })
          } else {
            let body = {
              messageId: This.messageIdSend,
              roomId: This.roomId,
              indexId: parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp,
              content: parsedMessage.data.result.finalText,
              contentSrc: parsedMessage.data.result.details.textSrcFull,
              senderId: This.currentSenderID,
              username: This.currentSenderName,
              avatar: This.currentAvatar,
              date: This.currentDate(),
              timestamp: This.currentTime(),
              system: false,
              saved: true,
              distributed: true,
              seen: true,
              deleted: false,
              disableActions: false,
              disableReactions: false,
              files: null,
              replyMessage: null,
              langSrc: parsedMessage.data.task.langSrc,
              langTgt: parsedMessage.data.task.langTgt
            }
            //restATmessage отправка сообщения перевода
            This.$root.restPost(`${This.urlRest}/exgc/sendChatMess`, body)
              .then((res) => {
                //console.log('res sendChatMess отправка сообщения перевода', res)
                var newMess = {
                  _id: res.gcChatId,
                  indexId: res.gcChatId,
                  content: parsedMessage.data.result.finalText,
                  contentSrc: parsedMessage.data.result.details.textSrcFull,
                  senderId: This.currentSenderID,
                  username: This.currentSenderName,
                  avatar: This.currentAvatar,
                  date: This.currentDate(),
                  timestamp: This.currentTime(),
                  system: false,
                  saved: true,
                  distributed: true,
                  seen: true,
                  deleted: false,
                  disableActions: false,
                  disableReactions: false,
                  files: null,
                  replyMessage: null,
                  langSrc: parsedMessage.data.task.langSrc,
                  langTgt: parsedMessage.data.task.langTgt,
                  plan: false
                }


                This.$emit('sendMessage', This.currentSenderID, newMess)

                var content = newMess.content
                newMess.contentTgt = content
                newMess.content = '...'

                This.messageIdSend = -1
                This.messages = [...this.messages, newMess] //сообщения перевода 1
                This.funcMessages(newMess._id, newMess.langSrc != '', true, newMess)
                This.elMessageTranslate(newMess)
                This.textLengthAT(0, false)// отправка сообщения в чате с переводом

                setTimeout(() => {
                  $('#id_span_text' + newMess._id).text(newMess.contentTgt)
                  //console.log('сообщения перевода 1 newMess ', newMess)
                }, 500)

                setTimeout(() => {

                  $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp).find('div.vac-message-box.vac-offset-current').find('div.vac-message-container').find('div.vac-message-card.vac-message-current').css("cssText", "background: #f3b563 !important;")
                  if (parsedMessage.data.task.langSrc == 'RU') {
                    var timeSt = $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp)
                      .find('div.vac-message-box.vac-offset-current')
                      .find('div.vac-message-container')
                      .find('div.vac-message-card.vac-message-current')
                      .find('div.vac-text-timestamp')
                    // var divLang = `<div  id="divLang_${parsedMessage._id}" style="width: 0px"></div>`
                    // timeSt.prepend(divLang);
                    // divLang.prepend(This.spanEn);
                    // divLang.prepend(This.spanTranslate);
                    // divLang.prepend(This.spanRu);
                  } else {
                    var timeSt = $('#' + parsedMessage.data.task.roomId + parsedMessage.data.task.userInfo.senderId + parsedMessage.data.task.timestamp)
                      .find('div.vac-message-box.vac-offset-current')
                      .find('div.vac-message-container')
                      .find('div.vac-message-card.vac-message-current')
                      .find('div.vac-text-timestamp')
                    // var divLang = `<div id="divLang_${parsedMessage._id}" style="width: 0px"></div>`
                    // timeSt.prepend(divLang);
                    // divLang.prepend(This.spanRu);
                    // divLang.prepend(This.spanTranslate);
                    // divLang.prepend(This.spanEn);

                  }
                }, 100)

                this.newSelect()

                This.messageIdSend = -1;
              })
          }
        }
        else {
          //console.log('parsedMessage.data.result.details.resultTask', parsedMessage.data.result.details.resultTask)
        }


      }
    }
    ,

    clickIsEnRu() {
      if (This.isStreamMicSave) {
        This.$root.showInfo('Нельзя поменять язык пока идет запись!')
      } else {

      }
    }
    ,
    errorAudiotoText() {
      $('#idDivisSearchToText').hide()
      This.isSendTextAT = false
    },

    restTranlsateAudioVosk(parsedMessage) {

      //console.log('restTranlsateAudioVosk messageId' + parsedMessage.data.messageId)
      This.messageIdSend = parsedMessage.data.messageId ? parsedMessage.data.messageId : -1
      //console.log('restTranlsateAudioVosk messageIdSend' + This.messageIdSend)

      $('#idDivisSearchToText').hide()

      if (!noIdTranslateSET.has(parsedMessage.roomId + parsedMessage.senderId + parsedMessage.timestamp)) {
        // $('#idVacBtnSaveBlobAT0').show()

        // This.isEnterAT = true

        $('#idTextAreaMess').val('' + parsedMessage.textSrcFull).focus();
        This.isSendFileAudioTranslate = false
        //$('#idVacSvgButton8').show() // открыть настройки языка
        // $('#idVacSvgButton9').show() // закрыть функции перевода

        This.inputClickTextArea()

        // $('#idVacBtnFileInput').hide() // отправить сообщение
        // $('#idVacBtnSendMess').hide() // отправить сообщение


        if (This.isShowTranslate && !This.isStreamMicSave) {
          $('#idVacSvgButton5').hide()
          $('#idVacSvgButton4').show()
        }
      } else {
        //console.log('restTranlsateAudioVosk этот id был отменен ' + parsedMessage.RoomId + parsedMessage.SenderId + parsedMessage.Timestamp)
      }
    }
    ,

    inputClickTextArea() {
      const input = document.getElementById('idTextAreaMess');
      // setTimeout(() => input.dispatchEvent(eventFactory('keydown')), 10)
      setTimeout(() => input.dispatchEvent(eventFactory('beforeinput')), 20)
      setTimeout(() => input.dispatchEvent(eventFactory('keypress')), 30)
      setTimeout(() => input.dispatchEvent(eventFactory('input')), 40)
      setTimeout(() => input.dispatchEvent(eventFactory('change')), 50)
      setTimeout(() => input.dispatchEvent(eventFactory('keyup')), 60)

      function eventFactory(eventName) {
        return new KeyboardEvent(eventName, {
          key: "Space",
          keyCode: 40, // example values.
          code: "Space", // put everything you need in this object.
          which: 40,
          shiftKey: false, // you don't need to include values
          ctrlKey: false,  // if you aren't going to use them.
          metaKey: false   // these are here for example's sake.
        });
      }
    }
    ,

    startSetting() {
      // This.$refs.dialogSettCallAT.show()
      This.isSetLangSetting = true
    }
    ,

    settUpdate() {
      this.oldlangTgt = this.langTgt
      this.oldlangSrc = this.langSrc

      localStorage['gc.langSrc'] = This.langSrc
      localStorage['gc.langTgt'] = This.langTgt

      //console.log('settUpdate ' + this.langSrc)
      // $('#idbar-line__right-text1').empty();
      // $('#idbar-line__right-text1').append(This.spanLang[This.langSrc]);
      // $('#idbar-line__right-text2').empty();
      // $('#idbar-line__right-text2').append(This.spanLang[This.langTgt]);
    }
    ,

    setIsLang() {
      var ol = this.langSrc
      this.langSrc = this.langTgt
      this.langTgt = ol
    }
    ,

    cancelSendAT() {
      This.$emit('soundRecAT', false, This.langSrc, This.langTgt)
      This.isSendFileAudioTranslate = !This.isSendFileAudioTranslate
      $('#idVacSvgButton5').show()
      $('#idVacSvgButton4').hide()
      $('#idVacBtnFileInput').show()

      //$('#idVacSvgButton8').show()
      // $('#idVacSvgButton9').show()
      noIdTranslateSET.add(This.idSecondMessAT)
      //console.log('noIdTranslateSET', noIdTranslateSET)
    }
    ,
//endregion

    //region_____________________RECORD AUDIO AND TEXT_____________________________
    //region click
    clickRecordAudioMic() {
      $('#idVacBtnSendMess').hide()
      $('#idVacBtnAudioMess').hide()
      $('#idVacBtnFileInput').hide()
      $('#idTextAreaMess').hide()
      $('#idVacBtnPauseRecordBlob').show()
      $('#idVacBtnPlayRecordBlob').hide()
      $('#idDivAudioMicMess').show()

      This.recordAudioMic();
    },

    clickRecordDelete() {
      $('#idVacBtnSendMess').hide()
      $('#idVacBtnAudioMess').show()
      $('#idVacBtnFileInput').show()
      $('#idTextAreaMess').show()
      $('#idDivAudioMicMess').hide()

      This.isPauseRecordMic = false
      This.stopSaveAudioSend(false)
      This.messageIdSend = -1
    },

    clickSendConvertBlob() {
      $('#idVacBtnSendMess').hide()
      $('#idVacBtnAudioMess').show()
      $('#idVacBtnFileInput').show()
      $('#idTextAreaMess').show()
      $('#idDivAudioMicMess').hide()

      This.stopSaveAudioSend(true)
    },

    clickSearchToTextClose() {
      $('#idDivisSearchToText').hide()
      This.clickRecordDelete()
    },

    clickPausePlayRecordBlob(isPause) {
      if (isPause) {
        This.audioPauseRecordMic.play()
        $('#idVacBtnPauseRecordBlob').hide()
        $('#idVacBtnPlayRecordBlob').show()
        This.isPauseRecordMic = true
        This.mediaRecorder.pause();
        //console.log("recording paused");
      } else {
        This.audioResumeRecordMic.play()
        $('#idVacBtnPauseRecordBlob').show()
        $('#idVacBtnPlayRecordBlob').hide()
        This.isPauseRecordMic = false
        This.mediaRecorder.resume();
        //console.log("recording resume()");
      }
    },
    //endregion

    //region audio blob

    recordAudioMic() {
      This.isStreamMicSave = true
      This.isPauseRecordMic = false
      This.elStartSaveAudioBlob()
      navigator.mediaDevices.getUserMedia({audio: true})
        .then(stream => {
          AUDIO_STREAM = stream
          This.mediaRecorder = new MediaRecorder(AUDIO_STREAM);

          This.audioBarGraph()

          let voice = [];
          This.mediaRecorder.start()
          var tik = 2100
          setTimeout(function run() {

            if (!This.isPauseRecordMic) {
              tik--
              if (!This.isStreamMicSave) {
                tik = 0
              } else if (tik <= 0) {
                //console.log("tik <= 0")
                $('#idVacBtnSendMess').hide()
                $('#idVacBtnAudioMess').show()
                $('#idVacBtnFileInput').show()
                $('#idTextAreaMess').show()
                $('#idDivAudioMicMess').hide()
                This.stopSaveAudioSend(true)
              } else {
                if (tik > 2000) {
                  This.textStreamMicSave = '00:20'
                } else if (tik < 1000) {
                  This.textStreamMicSave = '00:0' + ((tik / 100) | 0)
                  if (tik == 600) {
                    This.audiostartATs.play()
                  }
                } else {
                  This.textStreamMicSave = '00:' + ((tik / 100) | 0)

                }
                ////console.log(This.textStreamMicSave)

                setTimeout(run, 10);
              }
            } else {
              setTimeout(run, 10);
            }


          }, 10);

          This.mediaRecorder.addEventListener("dataavailable", function (event) {
            voice.push(event.data);
          });

          This.mediaRecorder.addEventListener("stop", function () {
            //console.log("stop")
            This.voiceBlob = new Blob(voice, {
              type: 'audio/wav'
            });
            if (This.isSendBlob) {
              This.sendBlobServer()
            }
            This.isSendBlob = true
          });

        });

    },

    stopSaveAudioSend(isSend) {
      //console.log('stopSaveAudioSend', isSend)
      This.isSendBlob = isSend
      if (isSend) {
        This.audiostopATs.play()
      } else {
        This.audiostopNoSendATs.play()
        This.$emit('stopNoTranslate', This.tTimestamp)
      }
      //This.elStopSaveAudioBlob()
      //This.$emit('soundRecAT', false, This.langSrc, This.langTgt)
      This.isStreamMicSave = false
      This.textStreamMicSave = '00:20'
      This.isScrollDownloadAT = true
      try {
        This.mediaRecorder.stop()
      } catch (e) {
      }
    }
    ,

    // clickEditAudio(type) {
    //   This.isStreamMicSave = !This.isStreamMicSave
    //   This.isScrollDownloadAT = false
    //
    //   if (This.isStreamMicSave) {
    //     This.elStartSaveAudioBlob()
    //
    //     navigator.mediaDevices.getUserMedia({audio: true})
    //       .then(stream => {
    //         AUDIO_STREAM = stream
    //         This.mediaRecorder = new MediaRecorder(AUDIO_STREAM);
    //
    //         This.audioBarGraph()
    //
    //         let voice = [];
    //         This.mediaRecorder.start()
    //         var tik = 20
    //         setTimeout(function run() {
    //           tik--
    //           if (!This.isStreamMicSave) {
    //             tik = 0
    //           } else if (tik <= 0) {
    //             This.stopSaveAudioSend(true)
    //           } else {
    //             if (tik < 10) {
    //               This.textStreamMicSave = '00:0' + tik
    //               if (tik == 5) {
    //                 This.audiostartATs.play()
    //               }
    //             } else {
    //               This.textStreamMicSave = '00:' + tik
    //
    //             }
    //             ////console.log(This.textStreamMicSave)
    //
    //             setTimeout(run, 1000);
    //           }
    //
    //         }, 1000);
    //
    //         This.mediaRecorder.addEventListener("dataavailable", function (event) {
    //           voice.push(event.data);
    //         });
    //
    //         This.mediaRecorder.addEventListener("stop", function () {
    //           This.voiceBlob = new Blob(voice, {
    //             type: 'audio/wav'
    //           });
    //           if (This.isSendBlob) {
    //             This.sendBlobServer()//2
    //           }
    //           This.isSendBlob = true
    //         });
    //
    //       });
    //   } else {
    //     This.$emit('soundRecAT', false, This.langSrc, This.langTgt)
    //     if (This.mediaRecorder) {
    //       if (type == 3) {
    //
    //         This.stopSaveAudioSend(true)
    //       } else {
    //
    //         This.stopSaveAudioSend(false)
    //         //$('#idVacSvgButton8').show() // открыть настройки языка
    //         // $('#idVacSvgButton9').show() // закрыть функции перевода
    //       }
    //     }
    //   }
    // }
    // ,

    audioBarGraph() {
      /*СОЗДАЁМ ОСНОВНЫЕ ПЕРЕМЕННЫЕ*/
      var bodyAT, num, array, width, context, logoAT, myElements, analyser, src, height;

      /*ЗАПИСЫВАЕМ В ПЕРЕМЕННУЮ bodyAT ЭЛЕМЕНТ bodyAT*/
      bodyAT = document.getElementById('idAudioGraphAT');

      /*УКАЗЫВАЕМ КОЛИЧЕСТВО СТОЛБЦОВ НА ИНДИКАТОРЕ*/
      num = 29;

      /*СОЗДАЕМ МАССИВ*/
      array = new Uint8Array(num * 2);

      /*УКАЗЫВАЕМ ДЛИНУ СТОЛБИКОВ В PX*/
      width = 7;


      if (context) return;

      /*ПРОПИСЫВАЕМ ЦИКЛ ВНУТРИ КОТОРОГО БУДЕМ СОЗДАВАТЬ ЭЛЕМЕНТЫ НАШИХ СТОЛБИКОВ*/
      for (var i = 0; i < num; i++) {
        logoAT = document.createElement('div'); /*КАЖДЫЙ ЭЛЕМЕНТ БУДЕТ ЗАПИСЫВАТЬСЯ ВНУТРИ ПЕРЕМЕННОЙ logoAT*/
        logoAT.className = 'logoAT'; /*ДЛЯ ВЫШЕ СОЗДАННОГО ЭЛЕМЕНТА МЫ ПРОПИСЫВАЕМ КЛАСС logoAT*/
        // logoAT.style.background = '#bf7411';
        logoAT.style.background = '#3f3f40';
        logoAT.style.minWidth = width + 'px'; /*УКАЗЫВАЕМ ЕГО ШИРИНУ*/
        bodyAT.appendChild(logoAT); /*ДОБАВЛЯЕМ ЭЛЕМЕНТ ВО ВНУТРЬ ЭЛЕМЕНТА bodyAT*/
      }

      myElements = document.getElementsByClassName('logoAT');/*ЗАПИСЫВАЕМ ЭЛЕМЕНТ logoAT В ПЕРЕМЕННУЮ myElement*/
      context = new AudioContext(); /*СОЗДАЕМ НОВЫЙ ЭКЗЕМПЛЯР КЛАССА AudioContext*/

      /*СОЗДАЁМ АНАЛАЙЗЕР*/
      analyser = context.createAnalyser();

      src = context.createMediaStreamSource(AUDIO_STREAM);
      src.connect(analyser);
      loop();

      function loop() {
        if (myElements.length > 0) {
          window.requestAnimationFrame(loop);
          analyser.getByteFrequencyData(array);
          if (!This.isPauseRecordMic) {
            for (var i = 0; i < num; i++) {
              height = array[i + num];
              if (height / 5 > 40) {
                myElements[i].style.minHeight = '38px';
              } else {
                myElements[i].style.minHeight = height / 5 + 'px';
              }
              myElements[i].style.opacity = 0.008 * height;
            }
          }
        }
      }
    }
    ,

    // отправить блоб аудио для конв. в текст
    sendBlobServer() {
      let reader = new FileReader();
      ////console.log('This.voiceBlob', This.voiceBlob

      reader.readAsDataURL(This.voiceBlob);
      reader.onloadend = function () {
        var base64String = reader.result;
        var bBase64String = base64String.substr(base64String.indexOf(', ') + 1)

        var time = This.setTimestamp()
        This.tTimestamp = time
        //console.log('sendBlobServer id mess ' + This.roomId + This.senderId + time)
        This.idSecondMessAT = This.roomId + This.senderId + time

        // let body = {
        //   roomId: This.roomId,
        //   indexId: This.currentSenderID + This.messages.length,
        //   content: '',
        //   contentSrc: '',
        //   senderId: This.currentSenderID,
        //   username: This.currentSenderName,
        //   timestamp: This.tTimestamp,
        //   base: bBase64String,
        //   time: time,
        //   langSrc: This.langSrc,
        //   langTgt: This.langTgt
        // }
        // if (This.isTranslate)
        $('#idDivisSearchToText').show()

        //console.log('This.isTranslate', This.isTranslate)
        //console.log('This.langTgt', This.langTgt)

        if (This.isTranslate) {
          $('#idDivisSearchToText').css('height', '120px')
          This.$emit('sendBlobAudioTranslate', bBase64String, This.langSrc, This.langTgt, time)
        } else {
          $('#idDivisSearchToText').css('height', '48px')

          if (This.userLang == 'RU') {
            This.$emit('sendBlobAudioTranslate', bBase64String, This.userLang, 'EN', time)
          } else {
            This.$emit('sendBlobAudioTranslate', bBase64String, This.userLang, 'RU', time)
          }
        }
        This.isSearchToText = true
      }
    }
    ,

    // начать запись блоб для аудио перевода
    elStartSaveAudioBlob() {
      This.audiostartATs.play()
    },

    // остановить запись блоб для аудио перевода
    elStopSaveAudioBlob() {
      try {
        $('#idAudioGraphAT').empty();
        AUDIO_STREAM.getTracks().forEach(function (track) {
          track.stop();
        });
      } catch (e) {
        // console.error('e 1 ', e)
      }
    },
    //endregion

    //endregion

    // region_____________________SEND FILE SLISER_____________________________
    sendFileMessage(message) {
      // if(This.isTranslate){
      This.pdfArray = []
      var files = message.files
      var num = 0
      //console.log('sendFileMessage:', files)

      files.forEach((selectedFile) => {
        //console.log("selectedFile:", selectedFile);
        //console.log("selectedFile size :", selectedFile.blob.size);
        //console.log("selectedFile.type :", selectedFile.extension);
        //console.log("selectedFile.name :", selectedFile.name)

        if (selectedFile.name === 'image'){
          selectedFile.name = 'image'+Date.now()
          //console.log("selectedFile.name NEW :", selectedFile.name)
        }

        if(selectedFile.blob.size > 10 * 1024 * 1024){
          This.$root.showInfo(This.$tc('gc.size.max.file.chat', 1) + ' ' +selectedFile.name+'.'+selectedFile.extension + ' ' + This.$tc('gc.size.max.file.chat', 0))
          //console.log('Размер файла '+selectedFile.name+' = '+selectedFile.blob.size+', первышает максимальный размер '+(10 * 1024 * 1024))
          // delete files[num];
          files.splice(num, 1);
          //console.log('удалили файли из очереди, теперь ',files)

          // if (num != 0 && num == files.length) {
          //   //console.log('отправка сообщения с файлом на сервер num', num)
          //
          //   let body = {
          //     roomId: This.roomId,
          //     indexId: This.currentSenderID + This.messages.length,
          //     content: message.content,
          //     contentSrc: '',
          //     senderId: This.currentSenderID,
          //     username: This.currentSenderName,
          //     avatar: This.currentAvatar,
          //     date: This.currentDate(),
          //     timestamp: This.currentTime(),
          //     system: false,
          //     saved: true,
          //     distributed: true,
          //     seen: true,
          //     deleted: false,
          //     disableActions: false,
          //     disableReactions: false,
          //     files: message.files,
          //     replyMessage: null,
          //     langSrc: '',
          //     langTgt: ''
          //   }
          //   //console.log(`${This.urlRest}/exgc/sendChatMess`)
          //   // отправка файлов
          //   This.$root.restPost(`${This.urlRest}/exgc/sendChatMess`, body)
          //     .then((res) => {
          //       //console.log('res sendChatMess отправка файлов', res)
          //       var newMess = {
          //         _id: res.gcChatId,
          //         indexId: res.gcChatId,
          //         content: message.content,
          //         contentSrc: '...',
          //         senderId: This.currentSenderID,
          //         username: This.currentSenderName,
          //         avatar: This.currentAvatar,
          //         date: This.currentDate(),
          //         timestamp: This.currentTime(),
          //         system: false,
          //         saved: true,
          //         distributed: true,
          //         seen: true,
          //         deleted: false,
          //         disableActions: false,
          //         disableReactions: false,
          //         files: message.files,
          //         replyMessage: null,
          //         langSrc: '',
          //         langTgt: '',
          //         plan: false
          //       }
          //
          //       This.pdfMap[newMess._id] = This.pdfArray
          //
          //       This.messages = [...This.messages, newMess] //сообщение с файлом
          //       This.funcMessages(newMess._id, newMess.langSrc != '', true, newMess)
          //       ////console.log('CH 9 ' + This.currentSenderID)
          //       This.textLengthAT(0, false)// отправка сообщения в чате с файлом
          //       This.$emit('sendMessage', This.currentSenderID, newMess)
          //
          //       This.newSelect()
          //     })
          // }

        }else{
          let sliceSize = 100000;
          var reader = new FileReader();

          reader.readAsDataURL(selectedFile.blob);
          reader.onloadend = function () {
            var base64String = reader.result;
            //console.log(base64String, 'reader.result')

            var bBase64String = base64String.substr(base64String.indexOf(', ') + 1)

            // var name = selectedFile.localUrl.substr(selectedFile.localUrl.length - 8) + '.' + selectedFile.extension
            //
            // if (selectedFile.extension === 'pdf' || selectedFile.extension === 'docx' || selectedFile.extension === 'zip'
            //   || selectedFile.extension === 'xlsx' || selectedFile.extension === 'xls') {
            //   name = selectedFile.name +'.'+selectedFile.extension
            //   //для скачивания пдф
            // }

            var name = selectedFile.name +'.'+selectedFile.extension

            name = encodeURIComponent(name)
            //console.log('encodeURIComponent',name)

            var sizeSlice = 1300000
            //console.log('bBase64String.length = ' + bBase64String.length)
            var sumSlice = Math.ceil(bBase64String.length / sizeSlice)
            //console.log('sumSlice = ' + sumSlice)

            if (sumSlice > 1) {

              $('#showLoadTip').appendTo($('.vac-container-scroll'))

              setTimeout(() => {
                //console.log('1',$('#room-footer'))
                //console.log('1',$('#showLoadTip'))
                $('#room-footer').prepend($('#showLoadTip'))
                $('#showLoadTip').show()
              }, 10)
            }

            // var arFile = []

            var numSlice = 1
            let headers = {
              'Content-Type': 'application/json',
            }
            let bv = ","
            for (var i = 0; i < 10000; i++) {
              bv += "          "
            }

            setTimeout(function runSendSlice() {
              //console.log('runSendSlice ', numSlice)
              //console.log('runSendSlice ', ((numSlice - 1) * sizeSlice) + ' - ' + (numSlice * sizeSlice))
              //console.log('runSendSlice ', bBase64String.slice(((numSlice - 1) * sizeSlice), (numSlice * sizeSlice)))
              if (numSlice == sumSlice) {
                var bytes = bBase64String.slice(((numSlice - 1) * sizeSlice), bBase64String.length) + bv

                This.$root.restPost(`${This.urlRest}/exgc/saveChatFileMulti/${This.roomId}/${sumSlice}/${numSlice}/${name}`,
                  bytes, headers).then((res) => {
                  //console.log('bBase64String', bytes)
                  //console.log('res 1', res)

                  message.files[num].url = res.url
                  message.files[num].preview = res.preview

                  // if (selectedFile.extension === 'pdf' || selectedFile.extension === 'docx' || selectedFile.extension === 'zip'
                  //   || selectedFile.extension === 'xlsx' || selectedFile.extension === 'xls') {
                  //console.log('selectedFile.extension',selectedFile.extension)
                  //console.log('selectedFile.name',name)
                  if( selectedFile.extension !== 'png' || selectedFile.extension !== 'jpg'
                    || selectedFile.extension !== 'jpeg'){
                    This.pdfArray.push(decodeURIComponent(name), selectedFile.extension, res.url) //для скачивания пдф cdjq
                  }

                  num++

                  if (num == files.length) {
                    //console.log('отправка сообщения с файлом на сервер num', num)

                    let body = {
                      roomId: This.roomId,
                      indexId: This.currentSenderID + This.messages.length,
                      content: message.content,
                      contentSrc: '',
                      senderId: This.currentSenderID,
                      username: This.currentSenderName,
                      avatar: This.currentAvatar,
                      date: This.currentDate(),
                      timestamp: This.currentTime(),
                      system: false,
                      saved: true,
                      distributed: true,
                      seen: true,
                      deleted: false,
                      disableActions: false,
                      disableReactions: false,
                      files: message.files,
                      replyMessage: null,
                      langSrc: '',
                      langTgt: ''
                    }
                    //console.log(`${This.urlRest}/exgc/sendChatMess`)
                    // отправка файлов
                    This.$root.restPost(`${This.urlRest}/exgc/sendChatMess`, body)
                      .then((res) => {
                        //console.log('res sendChatMess отправка файлов', res)
                        var newMess = {
                          _id: res.gcChatId,
                          indexId: res.gcChatId,
                          content: message.content,
                          contentSrc: '...',
                          senderId: This.currentSenderID,
                          username: This.currentSenderName,
                          avatar: This.currentAvatar,
                          date: This.currentDate(),
                          timestamp: This.currentTime(),
                          system: false,
                          saved: true,
                          distributed: true,
                          seen: true,
                          deleted: false,
                          disableActions: false,
                          disableReactions: false,
                          files: message.files,
                          replyMessage: null,
                          langSrc: '',
                          langTgt: '',
                          plan: false,
                          name: name
                        }

                        This.pdfMap[newMess._id] = This.pdfArray

                        This.messages = [...This.messages, newMess] //сообщение с файлом
                        This.funcMessages(newMess._id, newMess.langSrc != '', true, newMess)
                        ////console.log('CH 9 ' + This.currentSenderID)
                        This.textLengthAT(0, false)// отправка сообщения в чате с файлом
                        This.$emit('sendMessage', This.currentSenderID, newMess)

                        This.newSelect()
                      })
                  }
                });
              } else {
                var bytes = bBase64String.slice(((numSlice - 1) * sizeSlice), (numSlice * sizeSlice)) + bv

                This.$root.restPost(`${This.urlRest}/exgc/saveChatFileMulti/${This.roomId}/${sumSlice}/${numSlice}/${name}`,
                  bytes, headers).then((res) => {
                  //console.log('bBase64String length', bytes.length)
                  numSlice++;
                  setTimeout(runSendSlice, 0);
                });
              }
            }, 0);
          }
        }
      })
    }
    ,

    elSelects() {
      document.getElementsByClassName('v-select__slot').forEach((item) => {
        item.classList.add("styleATSl")
      })

      document.getElementsByClassName('v-input__slot').forEach((item) => {
        item.classList.add("styleATSl")
      })

      document.getElementsByClassName('v-select__selections').forEach((item) => {
        item.classList.add("styleATSl")
      })

      setTimeout(() => {
        document.getElementsByClassName('v-label--active').forEach((item) => {
          item.style.top = '18px';
          item.style.fontSize = '12px';
        })
      }, 2000)
    },
//endregion

    createNewElements(el) {
      switch (el) {
        case 'vacDivisSearchToText':
          return `<div id="idDivisSearchToText" class="divisSearchToText">
                <p id="idPTextSearchToText" class="pTextSearchToText"></p>
            </div>`
        case 'vacIconTextAreaLeft':
          return `<div id="idVacIconTextareaLeft" class="vac-icon-textarea-left"></div>`
        case 'divRowSaveAudioAT':
          return `<div id="idDivRowSaveAudioAT" class="row rowAudioAT"></div>`
        case 'vacBtnAudioMess':
          return `<div id="idVacBtnAudioMess" title="${This.$t('gc.at.btn.audio')}" class="vac-svg-button vacBtnBar pAT-5-0-0-4" >
              <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="24" height="24" viewBox="0 0 512 512" class="vac-icon-microphone"><path id="vac-icon-microphone" d="M432.8,216.4v39.2c0,45.2-15.3,84.3-45.2,118.4c-29.8,33.2-67.3,52.8-111.6,57.9v40.9h78.4c5.1,0,10.2,1.7,13.6,6c4.3,4.3,6,8.5,6,13.6c0,5.1-1.7,10.2-6,13.6c-4.3,4.3-8.5,6-13.6,6H157.6c-5.1,0-10.2-1.7-13.6-6c-4.3-4.3-6-8.5-6-13.6c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6H236v-40.9c-44.3-5.1-81.8-23.9-111.6-57.9s-45.2-73.3-45.2-118.4v-39.2c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6s10.2,1.7,13.6,6c4.3,4.3,6,8.5,6,13.6v39.2c0,37.5,13.6,70.7,40,97.1s59.6,40,97.1,40s70.7-13.6,97.1-40c26.4-26.4,40-59.6,40-97.1v-39.2c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6c5.1,0,10.2,1.7,13.6,6C430.2,206.2,432.8,211.3,432.8,216.4z M353.5,98v157.6c0,27.3-9.4,50.3-29,69c-19.6,19.6-42.6,29-69,29s-50.3-9.4-69-29c-19.6-19.6-29-42.6-29-69V98c0-27.3,9.4-50.3,29-69c19.6-19.6,42.6-29,69-29s50.3,9.4,69,29C344.2,47.7,353.5,71.6,353.5,98z"></path>
              </svg>
           </div>`
        case 'btnSaveAudioBlobAT':
          return `<div id="idVacBtnSaveBlobAT" title="${This.$t('gc.at.btn.audio')}" class="vac-svg-button btnATBar pAT-5-0-0-5">
              <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="24" height="24" viewBox="0 0 512 512" class="vac-icon-microphone"><path id="vac-icon-microphone" d="M432.8,216.4v39.2c0,45.2-15.3,84.3-45.2,118.4c-29.8,33.2-67.3,52.8-111.6,57.9v40.9h78.4c5.1,0,10.2,1.7,13.6,6c4.3,4.3,6,8.5,6,13.6c0,5.1-1.7,10.2-6,13.6c-4.3,4.3-8.5,6-13.6,6H157.6c-5.1,0-10.2-1.7-13.6-6c-4.3-4.3-6-8.5-6-13.6c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6H236v-40.9c-44.3-5.1-81.8-23.9-111.6-57.9s-45.2-73.3-45.2-118.4v-39.2c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6s10.2,1.7,13.6,6c4.3,4.3,6,8.5,6,13.6v39.2c0,37.5,13.6,70.7,40,97.1s59.6,40,97.1,40s70.7-13.6,97.1-40c26.4-26.4,40-59.6,40-97.1v-39.2c0-5.1,1.7-10.2,6-13.6c4.3-4.3,8.5-6,13.6-6c5.1,0,10.2,1.7,13.6,6C430.2,206.2,432.8,211.3,432.8,216.4z M353.5,98v157.6c0,27.3-9.4,50.3-29,69c-19.6,19.6-42.6,29-69,29s-50.3-9.4-69-29c-19.6-19.6-29-42.6-29-69V98c0-27.3,9.4-50.3,29-69c19.6-19.6,42.6-29,69-29s50.3,9.4,69,29C344.2,47.7,353.5,71.6,353.5,98z"></path>
              </svg>
           </div>`
        case 'btnSaveAudioBlobATClear':
          return `<div id="idVacSvgButton2" title="${This.$t('gc.at.btn.audioClear')}" class="vac-svg-button btnATBar pAT-5-0-0-5">
              <svg version="1.1" width="24" height="24" viewBox="0 0 24 24" class="">
                <path d="M19,6.41L17.59,5L12,10.59L6.41,5L5,6.41L10.59,12L5,17.59L6.41,19L12,13.41L17.59,19L19,17.59L13.41,12L19,6.41Z" stroke="#cb2020" fill="#cb2020"></path>
              </svg>
           </div>`
        case 'btnSaveAudioBlobATSave':
          return `<div id="idVacSvgButton3" title="${This.$t('gc.at.btn.audioSend')}" class="vac-svg-button btnATBar pAT-5-0-0-5">
              <svg version="1.1" width="24" height="24" viewBox="0 0 24 24"><path d="M21,7L9,19L3.5,13.5L4.91,12.09L9,16.17L19.59,5.59L21,7Z" stroke="#43b338" fill="#43b338"></path>
              </svg>
           </div>`
        case 'btnSendMessageAT':
          return `<div id="idVacSvgButton4" title="${This.$t('gc.at.btn.textSend')}" class="vac-svg-button btnATBar sendTextAT ml-1">
            <svg  version="1.1" width="24" height="24" viewBox="0 0 24 24"><path d="M2,21L23,12L2,3V10L17,12L2,14V21Z" style="fill:#bf7411"></path><!----></svg>
           </div>`
        case 'btnShowAT':
          return `<div id="idVacSvgButton0" title="${This.$t('gc.at.btn.open')}" class="vac-svg-button btnATBar mb1 pAT-3-0-0-3">
            <svg width="28" height="28">
            <g>
            <g stroke="currentColor" fill="none" fill-rule="evenodd" stroke-linecap="round" stroke-linejoin="round" id="svg_1" stroke-width="2">
            <path stroke="#bf7411" d="m24.70932,14.09756l0,-8.05769c0,-1.48338 -1.2038,-2.6859 -2.68877,-2.6859l-8.0663,0c-1.48496,0 -2.68877,1.20252 -2.68877,2.6859l0,8.05769c0,1.48338 1.2038,2.6859 2.68877,2.6859l8.0663,0c1.48496,0 2.68877,-1.20252 2.68877,-2.6859z" id="svg_2"/>
            <path stroke="#bf7411" d="m8.57672,11.41628l-2.68878,0c-1.48496,0 -2.68877,1.20252 -2.68877,2.6859l0,8.05693c0,1.48338 1.2038,2.6859 2.68877,2.6859l0.00464,0l8.06633,-0.01392c1.48313,-0.00256 2.68412,-1.20433 2.68412,-2.6859l0,-2.67584" id="svg_3"/>
            <g stroke="null" id="svg_4">
            <path stroke="#bf7411" d="m9.92111,16.78748l-4.03315,0" id="svg_5"/>
            <path stroke="#bf7411" d="m11.93824,18.79787c-0.44814,0.44765 -0.89626,0.8953 -1.34438,1.34295s-1.56845,1.11912 -3.36096,2.01443" id="svg_6"/>
            <path stroke="#bf7411" d="m7.23291,16.78771c0.44812,1.56606 1.12032,2.68447 2.01658,3.35523s2.01658,1.34154 3.36096,2.0123" id="svg_7"/>
            </g>
            <g stroke="null" id="svg_8">
            <path stroke="#bf7411" d="m17.98741,6.03986l-4.03315,8.05769" id="svg_9"/>
            <path stroke="#bf7411" d="m17.98741,6.03986l4.03315,8.05769" id="svg_10"/>
            <path stroke="#bf7411" d="m20.67618,11.41165l-5.37753,0" id="svg_11"/>
            </g>
            </g>
            </g>
            </svg>
            </div> `
        case 'btnSettingAT':
          return `<div id="idVacSvgButton8" class="vac-svg-button btnATBar pAT-3-0-0-3"
            >
            <svg width="28" height="28">
            <g>
            <g stroke="null" id="_1" data-name="1">
            <path stroke="#bf7411" fill="#bf7411" d="m15.73746,25.52641l-3.47205,0a0.86269,0.86018 0 0 1 -0.85809,-0.76957l-0.25708,-2.41366a8.78625,8.76064 0 0 1 -1.04961,-0.43353l-1.8916,1.52539a0.86269,0.86018 0 0 1 -1.15026,-0.06079l-2.45523,-2.44807a0.86269,0.86018 0 0 1 -0.06096,-1.14691l1.53042,-1.88838a8.75059,8.72509 0 0 1 -0.43537,-1.04655l-2.42474,-0.25805a0.86269,0.86018 0 0 1 -0.77182,-0.85502l0,-3.46251a0.86269,0.86018 0 0 1 0.77182,-0.85502l2.42072,-0.25633a8.75059,8.72509 0 0 1 0.43537,-1.04655l-1.53042,-1.88838a0.86269,0.86018 0 0 1 0.06096,-1.14691l2.45523,-2.44807a0.86269,0.86018 0 0 1 1.15026,-0.06079l1.8939,1.52539a8.78625,8.76064 0 0 1 1.04961,-0.43353l0.25708,-2.41366a0.86269,0.86018 0 0 1 0.85982,-0.77129l3.47205,0a0.86269,0.86018 0 0 1 0.85809,0.76957l0.25651,2.41366a8.7943,8.76867 0 0 1 1.05019,0.43353l1.89333,-1.52539a0.86269,0.86018 0 0 1 1.15026,0.06079l2.45523,2.44807a0.86269,0.86018 0 0 1 0.06096,1.14691l-1.53042,1.88838a8.84549,8.81971 0 0 1 0.43537,1.04655l2.42072,0.25633a0.86269,0.86018 0 0 1 0.77125,0.85502l0,3.46251a0.86269,0.86018 0 0 1 -0.76895,0.85674l-2.42072,0.25633a8.84549,8.81971 0 0 1 -0.43537,1.04655l1.53042,1.88838a0.86269,0.86018 0 0 1 -0.06096,1.14691l-2.45523,2.44979a0.86269,0.86018 0 0 1 -1.15026,0.06079l-1.89333,-1.52539a8.7943,8.76867 0 0 1 -1.05019,0.43353l-0.25651,2.41366a0.86269,0.86018 0 0 1 -0.86039,0.76957zm-2.69736,-1.72036l1.92036,0l0.23523,-2.21124a0.86269,0.86018 0 0 1 0.63609,-0.74033a7.07409,7.05347 0 0 0 1.73459,-0.71854a0.86269,0.86018 0 0 1 0.97772,0.07512l1.73459,1.39751l1.35788,-1.35392l-1.40389,-1.72437a0.86269,0.86018 0 0 1 -0.07534,-0.97487a7.05281,7.03225 0 0 0 0.71834,-1.72839a0.86269,0.86018 0 0 1 0.74307,-0.63424l2.21712,-0.23512l0,-1.91476l-2.21712,-0.23512a0.86269,0.86018 0 0 1 -0.74307,-0.63424a7.05281,7.03225 0 0 0 -0.71834,-1.72896a0.86269,0.86018 0 0 1 0.07534,-0.97487l1.40159,-1.72953l-1.35788,-1.35392l-1.73459,1.39751a0.86269,0.86018 0 0 1 -0.97772,0.07512a7.07409,7.05347 0 0 0 -1.73344,-0.71624a0.86269,0.86018 0 0 1 -0.63609,-0.74033l-0.23293,-2.20837l-1.92151,0l-0.23523,2.21124a0.86269,0.86018 0 0 1 -0.63667,0.74033a7.06201,7.04143 0 0 0 -1.73171,0.71854a0.86269,0.86018 0 0 1 -0.97772,-0.07512l-1.73286,-1.3998l-1.3573,1.35392l1.40216,1.72953a0.86269,0.86018 0 0 1 0.07477,0.97487a7.09767,7.07698 0 0 0 -0.71834,1.72839a0.86269,0.86018 0 0 1 -0.74249,0.63424l-2.2177,0.23512l0,1.91419l2.2177,0.23512a0.86269,0.86018 0 0 1 0.74249,0.63424a7.09767,7.07698 0 0 0 0.71776,1.72667a0.86269,0.86018 0 0 1 -0.07477,0.97487l-1.40159,1.72667l1.35788,1.35392l1.73516,-1.39751a0.86269,0.86018 0 0 1 0.97772,-0.07512a7.06201,7.04143 0 0 0 1.73286,0.71624a0.86269,0.86018 0 0 1 0.63667,0.74033l0.2312,2.21124zm11.65844,-8.07479l0,0z"/>
            <path stroke="#bf7411" fill="#bf7411" d="m14.00115,19.23907a5.25438,5.23907 0 1 1 5.25496,-5.23907a5.26013,5.2448 0 0 1 -5.25496,5.23907zm0,-8.75778a3.52899,3.51871 0 1 0 3.52957,3.51871a3.53302,3.52272 0 0 0 -3.52957,-3.51871z"/>
            </g>
            </g>
            </svg>
            </div> `
        case 'btnReTextTranslate':
          return `<div id="arrows_${parsedMessage._id}" style="width: 0px" title="${This.$t('gc.at.btn.at.text')}">
            <svg version="1.1" id="arrows_svg_${parsedMessage._id}" x="0px" y="0px" width="10px" height="10px" viewBox="0 0 511.624 511.623" style="position: absolute;
    top: 4px;
    right: 4px;
    border: 1px solid rgb(225, 228, 232);
    border-radius: 4px;
    padding: 4px;
    cursor: pointer;
    pointer-events: all;
    background: #ffffff6b;" xml:space="preserve" title="${This.$t('gc.at.btn.at.text')}">
              <g style="pointer-events:none;">
              \t\t<path style="pointer-events:none;" d="M9.135,200.996h392.862v54.818c0,2.475,0.9,4.613,2.707,6.424c1.811,1.81,3.953,2.713,6.427,2.713
              \t\t\tc2.666,0,4.856-0.855,6.563-2.569l91.365-91.362c1.707-1.713,2.563-3.903,2.563-6.565c0-2.667-0.856-4.858-2.57-6.567
              \t\t\tl-91.07-91.078c-2.286-1.906-4.572-2.856-6.858-2.856c-2.662,0-4.853,0.856-6.563,2.568c-1.711,1.715-2.566,3.901-2.566,6.567
              \t\t\tv54.818H9.135c-2.474,0-4.615,0.903-6.423,2.712S0,134.568,0,137.042v54.818c0,2.474,0.903,4.615,2.712,6.423
              \t\t\tS6.661,200.996,9.135,200.996z"/>
              \t\t<path style="pointer-events:none;" d="M502.49,310.637H109.632v-54.82c0-2.474-0.905-4.615-2.712-6.423c-1.809-1.809-3.951-2.712-6.424-2.712
              \t\t\tc-2.667,0-4.854,0.856-6.567,2.568L2.568,340.607C0.859,342.325,0,344.509,0,347.179c0,2.471,0.855,4.568,2.568,6.275
              \t\t\tl91.077,91.365c2.285,1.902,4.569,2.851,6.854,2.851c2.473,0,4.615-0.903,6.423-2.707c1.807-1.813,2.712-3.949,2.712-6.427V383.72
              \t\t\tH502.49c2.478,0,4.613-0.899,6.427-2.71c1.807-1.811,2.707-3.949,2.707-6.427v-54.816c0-2.475-0.903-4.613-2.707-6.42
              \t\t\tC507.103,311.54,504.967,310.637,502.49,310.637z"/>
              </g>
            </svg>
          </div>`
        case 'btnCloseAT':
          return `<div id="idVacSvgButton9" title="${This.$t('gc.at.btn.close')}" class="vac-svg-button btnATBar mb1 pAT-3-0-0-3"
      >
      <svg width="28" height="28">
      <g>
      <g>
      <g stroke="currentColor" fill="none" fill-rule="evenodd" stroke-linecap="round" stroke-linejoin="round" id="svg_1" stroke-width="2">
      <path stroke="#bf7411" d="m24.70932,14.09756l0,-8.05769c0,-1.48338 -1.2038,-2.6859 -2.68877,-2.6859l-8.0663,0c-1.48496,0 -2.68877,1.20252 -2.68877,2.6859l0,8.05769c0,1.48338 1.2038,2.6859 2.68877,2.6859l8.0663,0c1.48496,0 2.68877,-1.20252 2.68877,-2.6859z" id="svg_2"/>
      <path stroke="#bf7411" d="m8.57672,11.41628l-2.68878,0c-1.48496,0 -2.68877,1.20252 -2.68877,2.6859l0,8.05693c0,1.48338 1.2038,2.6859 2.68877,2.6859l0.00464,0l8.06633,-0.01392c1.48313,-0.00256 2.68412,-1.20433 2.68412,-2.6859l0,-2.67584" id="svg_3"/>
      <g stroke="null">
      <path stroke="#bf7411" d="m9.92111,16.78748l-4.03315,0" id="svg_5"/>
      <path stroke="#bf7411" d="m11.93824,18.79787c-0.44814,0.44765 -0.89626,0.8953 -1.34438,1.34295s-1.56845,1.11912 -3.36096,2.01443" id="svg_6"/>
      <path stroke="#bf7411" d="m7.23291,16.78771c0.44812,1.56606 1.12032,2.68447 2.01658,3.35523s2.01658,1.34154 3.36096,2.0123" id="svg_7"/>
      </g>
      <g stroke="null">
      <path stroke="#bf7411" d="m17.98741,6.03986l-4.03315,8.05769" id="svg_9"/>
      <path stroke="#bf7411" d="m17.98741,6.03986l4.03315,8.05769" id="svg_10"/>
      <path stroke="#bf7411" d="m20.67618,11.41165l-5.37753,0" id="svg_11"/>
      </g>
      </g>
      </g>
      <line stroke-width="2" stroke-linecap="undefined" stroke-linejoin="undefined" id="svg_13" y2="25.49253" x2="1.46269" y1="3.70149" x1="26.53732" stroke="#ffffff" fill="none"/>
      <line stroke-width="2" stroke-linecap="undefined" stroke-linejoin="undefined" id="svg_14" y2="24" x2="-0.02984" y1="0.50746" x1="27.50746" stroke="#bf7411" fill="none"/>
      </g>
      </svg>
      </div> `
      }
    },

    funcMessages(_id, isTranslate, isMy, parsedMessage) {
      //console.log('funcMessages работает')

      setTimeout(()=>{
        $('#showLoadTip').hide()
      }, 300)

      $('#openNewDialog').appendTo($('#devDevIconNoVideoMain1'))
      setTimeout(() => {
        let secondChild = 0;
        let wrappers = document.querySelectorAll('.vac-message-wrapper')
        for (let i = 0; i < wrappers.length; i++) {
          let id = wrappers[i].id

          //console.log('id ' + id)
          //console.log('This.pdfMap', This.pdfMap)
          //console.log('This.pdfMap[id]', This.pdfMap[id])

          let currNodes = document.getElementById('' + id).querySelectorAll('.vac-message-file-container')
          for (let j = 0; j < currNodes.length; j++) {
            //console.log('зашел в for')
            if (currNodes[j].getAttribute('hasDiv') === 'true') continue
            let div = $('<div></div>').css({
              'left': '0px',
              'top': '0px',
              'width': '270px',
              'height': '235px',
              'position': 'absolute',
              'z-index': '10000'
            })
            div.appendTo(currNodes[j].children)

            currNodes[j].setAttribute('hasDiv', 'true')

            if (currNodes[j].children[0].children[0].getAttribute('class').split(' ')[0] ===
              'vac-loader-wrapper') {
              secondChild = 1
              //console.log(secondChild)
            }

            //console.log(currNodes[j])
            //console.log(currNodes[j].children[0])
            //console.log(currNodes[j].children[0].children[secondChild])

            let fullBgImage = currNodes[j].children[0].children[secondChild].style.backgroundImage.toString()
            //console.log(fullBgImage)
            let len = fullBgImage.length
            //console.log(fullBgImage + ' ' + len)
            let src = fullBgImage.substring(5, len - 2)
            //console.log(src + ' srс, которая в чате показывает картинку')

            div.attr('id', src)
            div.attr('name', parsedMessage.name)
            div.click(function (e) {
              e.stopPropagation()
              This.src = div.attr('id').replace('_m.', '.')
              This.dialog = true
              This.name = div.attr('name')

              setTimeout(() => {
                //console.log($('div.viewer-toolbar ul .viewer-one-to-one'))
                $('div.viewer-toolbar ul .viewer-one-to-one').remove()
                $('div.viewer-toolbar ul .viewer-reset').remove()
                $('div.viewer-toolbar ul .viewer-rotate-left').remove()
                $('div.viewer-toolbar ul .viewer-rotate-right').remove()
              }, 300)

            })
          }
          //pdf
          let svgButtons = document.getElementById('' + id).querySelectorAll('.vac-svg-button')
          let titles = document.getElementById('' + id).querySelectorAll('.vac-text-ellipsis:not([class*=" "])')
          let extensions = document.getElementById('' + id).querySelectorAll('.vac-text-extension')
          let j = 0;
          if (svgButtons.length === 0 || titles.length === 0 || extensions.length === 0)
            continue;

          for (let i = 0; i < svgButtons.length; i++) {
            //console.log(j, 'j')
            //console.log('svgButtons[i] '+i+' _ ', svgButtons[i])
            //console.log('titles[i] '+i+' _ ', titles[i])
            //console.log('extensions[i] '+i+' _ ', extensions[i])

            if (svgButtons[i].hasAttribute('name'))
              continue
            svgButtons[i].setAttribute('name', This.pdfMap[id][j])
            titles[i].setAttribute('name', This.pdfMap[id][j])
            extensions[i].setAttribute('name', This.pdfMap[id][j])

            svgButtons[i].setAttribute('ex', This.pdfMap[id][j + 1])
            titles[i].setAttribute('ex', This.pdfMap[id][j + 1])
            extensions[i].setAttribute('ex', This.pdfMap[id][j + 1])

            svgButtons[i].setAttribute('link', This.pdfMap[id][j + 2])
            titles[i].setAttribute('link', This.pdfMap[id][j + 2])
            extensions[i].setAttribute('link', This.pdfMap[id][j + 2])

            titles[i].innerText = This.pdfMap[id][j]
            extensions[i].innerText = This.pdfMap[id][j + 1]

            svgButtons[i].addEventListener('click', function () {
              let a = document.createElement("a");
              a.href = svgButtons[i].getAttribute('link')
              a.download = svgButtons[i].getAttribute('name') + '.'
                + svgButtons[i].getAttribute('ex')
              a.click()
            })

            if (svgButtons[i].getAttribute('ex') == 'pdf') {
              titles[i].addEventListener('pointerdown', function (e) {
                event.stopPropagation()
                fetch(extensions[i].getAttribute('link'))
                  .then(response => response.blob())
                  .then(blob => {
                    var blob2 = new Blob([blob], {type: 'application/pdf'});
                    var blobURL = URL.createObjectURL(blob2);
                    window.open(blobURL);
                  });
              })

              extensions[i].addEventListener('pointerdown', function (e) {
                event.stopPropagation()
                fetch(extensions[i].getAttribute('link'))
                  .then(response => response.blob())
                  .then(blob => {
                    var blob2 = new Blob([blob], {type: 'application/pdf'});
                    var blobURL = URL.createObjectURL(blob2);
                    window.open(blobURL);
                  });
              })
            }
            j += 3
          }
        }
      }, 300)

      if (parsedMessage.content.length > 0) {
        setTimeout(() => {

          // //console.log('funcMessages1', _id, isTranslate, isMy, parsedMessage)
          // //console.log('funcMessages2', $('#' + _id).children('.vac-message-box'))
          // //console.log('funcMessages3', $('#' + _id).children('.vac-message-box')
          //   .children('.vac-message-container'))
          // //console.log('funcMessages4', $('#' + _id).children('.vac-message-box')
          //   .children('.vac-message-container')
          //   .children('.vac-message-card'))

          var divPlan = `<div id="divPlanMessGC_${_id}" class="divPlanCss">
                <span>${This.$t('gc.plan.text')}</span>
                <span id="spanPlanMessGC_${_id}"></span>
            </div>`
          $('#' + parsedMessage._id).children('.vac-message-box').children('.vac-message-container')
            .children('.vac-message-card').prepend(divPlan)

          $('#divPlanMessGC_' + parsedMessage._id).hide()

          $('#' + _id).children('.vac-message-box')
            .children('.vac-message-container')
            .children('.vac-message-card')
            .children('.vac-message-actions-wrapper')
          // var ddd = `<div id="btnPlanMess_${_id}" style=" z-index: 1;" class="vac-svg-button vac-message-options __web-inspector-hide-shortcut__ animBtnChatMenu"><svg width="24" height="24" viewBox="0 0 24 24"><path d="M7.41,8.58L12,13.17L16.59,8.58L18,10L12,16L6,10L7.41,8.58Z"></path></svg></div>`
          var ddd = `<div id="btnPlanMess_${_id}" style=" z-index: 1;" class="vac-svg-button vac-message-options __web-inspector-hide-shortcut__ animBtnChatMenu">
                <svg width="24" height="24">
                  <g>
                    <path id="btnPlanMessPath_${_id}" stroke="null" fill="#fff" d="m8.36385,11.36232l-4.05324,2.85816l1.54939,-4.62336l-4.05232,-2.85816l5.00906,0.00089l1.33131,-3.97547l0.21717,-0.64568l1.54665,4.62026l5.01043,0l-4.05415,2.85816l1.54985,4.62469l-4.05415,-2.85949l0,0z" stroke-width="2"/>
                  </g>
                </svg>
             </div>`

          var blur = `<div id="btnPlanMessBlur_${_id}" class="vac-blur-container vac-options-me blurFunciksar"></div>`

          $('#' + _id).children('.vac-message-box')
            .children('.vac-message-container')
            .children('.vac-message-card')
            .children('.vac-message-actions-wrapper')
            .children('.vac-options-container')
            .children('span').append(blur)

          if (isTranslate) {
            $('#btnPlanMessBlur_' + _id).css('background', '#f3b563')

          } else if (isMy) {
            $('#btnPlanMessBlur_' + _id).css('background', 'var(--chat-message-bg-color-me)')
          } else {
            $('#btnPlanMessBlur_' + _id).css('background', 'white')
          }

          $('#' + _id).children('.vac-message-box')
            .children('.vac-message-container')
            .children('.vac-message-card')
            .children('.vac-message-actions-wrapper')
            .children('.vac-options-container')
            .children('span').append(ddd)

          if (isTranslate) {
            var ddd2 = `<div id="btnPlanMess2_${_id}" style=" z-index: 1;" class="vac-svg-button vac-message-options __web-inspector-hide-shortcut__ animBtnChatMenu2">
                <svg width="24" height="24">
                    <g>
                    <g stroke="null">
                     <path stroke="null" fill="#fff" d="m3.75783,7.0853l6.9463,0l0,1.24236c0,0.05609 0.01591,0.10455 0.04786,0.14559c0.03202,0.04102 0.06989,0.06149 0.11364,0.06149c0.04714,0 0.08586,-0.01938 0.11604,-0.05822l1.61545,-2.07057c0.03018,-0.03882 0.04532,-0.08845 0.04532,-0.14878c0,-0.06044 -0.01514,-0.1101 -0.04544,-0.14883l-1.61023,-2.06413c-0.04042,-0.0432 -0.08084,-0.06473 -0.12126,-0.06473c-0.04707,0 -0.08581,0.0194 -0.11604,0.0582c-0.03025,0.03887 -0.04537,0.08841 -0.04537,0.14883l0,1.24236l-6.94626,0c-0.04374,0 -0.0816,0.02046 -0.11357,0.06146s-0.04795,0.0895 -0.04795,0.14557l0,1.24236c0,0.05607 0.01597,0.10459 0.04795,0.14557s0.06982,0.06149 0.11357,0.06149z"/>
                     <path stroke="null" fill="#fff" d="m12.48097,9.57013l-6.94623,0l0,-1.2424c0,-0.05607 -0.016,-0.10459 -0.04795,-0.14557c-0.03199,-0.041 -0.06986,-0.06146 -0.11358,-0.06146c-0.04716,0 -0.08582,0.0194 -0.11611,0.0582l-1.61538,2.07045c-0.03022,0.03894 -0.04541,0.08843 -0.04541,0.14894c0,0.056 0.01512,0.10353 0.04541,0.14221l1.61036,2.07064c0.0404,0.04311 0.08079,0.06461 0.12119,0.06461c0.04373,0 0.0816,-0.02046 0.11357,-0.06135c0.03195,-0.04109 0.04795,-0.0895 0.04795,-0.14566l0,-1.24231l6.94619,0c0.04381,0 0.08156,-0.02037 0.11364,-0.06142c0.03195,-0.04104 0.04786,-0.0895 0.04786,-0.14566l0,-1.24231c0,-0.05609 -0.01597,-0.10455 -0.04786,-0.1455c-0.03207,-0.04095 -0.06984,-0.06142 -0.11364,-0.06142z"/>
                    </g>
                    </g>
                 </svg>
              </div>`

            $('#' + _id).children('.vac-message-box')
              .children('.vac-message-container')
              .children('.vac-message-card')
              .children('.vac-message-actions-wrapper')
              .children('.vac-options-container')
              .children('span').append(ddd2)

            var isDivLang = false
            $('#btnPlanMess2_' + _id).on('click', function () {
              var timeSt = $('#divLang_' + parsedMessage._id)
              timeSt.empty()
              if (isDivLang) {
                isDivLang = !isDivLang
                $('#id_span_text' + parsedMessage._id).text(parsedMessage.contentTgt)
                if (parsedMessage.langSrc == 'RU') {
                  $('#divLang_' + parsedMessage._id).css('width', '38px')
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['EN']);
                  $('#divLang_' + parsedMessage._id).prepend(This.spanTranslate);
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['RU']);
                } else {
                  $('#divLang_' + parsedMessage._id).css('width', '38px')
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['RU']);
                  $('#divLang_' + parsedMessage._id).prepend(This.spanTranslate);
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['EN']);
                }
              } else {
                isDivLang = !isDivLang
                $('#id_span_text' + parsedMessage._id).text(parsedMessage.contentSrc)
                if (parsedMessage.langSrc == 'RU') {
                  $('#divLang_' + parsedMessage._id).css('width', '4px')
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['RU']);
                } else {
                  $('#divLang_' + parsedMessage._id).css('width', '4px')
                  $('#divLang_' + parsedMessage._id).prepend(This.spanLang['EN']);
                }
              }
            });
          }

          $('#btnPlanMess_' + _id).on('click', function () {

            const names = This.messages.map(el => el._id);

            This.$emit('chatIsPlan', _id, !This.messages[names.indexOf(_id)].plan)
          });

          $('#btnPlanMess_' + _id).hide()
          $('#btnPlanMess2_' + _id).hide()
          $('#btnPlanMessBlur_' + _id).hide()

          $('#' + _id).children('.vac-message-box')
            .children('.vac-message-container')
            .children('.vac-message-card').on('mouseenter', function () {

            if (isTranslate) {
              $('#' + _id).children('.vac-message-box')
                .children('.vac-message-container')
                .children('.vac-message-card')
                .children('.vac-message-actions-wrapper').children('.vac-options-container').css('width', '56px')
            } else {
              $('#' + _id).children('.vac-message-box')
                .children('.vac-message-container')
                .children('.vac-message-card')
                .children('.vac-message-actions-wrapper').children('.vac-options-container').css('width', '34px')
            }

            $('#btnPlanMess_' + _id).show()
            $('#btnPlanMess2_' + _id).show()
            $('#btnPlanMessBlur_' + _id).show()
          });
          $('#' + _id).children('.vac-message-box')
            .children('.vac-message-container')
            .children('.vac-message-card').on('mouseleave', function () {

            $('#btnPlanMess_' + _id).hide()
            $('#btnPlanMess2_' + _id).hide()
            $('#btnPlanMessBlur_' + _id).hide()
          });
        }, 300)
      }
    },

    setPlanEl(parsedMessage) {
      const names = This.messages.map(el => el._id);

      try {
        //console.log('parsedMessage', parsedMessage)
        //console.log(This.messages[names.indexOf(parsedMessage._id)])

        This.messages[names.indexOf(parsedMessage._id)].plan = parsedMessage.isPlan
      } catch (e) {

      }


      //console.log('setPlanEl _id ' + parsedMessage._id + ' isPlan ' + parsedMessage.isPlan + ' numPlan ' + parsedMessage.numPlan)

      if (parsedMessage.isPlan) {
        setTimeout(function runPlan() {
          try {
            //console.log($('#btnPlanMess_' + parsedMessage._id).length)
            if ($('#btnPlanMess_' + parsedMessage._id).length > 0) {
              $('#btnPlanMess_' + parsedMessage._id).css("background", 'var(--v-error-darken1)')
              //console.log($('#btnPlanMess_' + parsedMessage._id))
              $('#' + parsedMessage._id).children('.vac-message-box')
                .children('.vac-message-container')
                .children('.vac-message-card').css('border', 'solid 2px var(--v-xr2L2-base)')
              $('#spanPlanMessGC_' + parsedMessage._id).text(' ' + parsedMessage.numPlan)
              $('#divPlanMessGC_' + parsedMessage._id).show()
            } else {
              //console.log('luppp2')
              setTimeout(runPlan, 300);
            }
          } catch (e) {
            //console.log('luppp')
            setTimeout(runPlan, 300);
          }
        }, 300);
      } else {
        setTimeout(function runPlan() {
          try {
            $('#btnPlanMess_' + parsedMessage._id).css("background", 'var(--chat-icon-bg-dropdown-message)')
            $('#' + parsedMessage._id).children('.vac-message-box')
              .children('.vac-message-container')
              .children('.vac-message-card').css('border', 'none')
            $('#divPlanMessGC_' + parsedMessage._id).hide()
          } catch (e) {
            //console.log('luppp')
            setTimeout(runPlan, 300);
          }
        }, 300);
      }
    },

    setColorRnd() {
      var colors = ['#a26400', '#a23900', '#3d2607', '#1b6558',
        '#3e70cc', '#1f3f7e', '#091556', '#2c2e4d',
        '#852525', '#045e37', '#4d0202', '#572626',
        '#79113b', '#0f4821', '#424018', '#464646',
        '#490a3c', '#2f1c27']

      // return colors[Math.floor(Math.random() * 16)];
      return '#ffc033';
    },

    generateAvatar(text, _foregroundColor, _backgroundColor) {
      const canvas = document.createElement("canvas");
      const context = canvas.getContext("2d");

      var foregroundColor = '#3d3d3d'
      var backgroundColor = this.setColorRnd()

      if (_foregroundColor) {
        foregroundColor = _foregroundColor
      }
      if (_backgroundColor) {
        backgroundColor = _backgroundColor
      }

      canvas.width = 200;
      canvas.height = 200;

      // Draw background
      context.fillStyle = backgroundColor;
      context.fillRect(0, 0, canvas.width, canvas.height);

      context.font = "80px Rooftop-Regular";
      context.fillStyle = foregroundColor;
      context.textAlign = "center";
      context.textBaseline = "middle";

      context.fillText(text, canvas.width / 2, canvas.height / 2 + 8);

      return canvas.toDataURL().replace('data:image/png;base64,', '');
    },

    //---------------------------------------------------------------------------------------------------------
    getChatPhoto(src, senderId, nameOper, date, nameDevice) {
      //console.log('зашел в новую функцию')
      This.sheet = true
      let item1 = {
        src: 'https://w.forfun.com/fetch/89/89b1452e43e738be92c573fdebfb1d22.jpeg',
        marksUrl: "none",
        senderId: senderId,
        nameOper: nameOper,
        date: date,
        nameDevice: nameDevice
      }
      let item2 = {
        src: 'https://w.forfun.com/fetch/25/2529ce3d3391789f369c4ec9a07a1b82.jpeg',
        marksUrl: "none",
        senderId: 1,
        nameOper: 'Павел Петрович Залежных',
        date: '20.10.2024 19:05:45',
        nameDevice: 'Samsung GA-12'
      }
      This.screenshots.push(item1, item2)
      //console.log(This.screenshots)
    },
    openAllPhotos() {
      This.sheet = false
      This.openAllPhotosBool = true
      // this.$refs.XrMediaViewer.show(This.screenshots, This.screenshots[0], true)
    },
    deleteSnapFromDB() {
      //console.log('заешл в deleteSnapFromDB')
      if (!this.openAllPhotosBool) {
        //console.log(this.screenshots[this.screenshots.length - 1])
        this.screenshots.splice(this.screenshots.length - 1, 1)
      } else
        this.screenshots.splice(this.itemNum, 1)
      if (this.screenshots.length === 0)
        this.openAllPhotosBool = false;
      if (this.itemNum = this.items.length - 1 && this.itemNum > 0)
        this.itemNum--
      this.deleteFromDB = false
      //console.log(this.screenshots)
      //логика удаления из бд
    },
    demonstrateMouseout() {
      if (event.offsetX > 0 && event.offsetX < 300 && event.offsetY > 0 && event.offsetY < 220)
        return;
      else
        this.showToolbar = false;
    },
    sendToOperator() {
      if (!this.openAllPhotosBool)
        console.log('отправляем последний скриншот')
      else
        console.log('отправляем скриншот', this.itemNum)
      this.sendTo = false
    },
    sendToChekList() {
      if (!this.openAllPhotosBool)
        console.log('отправляем последний скриншот')
      else
        console.log('отправляем скриншот', this.itemNum)
      this.sendTo = false
    },
    sendToChat() {
      if (!this.openAllPhotosBool)
        console.log('отправляем последний скриншот')
      else
        console.log('отправляем скриншот', this.itemNum)
      this.sendTo = false
    },
  }
}
</script>

<style>
#vieweropen {
  cursor: zoom-in;
}


.pTextSearchToText {
  position: absolute;
  left: 43px;
  bottom: 6px;
  height: 30px;
  margin-bottom: 0px !important;
}

.circularATSearch {
  left: 6px;
  position: absolute;
  bottom: 10px;
}

.vacBtnBlobSearch {
  position: fixed;
  height: 36px;
  right: 6px;
  bottom: 6px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  margin-left: 4px;
  border: 1px solid rgb(54, 51, 48);
  background: rgba(255, 255, 255, 0.43);
  padding-left: 5px;

}

.divisSearchToText {
  background: #fff6e1;
  z-index: 1123321333333333333;
  width: 100%;
  height: 48px;
  position: fixed;
  bottom: 0px;
}

.divPlanCss {
  height: 24px;
  white-space: normal;
  color: var(--v-error-darken1);
  font-style: italic;
}

.divPlanCssMy {

}

.iconVacBtnMic {
  color: var(--v-xr2D2-base) !important;
}

.iconVacBtnMicD {
  color: #f44336 !important;
}

.divAudioMicMess {
  width: -webkit-fill-available;
  margin-left: 4px;
}

.pLengthMessage {
  position: absolute;
  font-size: 8px;
  right: 46px;
  top: -8px;
  background: #fff6e1;
  padding: 4px 4px 0px 4px;
}

.vac-icon-textarea {
  margin-left: 2px;
  align-items: flex-end;
}

.v-input__slot.styleATSl {
  height: 36px !important;
  padding-left: 6px !important;
}

.v-input.vselectSettingsAT {
  margin-top: 0px !important;
  margin-bottom: 0px !important;
  max-width: 200px;
  margin-left: 2px;
  /*margin-right: 2px;*/
  margin-right: 1px;
  height: 36px;
}

button.mr-1.btnATSetIsLang {
  margin-top: 13px;
  margin-left: 4px;
  margin-right: 4px !important;
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  border: 1px solid rgb(225, 228, 232) !important;
  padding-top: 6px;
  color: #d4d4d4;
  -webkit-box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
}

button.mr-1.btnATSetIsLangSEND {
  position: absolute;
  padding-top: 4px !important;
  right: -45px;
  margin-top: 4px;
  margin-left: 4px;
  margin-right: 4px !important;
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  border: 1px solid rgb(191 116 18) !important;
  padding-top: 6px;
  color: #bf7412;
  -webkit-box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
}

.divSettLangUpdate {
  position: absolute;
  bottom: 0px;
  width: 100%;
  height: 132px;
  background: #00000078;
  left: 0px;
  padding-left: 6px;
  z-index: 12;
}

.divSettLangUpdate2 {
  border: 1px solid #c5842d !important;
  border-radius: 4px;
  position: absolute;
  bottom: 14px;
  width: calc(100% - 20px);
  height: 106px;
  background: white;
  left: 10px;
  padding-left: 6px;
  z-index: 12;
}

.rowSettLangUpdate {
  width: 100px;
  margin-top: 0px !important;
  margin-bottom: 0px !important;
  margin-right: 1px;
}

.vac-message-card.vac-message-current.messTranslate {
  background: #bf7411 !important;
}

button#idBtnIsEnRuAT {
  height: 22px;
  width: 22px;
  border: none;
  margin: 0;
  background-color: #d7d7d7 !important;
  border-radius: 2px;
  -webkit-box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
  box-shadow: 0 3px 5px -1px rgb(0 0 0 / 0%), 0 6px 10px 0 rgb(0 0 0 / 0%), 0 1px 18px 0 rgb(0 0 0 / 0%);
}

/*.vac-col-messages .vac-send-disabled, .vac-col-messages .vac-send-disabled svg {*/
/*  cursor: none !important;*/
/*  pointer-events: none !important;*/
/*  -webkit-transform: none !important;*/
/*  transform: none !important;*/
/*  margin: 0 2px;*/
/*}*/

.vac-col-messages .vac-icon-textarea-left .vac-wrapper, .vac-col-messages .vac-icon-textarea-left svg, .vac-col-messages .vac-icon-textarea .vac-wrapper, .vac-col-messages .vac-icon-textarea svg {
  margin: 0 2px;
}

.vac-col-messages .vac-icon-textarea-left .vac-wrapper, .vac-col-messages .vac-icon-textarea-left svg {
  margin: 0 0px;
}

div#idVacBtnSendMess {
  border: solid 1px black !important;
}

#vac-icon-paperclip {
  fill: black !important;
}

#vac-icon-send {
  fill: black !important;
}

.vac-col-messages .vac-send-disabled, .vac-col-messages .vac-send-disabled svg {
  cursor: none !important;
  pointer-events: none !important;
  -webkit-transform: none !important;
  transform: none !important;
  margin: 0;
}

.divMainChat {
  position: fixed;
  width: 400px;
  height: 100%;
  background: #ffffff;
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

.vac-card-window {
  height: calc(100% - 1px) !important;
}

.vac-messages-hidden {
  display: inline;
}

.vac-rooms-container.vac-app-border-r {
  display: none !important;
}

.vac-room-header.vac-app-border-b {
  display: none;
}

.vac-loader-wrapper {
  display: none;
}

div#vac-circle {
  display: none;
}

.vac-card-info.vac-card-date {
  display: none;
}

textarea.vac-textarea {
  border-radius: 8px !important;
}

.vac-avatar {
  align-self: flex-start !important;
  margin-top: 12px !important;
}

.vac-message-file-container.vac-image-loading {
  -webkit-filter: blur(0px) !important;
  filter: blur(0px) !important;
}

.vac-message-image.vac-image-loading {
  -webkit-filter: blur(0px) !important;
  filter: blur(0px) !important;
  cursor: zoom-in;
}

.vac-message-image.eventDBL.fullIMG {
  width: 60% !important;
  height: 80% !important;
  max-width: 1000px !important;
  max-height: 1000px !important;
  position: fixed !important;
  top: 10%;
  left: 20%;
  background-size: contain !important;
  z-index: 123;
  cursor: -webkit-zoom-out !important;
  cursor: zoom-out !important;
}

.vac-message-image.vac-image-loading.eventDBL.fullIMG {
  width: 60% !important;
  height: 80% !important;
  max-width: 1000px !important;
  max-height: 1000px !important;
  position: fixed !important;
  top: 10%;
  left: 20%;
  background-size: contain !important;
  z-index: 123;
  cursor: -webkit-zoom-out !important;
  cursor: zoom-out !important;
}

.eventDBL {
  cursor: zoom-in;
}

.vac-loader-wrapper.vac-container-center {
  z-index: 0 !important;
}

.vac-message-box {
  max-width: 80% !important;
}

.vac-offset-current {
  margin-left: 20% !important;
}

.vac-message-box {
  max-width: 80% !important;

}

button.btncloseChatAT.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default {
  border-radius: 0;
  width: 40px;
  height: 40px;
  min-width: 30px;
  min-height: 30px;
  max-width: 30px !important;
  max-height: 30px !important;
  right: 0px;
  position: absolute;
  top: 0px;
  z-index: 30;
  background: white;
  border: 0;
  box-shadow: 0 3px 1px -2px rgb(0 0 0 / 0%), 0 2px 2px 0 rgb(0 0 0 / 0%), 0 1px 5px 0 rgb(0 0 0 / 0%);
}

button.btncloseChat.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default {
  border-radius: 50px;
  width: 40px;
  height: 40px;
  min-width: 30px;
  min-height: 30px;
  max-width: 30px !important;
  max-height: 30px !important;
  right: 6px;
  position: absolute;
  top: 6px;
  z-index: 30;
}

button.btncloseChatimg.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default {
  right: 0;
  border-radius: 50px;
  background: white;
  width: 40px;
  height: 40px;
  min-width: 30px;
  min-height: 30px;
  max-width: 30px !important;
  max-height: 30px !important;
  position: absolute;
  z-index: 101;
  right: 6px;
  top: 6px;
}

.notification__wrapper {
  background: rgba(191, 0, 0, 0.57) !important;
  background-color: rgba(0, 0, 0, 0) !important;
}

p.notification__text {
  -ms-text-overflow: ellipsis;
  text-overflow: ellipsis;
  overflow: hidden;
  -ms-line-clamp: 2;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  display: -webkit-box;
  word-wrap: break-word;
  -webkit-box-orient: vertical;
  box-orient: vertical;
  font-size: 15px;
  line-height: 19px;
  color: black;
}

button.notification__action {
  color: black !important;
  background: white !important;
  border-bottom: solid 1px #02020236 !important;
  margin-right: -12px !important;
}

.notification__content {
  background: #ffffff6b;
  border-bottom-left-radius: 5px !important;
  border-top-left-radius: 5px !important;
}

.notification.notification--active.notification--bottom.notification--right {
  margin-right: 0;
  margin-bottom: 54px;
}

/*.vac-image-buttons {*/
/*  display: none !important;*/
/*}*/

.vac-message-image.eventDBL {
  background-size: contain !important;
}


.divFullImg {
  position: fixed;
  width: calc(60% + 0px);
  height: calc(80% + 1px);
  top: 10%;
  left: 10%;
  background: #dddddd;
  z-index: 100;
}

.imgFullImg {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

svg.vac-icon-microphone {
  fill: #363636 !important;
}

div#idBtnATSendMessage {
  fill: #bf7411 !important;
}

.logoAT {
  border-radius: 30px;
}

.bar-line {
  display: -webkit-box;
  /*display: -ms-flexbox;*/
  display: flex;
  height: 26px;
  margin-right: 2px;
}

.bar-line__left {
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  width: 0px;
  height: 15px;
  padding: 0 !important;
  padding-top: 5px !important;
  margin: 0 !important;
}

.bar-line__right {
  flex: 1;
  width: 16px;
  height: 15px;
}

.bar-line__right-text1 {
  flex: 1;
  width: 20px;
  height: 13px;
}

.bar-line__right-text2 {
  flex: 1;
  width: 20px;
  height: 13px;
}

bodY {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.wave {
  width: 5px;
  height: 40px;
  background: linear-gradient(45deg, #bf7411, #fff);
  margin: 2px;
  -webkit-animation: wave 1s linear infinite;
  animation: wave 1s linear infinite;
  border-radius: 20px;
}

.wave:nth-child(2) {
  animation-delay: 0.1s;
}

.wave:nth-child(3) {
  animation-delay: 0.2s;
}

.wave:nth-child(4) {
  animation-delay: 0.3s;
}

.wave:nth-child(5) {
  animation-delay: 0.4s;
}

.wave:nth-child(6) {
  animation-delay: 0.5s;
}

.wave:nth-child(7) {
  animation-delay: 0.6s;
}

.wave:nth-child(8) {
  animation-delay: 0.7s;
}

.wave:nth-child(9) {
  animation-delay: 0.8s;
}

.wave:nth-child(10) {
  animation-delay: 0.9s;
}

.wave:nth-child(11) {
  animation-delay: 1s;
}

.wave:nth-child(12) {
  animation-delay: 1.1s;
}

.wave:nth-child(13) {
  animation-delay: 1.2s;
}

.wave:nth-child(14) {
  animation-delay: 1.3s;
}

.wave:nth-child(15) {
  animation-delay: 1.4s;
}

.wave:nth-child(16) {
  animation-delay: 1.5s;
}

.wave:nth-child(17) {
  animation-delay: 1.6s;
}

.wave:nth-child(18) {
  animation-delay: 1.7s;
}

.wave:nth-child(19) {
  animation-delay: 1.8s;
}

.wave:nth-child(20) {
  animation-delay: 1.9s;
}

.wave:nth-child(21) {
  animation-delay: 2s;
}

.wave:nth-child(22) {
  animation-delay: 2.1s;
}

.wave:nth-child(23) {
  animation-delay: 2.2s;
}

.wave:nth-child(24) {
  animation-delay: 2.3s;
}

.wave:nth-child(25) {
  animation-delay: 2.4s;
}

.wave:nth-child(26) {
  animation-delay: 2.5s;
}

.wave:nth-child(27) {
  animation-delay: 2.6s;
}

.wave:nth-child(28) {
  animation-delay: 2.7s;
}

.wave:nth-child(29) {
  animation-delay: 2.8s;
}

.wave:nth-child(30) {
  animation-delay: 2.9s;
}

.wave:nth-child(31) {
  animation-delay: 3s;
}

.wave:nth-child(32) {
  animation-delay: 3.1s;
}

.wave:nth-child(33) {
  animation-delay: 3.2s;
}

.wave:nth-child(34) {
  animation-delay: 3.3s;
}

.wave:nth-child(35) {
  animation-delay: 3.4s;
}

.wave:nth-child(36) {
  animation-delay: 3.5s;
}

.wave:nth-child(37) {
  animation-delay: 3.6s;
}

.wave:nth-child(38) {
  animation-delay: 3.7s;
}

.wave:nth-child(39) {
  animation-delay: 3.8s;
}

@keyframes wave {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1);
  }
  100% {
    transform: scale(0);
  }
}

.btnATBar {
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  border: 1px solid rgb(191 116 17);
  background: #ffffff6b;
}

.vacBtnBar {
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  margin-left: 4px;
  border: 1px solid rgb(54, 51, 48);
  background: rgba(255, 255, 255, 0.43);
}

.vacBtnAudioMicDel {
  float: left;
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  margin-left: -10px;
  border: 1px solid rgb(54, 51, 48);
  background: rgba(255, 255, 255, 0.43);
  padding-left: 5px;
}

.vacBtnAudioMic {
  float: right;
  height: 36px;
  width: 36px;
  max-height: 100%;
  border-radius: 4px;
  margin-left: 4px;
  border: 1px solid rgb(54, 51, 48);
  background: rgba(255, 255, 255, 0.43);
  padding-left: 5px;
}

.btnChatBar {
  height: 36px !important;
  width: 36px !important;
  max-height: 100% !important;
  border-radius: 4px !important;
  border: 1px solid black !important;
  background: #ffffff6b !important;
  margin-left: 0px;
  /*margin-right: 3px;*/
  margin-right: 1px;
}

.atb1 {
  position: absolute;
  bottom: 94px;
}

.atb2 {
  position: absolute;

}

.atb3 {
  position: absolute;

}

.mb1 {
  margin-bottom: 4px;
}

.pAT-3-0-0-3 {
  padding: 3px 0px 0px 3px;
}

.pAT-5-0-0-0 {
  padding: 5px 0 0 0;
}

.pAT-5-0-0-4 {
  padding: 5px 0 0 4px;
}

.pAT-5-0-0-5 {
  padding: 5px 0 0 5px;
}

.sendTextAT {
  padding: 6px 0px 2px 6px;
  width: 36px;
}

.sendTextATd {
  padding: 6px 0px 2px 6px;
  width: 36px;
}

.rowAudioAT {
  width: 100px;
  background: #bf74112b;
  margin-left: 4px;
  margin-right: 4px;
  border-radius: 4px;
}

.vac-text-username {
  color: #1a3200 !important;
  margin-right: 20px !important;
}

.vac-text-timestamp {
  color: #1a3200 !important;
}

.circularAT {
  margin-left: -3px;
  margin-top: -2px;
}

.v-select__selections.styleATSl {
  padding-top: 0px !important;
  height: 30px;
}

.v-input__slot.styleATSl {
  min-height: 36px !important;
}

input#idSelectSrcLang {
  height: 0px;
  margin: 0px;
  padding: 0px;
  position: absolute;
  display: none;
}

input#idSelectTgtLang {
  height: 0px;
  margin: 0px;
  padding: 0px;
  position: absolute;
  display: none;
}

div#idVacIconTextareaLeft {
  display: -webkit-box;
  /*display: -ms-flexbox;*/
  display: flex;
  -webkit-box-align: end;
  -ms-flex-align: center;
  align-items: flex-end;
  margin-right: 5px;
}

.atpadding {
  padding: 6px 0px 0px 4px;
}

.vac-button-download {

  height: 0px !important;
  width: 0px !important;
}

.vac-button-view {

  height: 0px !important;
  width: 0px !important;
}

.vac-chat-container {
  /*background: #fff6e1;*/
  background: var(--xr-gc-pane-background-color) !important;
}

.vac-container-scroll {
  /*background: #fff6e1 !important;*/
  background: var(--xr-gc-pane-background-color) !important;
}

.vac-box-footer {
  /*background: #fff6e1 !important;*/
  background: var(--xr-gc-pane-background-color) !important;
}

/*--------------------------------------------------------*/
#idTextAreaMess {
  border-color: rgba(0, 0, 0, .4);
  border-radius: 4px !important;
  padding: 7px !important; /* ширина прыгала у поля */
}

#idTextAreaMess::placeholder {
  color: Silver;
}

#divATrow label {
  background: var(--xr-gc-pane-background-color);
  font-size: 14px !important;
  position: absolute;
  top: 17px !important;
}

#idDivRowSaveAudioAT {
  height: 36px;
  border: solid 1px rgba(0, 0, 0, .4);
}

#idAudioGraphAT {
  height: 36px;
  width: 206px;
}

#idDivInfoSaveBlob {
  float: right;
}

/*---------------------------------------------------------*/
.centerATt {
  display: -webkit-box;
  /*display: -ms-flexbox;*/
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  z-index: 22;
  position: absolute;
  bottom: 3px;
  background: var(--v-xr2L5-base);
  height: 42px;
  left: 6px;
  border: 1px solid #bf7411;
  border-radius: 4px;
  width: 348px;
}

/*----------------------------------------------*/
.bodyAT {
  margin-left: 4px;
  margin-right: 4px;
  display: -webkit-box;
  /*display: -ms-flexbox;*/
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  width: 80px;
  max-height: 46px;
  height: 36px;
}

.vac-dot-audio-record-time {
  width: 52px;
  margin-left: 4px;
  color: #3f3f40;
  margin-top: 6px;
}

/*.vac-col-messages .vac-icon-textarea-left .vac-dot-audio-record-time {*/
/*  color: var(--chat-color);*/
/*  !*margin-left: 0px;*!*/
/*  !*margin-right: 0px;*!*/
/*  !*width: 40px;*!*/
/*  height: 30px;*/
/*  font-size: 10px;*/
/*}*/

/*#idtextStreamMicSave {*/
/*  color: var(--chat-color);*/
/*  margin-left: 1px;*/
/*  margin-right: 0px;*/
/*  width: 50px;*/
/*  height: 30px;*/
/*  font-size: 16px;*/
/*  position: absolute !important;*/
/*  right: 48px !important;*/
/*}*/

/*---------------------------------------------------------*/
/*скролл был для .vac-textarea*/
/*---------------------------------------------------------*/
.divMainChat ::-webkit-scrollbar {
  height: 6px; /* высота для горизонтального скролла */
  width: 6px;
}

.divMainChat ::-webkit-scrollbar-track {
  background: var(--xr-editor-menu-paginator-color);
}

.divMainChat ::-webkit-scrollbar-thumb {
  background: var(--v-xr2L1-base);
  border-radius: 2px;
}

.divMainChat ::-webkit-scrollbar-thumb:vertical:hover,
.divMainChat ::-webkit-scrollbar-thumb:horizontal:hover {
  background: var(--v-xr2L1-base);
}

/*:hover::-webkit-scrollbar-thumb {*/
/*  background: #53ff47;*/
/*}*/
/*:hover::-webkit-scrollbar-thumb {*/
/*  background: #c847ff;*/
/*}*/
.divMainChat :hover::-webkit-scrollbar-thumb {
  background: var(--v-xr2L1-base);
}

/*---------------------------------------------------------*/

.blurFunciksar {
  right: 4px;
  top: 4px;
  left: inherit !important;
  height: 25px !important;
}

.animBtnChatMenu {
  opacity: 0;
  animation: animBtnChat 150ms linear forwards;
}

@keyframes animBtnChat {
  50% {
    opacity: 0;
    right: 0px;
  }
  100% {
    opacity: 1;
    right: 7px;
  }
}

.animBtnChatMenu2 {
  opacity: 0;
  animation: animBtnChat2 350ms linear forwards;
}

.dialog-container >>> .v-dialog__content,
.dialog-container >>> .v-dialog__container {
  position: fixed !important;
  bottom: 10px !important;
  right: 10px !important;
}


@keyframes animBtnChat2 {
  50% {
    opacity: 0;
    right: 0px;
  }
  100% {
    opacity: 1;
    right: 30px;
  }
}

.divInfoSaveBlob{
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

</style>

