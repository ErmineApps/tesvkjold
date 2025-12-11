<template>
  <div id="time-node" class="timerGC"><p>{{timeNode}}</p></div>
</template>

<script>
import $ from 'jquery'

var This

var deltaTime = 0
var testErrorTime = 0
var timeBegan = null
  , timeStopped = null
  , stoppedDuration = 0
  , started = null
  , running = false;

export default {
  name: "TimerGroupCall",
  props: {},

  data() {
    return {
      timeNode: '',
      closeTimeMain: '',
      closeTimeMainMin: '',
      closeTimeMainSec: '',
    }
  },

  mounted() {
    This = this
  },

  computed: {},

  methods: {
    //region Timer

    setMdeltaTime() {
      var ddt = new Date()
      return ddt.getTime() + deltaTime
    },

    startTimerMain(_dateStartDuty, _deltaTime) {

      deltaTime = _deltaTime

      // console.log('^ dateStartDuty m ' + _dateStartDuty)
      // console.log('^ deltaTime       ' + _deltaTime)
      //
      // console.log('dateStartDuty ' + new Date(_dateStartDuty))
      // console.log('date str delt ' + new Date(_dateStartDuty + _deltaTime))
      // console.log(' ')
      // console.log('delta date ___ ' + new Date(This.setMdeltaTime()))
      // console.log('real  date ___ ' + new Date())


      if (running) return;

      if (timeBegan === null) {
        this.resetTimer();
        timeBegan = new Date(_dateStartDuty + deltaTime);
      }

      if (timeStopped !== null) {
////console.log('TIME тест ')
        testErrorTime = (new Date() - timeStopped);
        if (testErrorTime < 0) {
          testErrorTime = 1
////console.log('TIME тест ОШИБКА БЫЛА ЗДЕСЬ')
        } else {
          stoppedDuration += testErrorTime
        }

      }

      started = setInterval(this.clockRunning, 10);
      running = true;

      let timerId = setTimeout(function tick() {

        if (This.closeTimeMain > This.stopTimeDutyM) {
//console.log('ошибка ожиданият таймера. Не правильное время на устройстве')
          This.stopTimer()
          This.resetTimer()
          var d = new Date()
          This.startTimerMain(d, d)
          timerId = setTimeout(10)
        } else {
          timerId = setTimeout(tick, 1000); // (*)
          if (This.isTimeStart) {
            if (This.closeTimeMainMin != 0 || This.closeTimeMainSec > 3) {
              This.isTimeStart = false
              This.stopTimer()
              This.resetTimer()
              var d = new Date()
              This.startTimerMain(d, d)
            } else {
              This.isTimeStart = false
            }
          }
        }
      }, 1000);
////console.log('close dateStartDuty '+ dateStartDuty)
    },

    stopTimer() {
      if (timeBegan === null) {
        this.resetTimer();
        timeBegan = null;//new Date();
      }

      running = false;
      timeStopped = new Date();
      clearInterval(started);
    },

    resetTimer() {
      running = false;
      clearInterval(started);
      stoppedDuration = 0;
      timeBegan = null;
      timeStopped = null;
      this.timeNode = "00:00";
    },

    clockRunning() {
// var deltime =
// if(deltime<0){
//   //console.log('deltime<0')
//   deltime = new Date().getMilliseconds()
// }

      var currentTime = new Date()
        , timeElapsed = new Date(currentTime - timeBegan - stoppedDuration)
        , hour = timeElapsed.getUTCHours()
        , min = timeElapsed.getUTCMinutes()
        , sec = timeElapsed.getUTCSeconds();
      //, ms = timeElapsed.getUTCMilliseconds()

      if(this.zeroPrefix(hour, 2) == '00'){
        $('#time-node').removeClass('timerGC2')
        $('#time-node').addClass('timerGC')

        this.timeNode =
          // this.zeroPrefix(hour, 2) + ":" +
          this.zeroPrefix(min, 2) + ":" +
          this.zeroPrefix(sec, 2)
      }else{
        $('#time-node').removeClass('timerGC')
        $('#time-node').addClass('timerGC2')

        this.timeNode =
          this.zeroPrefix(hour, 2) + ":" +
          this.zeroPrefix(min, 2) + ":" +
          this.zeroPrefix(sec, 2)
      }
      // this.timeNode =
      //   // this.zeroPrefix(hour, 2) + ":" +
      //   this.zeroPrefix(min, 2) + ":" +
      //   this.zeroPrefix(sec, 2)

      this.closeTimeMain = hour
      this.closeTimeMainMin = min
      this.closeTimeMainSec = sec
    },

    zeroPrefix(num, digit) {
      var zero = '';
      for (var i = 0; i < digit; i++) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },

//endregion
  }
}


</script>

<style>
div.timerGC {
  margin-left: 4px;
  padding-right: 6px;
  padding-top: 4px;
  position: relative;
  font-size: 18px;
  font-weight: 500;
  pointer-events: none;
  height: 30px;
  text-shadow: -1px 0 rgb(0 0 0 / 68%), 0 1px rgb(0 0 0 / 68%), 1px 0 rgb(0 0 0 / 0%), 0 -1px rgb(0 0 0 / 0%);
  /*font-family: sans-serif;*/
  /*color:var(--v-xr2L1-base) !important;*/
  color:var(--v-xr2L3-base) !important;
}

div.timerGC2 {
  margin-left: 0px;
  padding-right: 6px;
  padding-top: 6px;
  position: relative;
  font-size: 14px;
  font-weight: 500;
  pointer-events: none;
  height: 30px;
  text-shadow: -1px 0 rgb(0 0 0 / 68%), 0 1px rgb(0 0 0 / 68%), 1px 0 rgb(0 0 0 / 0%), 0 -1px rgb(0 0 0 / 0%);
  /* font-family: sans; */
  /*color:var(--v-xr2L1-base) !important;*/
  color:var(--v-xr2L3-base) !important;
}
</style>
