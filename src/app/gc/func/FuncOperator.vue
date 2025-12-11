<template>
  <div v-if="isShow" id="iDdivMainFuncOper" class="divMainFuncOper">
    <v-row class="vrowMenuFuncOper">
      <v-btn id="idBtnFuncOper_closePanel" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu1')"
             @click="closePanel">
        <v-icon style="font-size: 18px;">fas fa-times</v-icon>
      </v-btn>

      <div class="dividerMenuFuncOper"></div>

      <v-btn id="idBtnFuncOper_freeze" color="white"
             class="ml-6 btnFuncOper"
             :title="isFreeze ? $t('gc.more.menu19') : $t('gc.more.menu18')"
             @click="setFreeze">
        <v-icon v-if="isFreeze" style="position:absolute; font-size: 20px;">far fa-play-circle</v-icon>
        <v-icon v-else style="position:absolute; font-size: 20px;">far fa-stop-circle</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_arrow" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu20')"
             @click="setArrow">
        <v-icon style="font-size: 22px; transform: rotate(45deg);">fas fa-long-arrow-alt-up</v-icon>
        <v-icon v-if="isArrow" style="position:absolute; font-size: 18px;color: white; margin-top: 5px;">fas fa-slash
        </v-icon>
        <v-icon v-if="isArrow" style="position:absolute; font-size: 18px;">fas fa-slash</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu2')"
             @click="setPaint">
        <v-icon style="font-size: 18px;">fas fa-pencil-alt</v-icon>
        <v-icon v-if="isPaint" style="position:absolute; font-size: 18px;color: white; margin-top: 5px;">fas fa-slash
        </v-icon>
        <v-icon v-if="isPaint" style="position:absolute; font-size: 18px;">fas fa-slash</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_size" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu3')"
             @click="setMenuSize(0)">
        <v-icon :style="styleIconSize">fas fa-circle</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_color" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu4')"
             @click="setMenuColor('')">
        <v-icon :style=styleIconColor>fas fa-square</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_clean" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu5')"
             @click="setClear">
        <v-icon style="font-size: 18px;">fas fa-eraser</v-icon>
      </v-btn>

      <div v-if="isPaint || isArrow" class="dividerMenuFuncOper"></div>

      <v-btn id="idBtnFuncOper_paint_scale_plus" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu6')"
             @click="setScale(true)">
        <v-icon style="font-size: 18px;">fas fa-plus</v-icon>
      </v-btn>
      <v-btn v-if="scaleSize>0" id="idBtnFuncOper_paint_scale_size" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu7')"
             @click="">
        <span style="font-size: 16px; font-weight: 900; padding-right: 2px;">{{ scaleSize }}</span>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_scale_minus" color="white"
             class="ml-6 btnFuncOper disabledBtn"
             :title="$t('gc.more.menu8')"
             @click="setScale(false)">
        <v-icon style="font-size: 18px;">fas fa-minus</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_lightbulb" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu9')"
             @click="setLightbuld">
        <img v-if="!isLightbuld" :src="iconFlashlight_on" alt="" style="width: 22px; height: 22px;">
        <img v-if="isLightbuld" :src="iconflashlight_off" alt="" style="width: 22px; height: 22px;">
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_screenshot" :title="$t('gc.more.menu9')"
             class="ml-6 btnFuncOper"
             color="white"
             @click="saveScreenshot">
        <v-icon style="font-size: 18px;">fas fa-camera</v-icon>
      </v-btn>
    </v-row>

    <v-row v-if="isShowMenuSize" class="vrowMenuFuncOperSize">
      <v-btn id="idBtnFuncOper_paint_sett_size1" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu10')"
             @click="setMenuSize(6)">
        <v-icon style="font-size: 6px;">fas fa-circle</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_sett_size2" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu11')"
             @click="setMenuSize(12)">
        <v-icon style="font-size: 12px; ">fas fa-circle</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_sett_size3" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu12')"
             @click="setMenuSize(18)">
        <v-icon style="font-size: 18px;">fas fa-circle</v-icon>
      </v-btn>
      <v-btn id="idBtnFuncOper_paint_sett_size4" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu13')"
             @click="setMenuSize(22)">
        <v-icon style="font-size: 22px;">fas fa-circle</v-icon>
      </v-btn>
    </v-row>
    <v-row v-if="isShowMenuColor" class="vrowMenuFuncOperColor">
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_color1" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu14')"
             @click="setMenuColor('#f60505')">
        <v-icon style="font-size: 22px; color: #f60505">fas fa-square</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_color2" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu15')"
             @click="setMenuColor('#090000')">
        <v-icon style="font-size: 22px; color: #090000">fas fa-square</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_color3" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu16')"
             @click="setMenuColor('#00ac00')">
        <v-icon style="font-size: 22px; color: #00ac00">fas fa-square</v-icon>
      </v-btn>
      <v-btn v-if="isPaint || isArrow" id="idBtnFuncOper_paint_sett_color4" color="white"
             class="ml-6 btnFuncOper"
             :title="$t('gc.more.menu17')"
             @click="showColorPicker">
        <v-icon :style=styleIconColor>fas fa-palette</v-icon>
      </v-btn>
    </v-row>

    <XrColorForm ref="xrColorForm" :toolClass="'classToolColorForlm'" @moveHex="colorMove"
                 @hideHex="colorHide"></XrColorForm>

    <canvas id="idCanvasPrint" class="canvasPrint" :width="wCanvas" :height="hCanvas"></canvas>
    <canvas id="idCanvasPrintArrow" class="canvasPrint" :width="wCanvas" :height="hCanvas"></canvas>

  </div>
</template>

<script>
// const drawingTool = new DrawingTool("#drawing-tool-container");

import flashlight_on from "@/assets/icons/flashlight_on_black_24dp.svg"
import flashlight_off from "@/assets/icons/flashlight_off_black_24dp.svg"
import $ from "jquery";

var This
var mouseLocation

var mouse= {
  is_lmb_pressed: false,
  is_over: false,

  screen: {x: 0, y: 0}, // screen
  location: {x: 0, y: 0}, // canvas
  local: {x: 0, y: 0},	// relative canvas (like x = 0.5 for location.x = 320 & width = 640)

  handler: {
    pointer: null,
    draw: null,
    trail: null,

    timeout: 10, // can't fall behind 10 ( https://www.w3schools.com/jsref/met_win_setinterval.asp )
  },

  setLocation: function (
    x,
    y,
    width,
    height,
    screen_x,
    screen_y
  ) {
    if (typeof screen_x === 'undefined') {
      screen_x = null;
    }

    if (typeof screen_y === 'undefined') {
      screen_y = null;
    }

    this.location = {
      x: x,
      y: y,
    };

    this.local = {
      x: (x / width).toFixed(4),
      y: (y / height).toFixed(4),
    };

    if ((screen_x !== null) && (screen_y !== null)) {
      this.screen = {
        x: screen_x,
        y: screen_y
      };
    }
  }
}

export default {
  name: "FuncOperator",
  components: {},
  props: {
    isExternalUser: false
  },
  data() {
    return {
      isShow: false,
      isShowMenuColor: false,
      isShowMenuSize: false,
      sender: '',
      senderReplace: '',
      room: '',
      mainUser: '',
      isPaint: false,
      isArrow: false,
      isFreeze: false,
      scaleSize: 0,
      isLightbuld: false,
      iconFlashlight_on: flashlight_on,
      iconflashlight_off: flashlight_off,

      styleIconSize: 'font-size: 18px; color: black;',
      styleIconColor: 'font-size: 22px; color: red;',

      pointSize: 12,
      pointColor: '#f60505',

      ctx: null,
      ctxArrow: null,
      painting: false,
      wCanvas: 640,
      hCanvas: 360,

      xStartArrow: 0,
      yStartArrow: 0,

      oDraw: 'L',
      delta: 1.7
    }
  },

  mounted() {
    This = this
    // this.$i18n.locale = this.paramValueLang

    // Обработчик для глобального события mouseup
    document.addEventListener('mouseup', this.globalMouseUp);
  },
  //endregion

  methods: {
    show(sender, room, mainUser, _oDraw, _delta) {
      console.log('showFuncOper ', sender, room, mainUser)
      // $('#idCanvasPrint').hide()
      this.isShow = !this.isShow
      this.sender = sender
      this.senderReplace = sender
      this.room = room
      this.mainUser = mainUser

      this.delta = _delta
      console.log('show delta',this.delta)
      this.oDraw = _oDraw

      this.$emit('funcUpdateIconMenu', this.isShow, this.sender, this.mainUser)
    },

    setDataPhone(_oDraw, _delta){
      this.delta = _delta
      console.log('setDataPhone',this.delta)
      this.oDraw = _oDraw
    },

    closePanel() {
      console.log('closePanel')
      this.scaleSize=0

      this.sendMessage(
        {
          id: 'expert_zoom',
          idTo: this.sender,
          user: this.mainUser,
          room: this.room,
          type: 'previewStart'
        })

      this.sendMessage(
        {
          id: 'expert_zoom',
          idTo: this.sender,
          user: this.mainUser,
          room: this.room,
          type: 'StopZoom'
        })

      this.isLightbuld = false
      this.sendMessage(
        {
          id: 'expert_zoom',
          idTo: this.sender,
          user: this.mainUser,
          room: this.room,
          type: 'FLightOff'
        })

      this.isShowMenuColor = false
      this.isShowMenuSize = false

      this.isShow = false
      this.isPaint = true
      this.isArrow = true
      this.setPaint()
      this.setArrow();
      this.$emit('funcUpdateIconMenu', this.isShow, this.sender, this.mainUser)
    },

    setPaint() {
      this.isPaint = !this.isPaint

      this.$emit('setPaint', this.isPaint, this.sender)

      this.isShowMenuColor = false
      this.isShowMenuSize = false

      if (this.isPaint) {

        this.isArrow = true
        This.setArrow()

        var v = document.getElementById('video_' + this.senderReplace);

        if(this.oDraw == 'L'){

          // if(this.delta >= 2){
          //   this.delta = 1.7
          // }
          console.log( 'setPrint L - h='+this.hCanvas+' w= '+this.wCanvas)
          this.wCanvas = v.offsetWidth;

          console.log('v',v)
          console.log('delta',this.delta)

          console.log('setPrint v.offsetWidth = '+v.offsetWidth)
          console.log('setPrint v.offsetWidth = '+v.offsetHeight)
          console.log('setPrint hCanvas       = '+Math.floor(this.wCanvas / this.delta))

          if(this.delta > 1){
            this.hCanvas = Math.floor(this.wCanvas / this.delta);
          }else{
            this.hCanvas = Math.floor(this.wCanvas * this.delta);
          }

          console.log('setPrint2 delta = '+ this.delta)
          console.log('setPrint2 hCanvas = '+this.hCanvas)

          $('#idCanvasPrint').css('height', this.hCanvas+'px')
          $('#idCanvasPrint').css('width', this.wCanvas+'px')
          $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')

          $('#video_'+This.senderReplace).css('position', 'absolute')
          $('#video_'+This.senderReplace).css('height', this.hCanvas+'px')
          $('#video_'+This.senderReplace).css('width', this.wCanvas+'px')
          $('#video_'+This.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#video_'+This.senderReplace).css('object-fit', 'cover')
          // console.log( 'L -')
          // this.wCanvas = v.offsetWidth;
          // this.hCanvas = Math.floor(this.wCanvas / this.delta);
          // $('#idCanvasPrint').css('height', this.hCanvas+'px')
          // $('#idCanvasPrint').css('width', this.wCanvas+'px')
          // $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          //
          // $('#video_'+this.senderReplace).css('position', 'absolute')
          // $('#video_'+this.senderReplace).css('height', this.hCanvas+'px')
          // $('#video_'+this.senderReplace).css('width', this.wCanvas+'px')
          // $('#video_'+this.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          // $('#video_'+this.senderReplace).css('object-fit', 'cover')
        }
        else{
          console.log( 'P -')
          this.hCanvas = v.offsetHeight;
          // this.wCanvas = Math.floor(this.hCanvas * (9 / 16));
          this.wCanvas = Math.floor(this.hCanvas / this.delta);
          $('#idCanvasPrint').css('height', this.hCanvas+'px')
          $('#idCanvasPrint').css('width', this.wCanvas+'px')
          $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#idCanvasPrint').css('left', 'calc(42.5% - ' + this.wCanvas / 2 + 'px)')

          $('#video_'+this.senderReplace).css('position', 'absolute')
          $('#video_'+this.senderReplace).css('height', this.hCanvas+'px')
          $('#video_'+this.senderReplace).css('width', this.wCanvas+'px')
          $('#video_'+this.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#video_'+this.senderReplace).css('left', 'calc(42.5% - ' + this.wCanvas / 2 + 'px)')
          $('#video_'+this.senderReplace).css('object-fit', 'cover')
        }

        // console.log(this.wCanvas + ' ' + this.hCanvas)
        // $('#idCanvasPrint').css('height', this.hCanvas+'px')
        // $('#idCanvasPrint').css('width', this.wCanvas+'px')
        // $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
        $('#idCanvasPrint').show()

        $('#idCanvasPrint').mousedown((e)=>{This.canvasMousedownStart(e)})
        $('#idCanvasPrint').mouseup((e)=>{This.canvasMouseupFinish(e)})
        $('#idCanvasPrint').mousemove((e)=>{This.canvasMousemoveDraw(e)})
      // @mousedown="canvasMousedownStart"
      // @mouseup="canvasMouseupFinish"
      // @mousemove="canvasMousemoveDraw"

        //largeVideo

        $('#devVideo_' + this.senderReplace).append($('#idCanvasPrint'));

        setTimeout(()=>{
          this.ctx = document.getElementById('idCanvasPrint').getContext('2d');
          this.ctx.lineWidth = this.pointSize;
          // this.ctx.lineCap = 'round';
          this.ctx.strokeStyle = this.pointColor;
          This.ctx.clearRect(0, 0, This.wCanvas, This.hCanvas)
        }, 100)
      }
      else {
        this.setClear()
        //This.ctx.clearRect(0, 0, This.wCanvas, This.hCanvas)

        $('#idCanvasPrint').hide()
        $('#devVideo_' + this.senderReplace).remove($('#idCanvasPrint'));
        this.$emit('setPaint', this.isPaint, this.sender)

        $('#video_'+this.senderReplace).css('position', '')
        $('#video_'+this.senderReplace).css('height','')
        $('#video_'+this.senderReplace).css('width', '100%')
        $('#video_'+this.senderReplace).css('top', '')
        $('#video_'+this.senderReplace).css('object-fit', '')
      }

      $('#idCanvasPrint').mouseleave((e)=>{This.canvasMouseLeave(e)})
      $('#idCanvasPrint').mouseenter((e)=>{This.canvasMouseEnter(e)})
    },

    // заморозка видео оператора
    setFreeze(){
      This.isFreeze = ! This.isFreeze
      if(This.isFreeze){
        this.sendMessage(
          {
            id: 'expert_zoom',
            idTo: this.sender,
            user: this.mainUser,
            room: this.room,
            type: 'previewStop'
          })
      }else{
        this.sendMessage(
          {
            id: 'expert_zoom',
            idTo: this.sender,
            user: this.mainUser,
            room: this.room,
            type: 'previewStart'
          })
      }

    },

    setMenuSize(size) {
      if (size == 0) {
        this.isShowMenuSize = !this.isShowMenuSize
      } else {
        this.pointSize = size
        this.styleIconSize = `font-size: ${this.pointSize}px; color: black;`
        this.isShowMenuSize = false
      }
    },

    setMenuColor(color) {
      if (color == '') {
        this.isShowMenuColor = !this.isShowMenuColor
      } else {
        this.pointColor = color
        this.styleIconColor = `font-size: 22px; color: ${this.pointColor};`
        this.isShowMenuColor = false
      }
    },

    setScale(plus) {
      if (plus) {
        if (this.scaleSize < 4) {
          this.scaleSize++
          this.sendMessage(
            {
              id: 'expert_zoom',
              idTo: this.sender,
              user: this.mainUser,
              room: this.room,
              type: 'In'
            })
        }
      } else {
        if (this.scaleSize > 0) {
          this.scaleSize--
          this.sendMessage(
            {
              id: 'expert_zoom',
              idTo: this.sender,
              user: this.mainUser,
              room: this.room,
              type: 'Out'
            })
        }
      }
      if(this.scaleSize>3){
        $('#idBtnFuncOper_paint_scale_plus').addClass('disabledBtn')
        $('#idBtnFuncOper_paint_scale_minus').removeClass('disabledBtn')
      }else if(this.scaleSize==0){
        $('#idBtnFuncOper_paint_scale_plus').removeClass('disabledBtn')
        $('#idBtnFuncOper_paint_scale_minus').addClass('disabledBtn')
      }else{
        $('#idBtnFuncOper_paint_scale_plus').removeClass('disabledBtn')
        $('#idBtnFuncOper_paint_scale_minus').removeClass('disabledBtn')
      }
    },

    setLightbuld() {
      this.isLightbuld = !this.isLightbuld
      if(this.isLightbuld){
        this.sendMessage(
          {
            id: 'expert_zoom',
            idTo: this.sender,
            user: this.mainUser,
            room: this.room,
            type: 'FLightOn'
          })
      }else{
        this.sendMessage(
          {
            id: 'expert_zoom',
            idTo: this.sender,
            user: this.mainUser,
            room: this.room,
            type: 'FLightOff'
          })
      }
    },

    saveScreenshot(){
      this.$emit('saveScreenshot', this.sender)
    },

    //region Color
    showColorPicker() {
      This.$refs.xrColorForm.onShowHex(this.pointColor)
    },

    colorMove(colorHex) {
      this.pointColor = colorHex
      this.styleIconColor = `font-size: 22px; color: ${this.pointColor};`
    },

    colorHide(colorHex) {
      this.pointColor = colorHex
      this.styleIconColor = `font-size: 22px; color: ${this.pointColor};`
      this.isShowMenuColor = false
    },
    //endregion

    // region Рисование на канве
    setClear(){

      This.sendMessage(
        {
          id: 'clearCanvas',
          idTo: This.sender,
          room: This.room,
          user: This.mainUser,
        })

      if(This.ctx){
        This.ctx.clearRect(0, 0, This.wCanvas, This.hCanvas)
      }
      if(This.ctxArrow){
        This.ctxArrow.clearRect(0, 0, This.wCanvas, This.hCanvas)
      }
    },

    canvasMousedownStart(e) {

      console.log('canvasMousedownStart')
      This.painting = true;

      mouseLocation = {
        x: e.offsetX,
        y: e.offsetY
      }
      this.ctx.beginPath();

      mouse.setLocation(
        mouseLocation.x,
        mouseLocation.y,
        this.wCanvas,
        this.hCanvas
      )

      This.sendMessage(
        {
          id: 'moveDraw',
          status: 'on',
          idTo: This.sender,
          room: This.room,
          user: This.mainUser,
          pointer_color: This.pointColor,
          pointer_width: This.pointSize,
          x: mouse.local.x,
          y: mouse.local.y
        })
    },

    canvasMouseupFinish(e) {
      console.log('canvasMouseupFinish')

      This.ctx.lineTo(e.offsetX, e.offsetY);
      This.ctx.stroke();
      This.painting = false;

      This.sendMessage(
        {
          id: 'moveDraw',
          status: 'off',
          idTo: This.sender,
          room: This.room,
          user: This.mainUser,
          pointer_color: This.pointColor,
          pointer_width: This.pointSize,
          x: mouse.local.x,
          y: mouse.local.y
        })
    },

    canvasMousemoveDraw(e) {
      console.log('canvasMousemoveDraw')
      if (This.painting) {

        This.ctx.lineWidth = This.pointSize;
        // This.ctx.lineCap = 'round';
        This.ctx.strokeStyle = This.pointColor;
        This.ctx.fillStyle = This.pointColor;
        This.ctx.shadowColor = This.pointColor;

        This.ctx.moveTo(e.offsetX, e.offsetY);
        This.ctx.lineTo(mouseLocation.x, mouseLocation.y);
        This.ctx.stroke();

        mouseLocation = {
          x: e.offsetX,
          y: e.offsetY
        }

        mouse.setLocation(
          mouseLocation.x,
          mouseLocation.y,
          this.wCanvas,
          this.hCanvas
        )

        This.sendMessage(
          {
            id: 'moveDraw',
            status: 'on',
            idTo: This.sender,
            room: This.room,
            user: This.mainUser,
            pointer_color: This.pointColor,
            pointer_width: This.pointSize,
            x: mouse.local.x,
            y: mouse.local.y
          })

      }
    },

    sendMessage(message){
      this.$emit('sendMessage', message)
    },
    // endregion

    //region ARROW
    setArrow(){
      This.isArrow = !This.isArrow
      if (this.isArrow) {

        this.isPaint = true
        this.setPaint()

        this.$emit('setArrow', this.isArrow, this.sender)

        var v = document.getElementById('video_' + this.senderReplace);

        if(this.oDraw == 'L'){

          // if(this.delta >= 2){
          //   this.delta = 1.7
          // }
          console.log( 'setPrint L - h='+this.hCanvas+' w= '+this.wCanvas)
          this.wCanvas = v.offsetWidth;

          console.log('v',v)
          console.log('delta',this.delta)

          console.log('setPrint v.offsetWidth = '+v.offsetWidth)
          console.log('setPrint v.offsetWidth = '+v.offsetHeight)
          console.log('setPrint hCanvas       = '+Math.floor(this.wCanvas / this.delta))

          if(this.delta > 1){
            this.hCanvas = Math.floor(this.wCanvas / this.delta);
          }else{
            this.hCanvas = Math.floor(this.wCanvas * this.delta);
          }

          console.log('setPrint2 delta = '+ this.delta)
          console.log('setPrint2 hCanvas = '+this.hCanvas)

          $('#idCanvasPrint').css('height', this.hCanvas+'px')
          $('#idCanvasPrint').css('width', this.wCanvas+'px')
          $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#idCanvasPrintArrow').css('height', this.hCanvas+'px')
          $('#idCanvasPrintArrow').css('width', this.wCanvas+'px')
          $('#idCanvasPrintArrow').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')

          $('#video_'+This.senderReplace).css('position', 'absolute')
          $('#video_'+This.senderReplace).css('height', this.hCanvas+'px')
          $('#video_'+This.senderReplace).css('width', this.wCanvas+'px')
          $('#video_'+This.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#video_'+This.senderReplace).css('object-fit', 'cover')
          // console.log( 'L -')
          // this.wCanvas = v.offsetWidth;
          // this.hCanvas = Math.floor(this.wCanvas / this.delta);
          // $('#idCanvasPrint').css('height', this.hCanvas+'px')
          // $('#idCanvasPrint').css('width', this.wCanvas+'px')
          // $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          //
          // $('#video_'+this.senderReplace).css('position', 'absolute')
          // $('#video_'+this.senderReplace).css('height', this.hCanvas+'px')
          // $('#video_'+this.senderReplace).css('width', this.wCanvas+'px')
          // $('#video_'+this.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          // $('#video_'+this.senderReplace).css('object-fit', 'cover')
        }else{
          console.log( 'P -')
          this.hCanvas = v.offsetHeight;
          // this.wCanvas = Math.floor(this.hCanvas * (9 / 16));
          this.wCanvas = Math.floor(this.hCanvas / this.delta);
          $('#idCanvasPrint').css('height', this.hCanvas+'px')
          $('#idCanvasPrint').css('width', this.wCanvas+'px')
          $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#idCanvasPrint').css('left', 'calc(42.5% - ' + this.wCanvas / 2 + 'px)')
          $('#idCanvasPrintArrow').css('height', this.hCanvas+'px')
          $('#idCanvasPrintArrow').css('width', this.wCanvas+'px')
          $('#idCanvasPrintArrow').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#idCanvasPrintArrow').css('left', 'calc(42.5% - ' + this.wCanvas / 2 + 'px)')

          $('#video_'+this.senderReplace).css('position', 'absolute')
          $('#video_'+this.senderReplace).css('height', this.hCanvas+'px')
          $('#video_'+this.senderReplace).css('width', this.wCanvas+'px')
          $('#video_'+this.senderReplace).css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
          $('#video_'+this.senderReplace).css('left', 'calc(42.5% - ' + this.wCanvas / 2 + 'px)')
          $('#video_'+this.senderReplace).css('object-fit', 'cover')
        }

        // console.log(this.wCanvas + ' ' + this.hCanvas)
        // $('#idCanvasPrint').css('height', this.hCanvas+'px')
        // $('#idCanvasPrint').css('width', this.wCanvas+'px')
        // $('#idCanvasPrint').css('top', 'calc(50% - ' + this.hCanvas / 2 + 'px)')
        $('#idCanvasPrint').show()
        $('#idCanvasPrintArrow').show()

        $('#idCanvasPrintArrow').mousedown((e)=>{This.canvasArrowMousedownStart(e)})
        $('#idCanvasPrintArrow').mouseup((e)=>{This.canvasArrowMouseupFinish(e)})
        $('#idCanvasPrintArrow').mousemove((e)=>{This.canvasArrowMousemoveDraw(e)})
        // @mousedown="canvasMousedownStart"
        // @mouseup="canvasMouseupFinish"
        // @mousemove="canvasMousemoveDraw"


        $('#devVideo_' + this.senderReplace).append($('#idCanvasPrint'));
        $('#devVideo_' + this.senderReplace).append($('#idCanvasPrintArrow'));

        setTimeout(()=>{
          This.ctx = document.getElementById('idCanvasPrint').getContext('2d');
          This.ctx.lineWidth = this.pointSize;
          // this.ctx.lineCap = 'round';
          This.ctx.strokeStyle = this.pointColor;
          This.ctx.clearRect(0, 0, This.wCanvas, This.hCanvas)

          This.ctxArrow = document.getElementById('idCanvasPrintArrow').getContext('2d');
          This.ctxArrow.lineWidth = this.pointSize;
          // this.ctxArrow.lineCap = 'round';
          This.ctxArrow.strokeStyle = this.pointColor;
          This.ctxArrow.clearRect(0, 0, This.wCanvas, This.hCanvas)
        }, 100)
      }
      else {
        this.setClear()
        //This.ctx.clearRect(0, 0, This.wCanvas, This.hCanvas)

        $('#idCanvasPrint').hide()
        $('#idCanvasPrintArrow').hide()
        $('#devVideo_' + this.senderReplace).remove($('#idCanvasPrint'));
        $('#devVideo_' + this.senderReplace).remove($('#idCanvasPrintArrow'));
        this.$emit('setPaint', this.isPaint, this.sender)

        $('#video_'+this.senderReplace).css('position', '')
        $('#video_'+this.senderReplace).css('height','')
        $('#video_'+this.senderReplace).css('width', '100%')
        $('#video_'+this.senderReplace).css('top', '')
        $('#video_'+this.senderReplace).css('object-fit', '')
      }

    },

    canvasArrowMousedownStart(e) {

      console.log('canvasArrowMousedownStart')
      This.painting = true;
      This.ctxArrow.clearRect(0, 0, This.wCanvas, This.hCanvas)

      this.xStartArrow = e.offsetX
      this.yStartArrow = e.offsetY

      mouseLocation = {
        x: e.offsetX,
        y: e.offsetY
      }
      this.ctx.beginPath();

      mouse.setLocation(
        mouseLocation.x,
        mouseLocation.y,
        this.wCanvas,
        this.hCanvas
      )

      // This.sendMessage(
      //   {
      //     id: 'moveDraw',
      //     status: 'on',
      //     idTo: This.sender,
      //     room: This.room,
      //     user: This.mainUser,
      //     pointer_color: This.pointColor,
      //     pointer_width: This.pointSize,
      //     x: mouse.local.x,
      //     y: mouse.local.y
      //   })
    },

    canvasArrowMouseupFinish(e) {
      console.log('canvasMouseupFinish')

      // This.ctx.lineTo(e.offsetX, e.offsetY);
      // This.ctx.stroke();
      This.ctxArrow.clearRect(0, 0, This.wCanvas, This.hCanvas)
      // This.drawFilledArrow(This.ctxArrow, this.xStartArrow, this.yStartArrow , e.offsetX, e.offsetY, 0.14, This.pointColor, This.pointSize)

      This.drawArrowNew(This.ctxArrow, this.xStartArrow, this.yStartArrow , e.offsetX, e.offsetY, This.pointSize, This.pointColor)

      This.painting = false;

      mouse.setLocation(
        this.xStartArrow,
        this.yStartArrow,
        this.wCanvas,
        this.hCanvas
      )
      var X1 = mouse.local.x
      var Y1 = mouse.local.y

      mouse.setLocation(
        e.offsetX,
        e.offsetY,
        this.wCanvas,
        this.hCanvas
        )

      This.sendMessage(
        {
          id: 'arrowDraw',
          idTo: This.sender,
          room: This.room,
          user: This.mainUser,
          type: 1,
          pointer_color: This.pointColor,
          pointer_width: This.pointSize,
          x1: X1,
          y1: Y1,
          x2:mouse.local.x,
          y2:mouse.local.y,
          arrow:0.14
        })
    },

    canvasArrowMousemoveDraw(e) {
      console.log('canvasMousemoveDraw')
      if (This.painting) {

        This.ctxArrow.lineWidth = This.pointSize;
        // This.ctx.lineCap = 'round';
        This.ctxArrow.strokeStyle = This.pointColor;
        This.ctxArrow.fillStyle = This.pointColor;
        This.ctxArrow.shadowColor = This.pointColor;

        This.ctxArrow.clearRect(0, 0, This.wCanvas, This.hCanvas)

        // This.drawFilledArrow(This.ctxArrow, this.xStartArrow, this.yStartArrow , e.offsetX, e.offsetY, 0.14, This.pointColor, This.pointSize)
        This.drawArrowNew(This.ctxArrow, this.xStartArrow, this.yStartArrow , e.offsetX, e.offsetY, This.pointSize, This.pointColor)
        // This.ctx.moveTo(e.offsetX, e.offsetY);
        // This.ctx.lineTo(mouseLocation.x, mouseLocation.y);
        // This.ctx.stroke();

        mouseLocation = {
          x: e.offsetX,
          y: e.offsetY
        }

        mouse.setLocation(
          mouseLocation.x,
          mouseLocation.y,
          this.wCanvas,
          this.hCanvas
        )

        // This.sendMessage(
        //   {
        //     id: 'moveDraw',
        //     status: 'on',
        //     idTo: This.sender,
        //     room: This.room,
        //     user: This.mainUser,
        //     pointer_color: This.pointColor,
        //     pointer_width: This.pointSize,
        //     x: mouse.local.x,
        //     y: mouse.local.y
        //   })

      }
    },

    drawArrowNew(ctx, x0, y0, x1, y1, _width, color){
      let width = _width*2
      let head_width = width*3
      let head_length = width*3

     // console.log('drawArrowNew ',x0, y0, x1, y1, width, color, head_width, head_length)

      // сначала вычислите длину
      const length = Math.sqrt((x1-x0)*(x1-x0)+(y1-y0)*(y1-y0))
      let angle  = Math.atan2(y1-y0, x1-x0);
      // отрегулируйте угол на 90 градусов, так как стрелка, которую мы поворачиваем, повернута на 90 градусов
      angle -= Math.PI / 2;

      let p0 = [x0,y0];

      // порядок будет: p1 -> p3 -> p5 -> p7 -> p6 -> p4 -> p2
      // сформулируйте две базовые точки
      let p1 = [x0 + width / 4, y0];
      let p2 = [x0 - width / 4, y0];

      // верхние базовые точки, которые соединяют заостренный конец с длинной стрелки
      let p3 = [x0 + width / 2, y0 + length - head_length];
      let p4 = [x0 - width / 2, y0 + length - head_length];

      //внешние точки треугольника
      let p5 = [x0 + head_width / 2, y0 + length - head_length-5];
      let p6 = [x0 - head_width / 2, y0 + length - head_length-5];

      // конечная точка стрелки
      let p7 = [x0, y0 + length];

      p1 = this.transform(p1,angle,p0);
      p2 = this.transform(p2,angle,p0);
      p3 = this.transform(p3,angle,p0);
      p4 = this.transform(p4,angle,p0);
      p5 = this.transform(p5,angle,p0);
      p6 = this.transform(p6,angle,p0)
      p7 = this.transform(p7,angle,p0);

      // начать сначала.
      // context.moveTo(p1[0], p1[1]);
      ctx.lineCap = 'round';
      ctx.fillStyle = color;
      ctx.beginPath();
      // отрисовка линий
      ctx.moveTo(p1[0], p1[1]);
      ctx.lineTo(p3[0], p3[1]);
      ctx.lineTo(p5[0], p5[1]);
      ctx.lineTo(p7[0], p7[1]);
      ctx.lineTo(p6[0], p6[1]);
      ctx.lineTo(p4[0], p4[1]);
      ctx.lineTo(p2[0], p2[1]);
      ctx.lineTo(p1[0], p1[1]);
      ctx.closePath();
      // ctx.arc(x0,y0,width/2,angle-Math.PI,angle)
      ctx.arc(x0,y0,width/4,angle-Math.PI,angle)
      ctx.fill();
      //ctx.stroke();
    },

    transform(xy,angle,xy0){
      // поместите x и y относительно x0 и y0, чтобы вращаться вокруг
      const rel_x = xy[0] - xy0[0];
      const rel_y = xy[1] - xy0[1];

      // вычисление повернутых относительных точек
      const new_rel_x = Math.cos(angle) * rel_x - Math.sin(angle) * rel_y;
      const new_rel_y = Math.sin(angle) * rel_x + Math.cos(angle) * rel_y;

      return [xy0[0] + new_rel_x, xy0[1] + new_rel_y];
    },

    drawLine(ctx, x1, y1, x2, y2) {
      ctx.beginPath();
      ctx.moveTo(x1, y1);
      ctx.lineTo(x2, y2);
      ctx.stroke();
    },

    drawHead(ctx, x1, y1, x2, y2, filled) {
      var dx = x2 - x1;
      var dy = y2 - y1;
      ctx.beginPath();
      ctx.moveTo(x1 + 0.5 * dy, y1 - 0.5 * dx); // https://dirask.com/posts/jMqM0j
      ctx.lineTo(x1 - 0.5 * dy, y1 + 0.5 * dx); // https://dirask.com/posts/1GoW61
      ctx.lineTo(x2, y2);
      ctx.closePath();
      filled ? ctx.fill() : ctx.stroke();
    },

    drawArrow(ctx, x1, y1, x2, y2, arrow, filled) {
      if (arrow == null) {
        arrow = 0.1;
      }
      var dx = x2 - x1;
      var dy = y2 - y1;
      var t = 1.0 - arrow;
      var middleX = dx * t + x1;
      var middleY = dy * t + y1;
      This.drawLine(ctx,x1, y1, middleX, middleY);
      This.drawHead(ctx,middleX, middleY, x2, y2, filled);
    },

    drawEmptyArrow(ctx, x1, y1, x2, y2, arrow, color, width) {
      ctx.lineWidth = width;
      ctx.strokeStyle = color;
      This.drawArrow(ctx,x1, y1, x2, y2, arrow, false);
    },

    drawFilledArrow(ctx, x1, y1, x2, y2, arrow, color, width) {
      ctx.lineWidth = width;
      ctx.fillStyle = color;
      ctx.strokeStyle = color;
      This.drawArrow(ctx,x1, y1, x2, y2, arrow, true);
    },

    // _

    canvasMouseLeave(e) {
      //console.log('canvasMouseLeave')
      // Сбрасываем состояние рисования при выходе за пределы canvas
      if (this.painting) {
        this.painting = false;
        this.ctx.beginPath();

        // Отправляем сообщение о завершении рисования
        this.sendMessage({
          id: 'moveDraw',
          status: 'off',
          idTo: this.sender,
          room: this.room,
          user: this.mainUser,
          pointer_color: this.pointColor,
          pointer_width: this.pointSize,
          x: mouse.local.x,
          y: mouse.local.y
        });
      }
    },

    canvasMouseEnter(e) {
      //console.log('canvasMouseEnter')
      // Сбрасываем состояние при входе в canvas
      this.painting = false;
      this.ctx.beginPath();
    },

    canvasArrowMouseLeave(e) {
      console.log('canvasArrowMouseLeave')
      // Сбрасываем состояние рисования стрелки при выходе за пределы canvas
      if (this.painting) {
        this.painting = false;
        this.ctxArrow.clearRect(0, 0, this.wCanvas, this.hCanvas);

        // Отправляем сообщение о завершении рисования стрелки
        this.sendMessage({
          id: 'arrowDraw',
          status: 'cancel',
          idTo: this.sender,
          room: this.room,
          user: this.mainUser
        });
      }
    },

    canvasArrowMouseEnter(e) {
      //console.log('canvasArrowMouseEnter')
      // Сбрасываем состояние при входе в canvas для стрелки
      this.painting = false;
    },

    globalMouseUp(e) {
      if (this.painting) {
        //console.log('globalMouseUp - reset painting')
        this.painting = false;

        // Сбрасываем оба контекста
        if (this.ctx) {
          this.ctx.beginPath();
        }
        if (this.ctxArrow) {
          this.ctxArrow.clearRect(0, 0, this.wCanvas, this.hCanvas);
        }

        // Отправляем сообщения о завершении
        this.sendMessage({
          id: 'moveDraw',
          status: 'off',
          idTo: this.sender,
          room: this.room,
          user: this.mainUser,
          pointer_color: this.pointColor,
          pointer_width: this.pointSize,
          x: mouse.local.x,
          y: mouse.local.y
        });
      }
    },

// обработчик при уничтожении компонента
    beforeDestroy() {
      document.removeEventListener('mouseup', this.globalMouseUp);
    },

    //endregion
  },
}
</script>

<style>
.divMainFuncOper {
  position: absolute;
  left: 0;
  top: calc(50% - 150px);
  width: 54px;
  background: #07070780;
  z-index: 100;
  padding: 4px;
  border-top-right-radius: 5px;
  border-bottom-right-radius: 5px;
}

.vrowMenuFuncOper {

}

.vrowMenuFuncOperSize {
  position: absolute;
  left: 54px;
  top: 193px;
  height: 44px;
  width: 196px;
  background: #00000080;
  border-top-right-radius: 5px;
  border-bottom-right-radius: 5px;
}

.vrowMenuFuncOperColor {
  position: absolute;
  left: 54px;
  top: 237px;
  height: 44px;
  width: 196px;
  background: #00000080;
  border-top-right-radius: 5px;
  border-bottom-right-radius: 5px;
}

button.ml-6.btnFuncOper.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default.white {
  margin: 4px;
  margin-left: 8px !important;
  border-right: 24px;
  padding: 0 !important;
}

.dividerMenuFuncOper {
  margin: 8px;
  height: 1px;
  width: 100%;
  background: #00000050;
}

.classToolColorForlm {
  left: 265px;
}

.canvasPrint {
  position: absolute;
  background: transparent;
  z-index: 1;
  top: 19%;
  width: 1px;
  height: 1px;
  left: 1px;
  cursor: crosshair;
}

button.ml-6.btnFuncOper.v-btn.v-btn--is-elevated.v-btn--has-bg.theme--light.v-size--default.white.disabledBtn{
  pointer-events: none;
  background: #ffffff8a!important;
}
</style>
