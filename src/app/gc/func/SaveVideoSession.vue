<template>
  <div id="rxrssSaveSession"></div>
</template>

<script>
import IKSAR_WEBRTC from '@/assets/ap_kurento/js/iksarGCConnectionWebrtc'
import $ from "jquery";
// import $ from 'jquery'

var This

var stream = stream || {}
var SAVE_STREAM = null
var STREAM_AUDIO_OUT = null
var STREAM_AUDIO_SAVE = null

export default {
  name: "SaveVideoSession",

  props: {
    lang: {
      type: String,
      default: 'ru',
    },
    audioOutputValue: null,
    wsURL:'',
    hasCamera:false
  },

  data() {
    return {
      ws: null,
      iksarWebrtc: IKSAR_WEBRTC,
      roomID: '',
      senderId: '',
      saveNameUser: '',
      ro: true,
    }
  },

  mounted() {
    This = this
    stream.calls = {}
  },

  methods: {

    init(roomID, senderId, _STREAM_AUDIO_SAVE) {

      this.roomID = roomID
      this.senderId = senderId

      let d = new Date;
      this.saveNameUser = senderId+'_'+d.getTime();
      console.log('save init '+roomID+' s '+senderId+' n '+ this.saveNameUser)

      STREAM_AUDIO_SAVE = _STREAM_AUDIO_SAVE

      navigator.mediaDevices.getDisplayMedia({
        video: true,
        audio: true
      }).then(screenStream => {
        this.SAVE_STREAM = screenStream

        console.log("Save url "+`${this.wsURL}/ms/gcSave`)

        var hostname = window.location.host;
        console.log("host "+hostname)
        if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
          This.wsURL = 'wss://'+window.location.host
          console.log("location.hostname "+hostname)
        }


        this.ws = new WebSocket(`${this.wsURL}/ms/gcSave`);
        // this.ws = new WebSocket(`wss://${url}/testGcSave`);
        this.ws.onopen = function () {

          setTimeout(function run() {
            if (This.ws) {
              var message = {
                id: 'ping',
              }
              This.sendMessage(message);
              setTimeout(run, 5000);
            }
          }, 5000);

          This.onExistingParticipants()
        }


        this.ws.onmessage = function (message) {
          var parsedMessage = JSON.parse(message.data);

          if (parsedMessage.id != 'isUsLink')
            // console.info('Received message: ' + message.data);

          switch (parsedMessage.id) {
            case 'startResponse' :
              This.callResponse(parsedMessage)
              break;
            case 'stopSecond' :
              This.closeSecondCall(parsedMessage)
              break;
            case 'pong' :
              This.pong()
              break;
            case 'iceCandidate':
              stream.calls[This.saveNameUser].peer.addIceCandidate(parsedMessage.candidate, function (error) {
                if (error) {
                  console.error("Error adding candidate: " + error);
                  return;
                }
              });
              break;
            default:
              console.error('Unrecognized message', parsedMessage);
          }
        }

        this.ws.addEventListener('close', (evt) => {
          //console.log('ЗАКРЫТИЕ WebSocket с кодом: ' + evt.code);
          this.isInfoServer = false
          switch (evt.code) {
            case 1000: //CLOSE_NORMAL
              console.log("WS-close Правильное закрытие сокета.");
              this.infoCloseWS("WS-close Правильное закрытие сокета.")
              break;

            case 1001: //LWS_CLOSE_STATUS_GOINGAWAY
              console.log('WS-close сервер отключается или браузер перешел от страницы..');
              this.infoCloseWS('WS-close сервер отключается или браузер перешел от страницы..')
              break;

            case 1002: //LWS_CLOSE_STATUS_PROTOCOL_ERR
              this.infoCloseWS('WS-close завершает соединение из-за ошибки протокола..')
              console.log('WS-close завершает соединение из-за ошибки протокола..');
              break;

            case 1003: //LWS_CLOSE_STATUS_UNACCEPTABLE_OPCODE
              console.log('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +
                'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +
                'если она получает двоичное сообщение)..');
              this.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила тип данных, который она не ' +
                'может принять (например, конечная точка, которая понимает только текстовые данные, может отправить это, ' +
                'если она получает двоичное сообщение)..')
              break;

            case 1006: //CLOSE_ABNORMAL
              console.log('WS-close Сервер временно недоступен. Попробуйте снова позднее.');
              this.infoCloseWS('WS-close Сервер временно недоступен. Попробуйте снова позднее.');
              break;

            case 1009: //LWS_CLOSE_STATUS_MESSAGE_TOO_LARGE
              console.log('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.')
              this.infoCloseWS('WS-close конечная точка завершает соединение, поскольку она получила сообщение слишком большого размера для его обработки.')
              break

            case 1011: //LWS_CLOSE_STATUS_UNEXPECTED_CONDITION
              console.log('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.')
              this.infoCloseWS('WS-close сервер завершает соединение, поскольку он столкнулся с неожиданным условием, которое помешало ему выполнить запрос.')
              break;

            default:
              this.infoCloseWS('Нет данных подключения')
              break;
          }
        }, false);
      }).catch((error) => {
        This.closeCall()
      });
    }
    ,

    sendMessage(message) {
      if (this.ws) {
        var jsonMessage = JSON.stringify(message)
        this.ws.send(jsonMessage)
        return true
      } else {
        this.infoCloseWS()
        return false
      }
    }
    ,

    pong(){

    },

    callResponse(message) {
      stream.calls[this.saveNameUser].peer.processAnswer(message.sdpAnswer, function (error) {
        if (error) return console.error(error);
      });
    }
    ,

    onExistingParticipants() {
      stream.calls[this.saveNameUser] = this.newCallData()

      var participant = stream.calls[this.saveNameUser];//new Participant(this.nameUser);

      participant.name = this.saveNameUser

      var video12 = `<video
                  id="${'video_' + this.saveNameUser.replace(/ /g, '')}"
                  class="winVideoLocal"
                  autoplay="">`

      $('#rxrssSaveSession').append(video12);

      this.SAVE_STREAM.getVideoTracks()[0].addEventListener('ended', () => {
        This.$emit("saveClose")
        This.closeCall()
      });



      participant.video = video12
      var constraints = {
        audio: {optional: [{sourceId: this.audioOutputValue}]},
        video: true,
      };

      var options = {
        localVideo: $('#video_' + This.saveNameUser.replace(/ /g, ''))[0],
        video: {
          fps: 24,
          width: 1920,
          height: 1080
        },
        // videoStream: this.SAVE_STREAM,
        name: this.saveNameUser,
        mediaConstraints: constraints,
        onicecandidate: this.onIceCandidate
      }

      if (This.hasCamera) {
        // if (msg.hasCamera) { // это точно неправильно
        // if (This.senderName.substring(0, 6) == 'userON') {
        // if (This.hasCamera) {
        // if (this.videoValue != null) {
        // есть видео
        options = {
          localVideo: $('#video_' + This.senderId)[0],
          video: {
            fps: 24,
            width: 1920,
            height: 1080
          },
          // videoStream: this.SCREEN_STREAM,
          name: This.senderId,
          mediaConstraints: constraints,
          onicecandidate: this.onIceCandidate
        }
      } else {
        // нет видео
        options = {
          videoStream: this.SAVE_STREAM,
          name: This.senderId,
          mediaConstraints: constraints,
          onicecandidate: this.onIceCandidate
        }
      }

      this.startAudioOut()

      let self = this
      participant.peer = new this.iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly(options,
        function (error) {
          if (error) {
            return console.error(error);
          }
          participant.ofTEST = self.onOfferCall;
          participant.ofOPTION = options;
          participant.stopvideo = true
          // this.generateOffer(self.onOfferCall);
          this.generateOffer(participant.ofTEST, self.saveNameUser);
        });
    }
    ,

    startAudioOut(){

      var audioCtx = new (window.AudioContext || window.webkitAudioContext)();

      navigator.getUserMedia = navigator.getUserMedia ||
        navigator.webkitGetUserMedia ||
        navigator.mozGetUserMedia;
      if (navigator.getUserMedia) {
        navigator.getUserMedia({
              audio: {optional: [{sourceId: this.audioOutputValue}]},
              video: false,
          },
          function (stream) {

            STREAM_AUDIO_OUT = stream
            // end fn stream
          },
          function (err) {
            console.log("The following error occured: " + err.name)
          });
      } else {
        console.log("getUserMedia not supported");
      }

    },

    onOfferCall(context, error, offerSdp, name) {
      if (error) {
        return console.error('er '+error)
      }

      offerSdp = this.setCodec(offerSdp);

      This.$emit("startSave")

      let senders = stream.calls[This.saveNameUser].peer.peerConnection.getSenders();
      setTimeout(()=>{
          senders.forEach((sender) => {
            if(sender.track.kind == 'audio'){

              var OutgoingAudioMediaStream = new MediaStream();
              if(This.SAVE_STREAM.getAudioTracks()[0]){
                try {
                  OutgoingAudioMediaStream.addTrack(This.SAVE_STREAM.getAudioTracks()[0]);
                }catch (e) {

                }
              }
              var IncomingAudioMediaStream2 = new MediaStream();
              if(STREAM_AUDIO_SAVE.getAudioTracks()[0]){
                try {
                  IncomingAudioMediaStream2.addTrack(STREAM_AUDIO_SAVE.getAudioTracks()[0]);
                }catch (e) {

                }
              }

              const audioContext = new AudioContext();

              var audioIn_01 = null
              var audioIn_02 = null

              try {
                audioIn_01 = audioContext.createMediaStreamSource(OutgoingAudioMediaStream);
              }catch (e) {

              }
              try {
                audioIn_02 = audioContext.createMediaStreamSource(IncomingAudioMediaStream2);
              }catch (e) {

              }

              var dest = audioContext.createMediaStreamDestination();

              if(audioIn_01){
                audioIn_01.connect(dest);
              }
              if(audioIn_02){
                audioIn_02.connect(dest);
              }

              var FinalStream = dest.stream;

              sender.replaceTrack(FinalStream.getAudioTracks()[0]);
            }else if(sender.track.kind == 'video'){
              sender.track.applyConstraints({
                width: 640,
                height: 380,
                frameRate: 24
              });
              console.log('no error applyConstraints')
              sender.replaceTrack(This.SAVE_STREAM.getVideoTracks()[0]);
              $('#video_' + This.saveNameUser.replace(/ /g, ''))[0].srcObject = This.SAVE_STREAM;
            }
          });
      }, 10)

      this.sendMessage(
        {
          id: 'start',
          name: This.saveNameUser,
          senderId: This.senderId,
          sdpOffer: offerSdp,
          roomID: This.roomID
        }
      );
    }
    ,

    addNewAudioTrack(track){
      let senders = stream.calls[This.saveNameUser].peer.peerConnection.getSenders();

      setTimeout(()=>{
        senders.forEach((sender) => {
          if(sender.track.kind == 'audio'){

            var OutgoingAudioMediaStream = new MediaStream();
            if(sender.track){
              try {
                OutgoingAudioMediaStream.addTrack(sender.track);
              }catch (e) {

              }
            }
            var IncomingAudioMediaStream = new MediaStream();
            if(track){
              try {
                IncomingAudioMediaStream.addTrack(track);
              }catch (e) {

              }
            }

            const audioContext = new AudioContext();

            var audioIn_01 = null
            var audioIn_02 = null

            try {
              audioIn_01 = audioContext.createMediaStreamSource(OutgoingAudioMediaStream);
            }catch (e) {

            }
            try {
              audioIn_02 = audioContext.createMediaStreamSource(IncomingAudioMediaStream);
            }catch (e) {

            }

            var dest = audioContext.createMediaStreamDestination();

            if(audioIn_01){
              audioIn_01.connect(dest);
            }
            if(audioIn_02){
              audioIn_02.connect(dest);
            }

            var FinalStream = dest.stream;

            sender.replaceTrack(FinalStream.getAudioTracks()[0]);
          }
        });
      }, 500)
    },

    newCallData() {
      return {
        ws: null,
        peer: null,
        full: false,
        name: '',
        call_time_timeout: 1000,
        call_time_interval: null,
        call_time: 0,
        call_time_display: null,
        resolution_display: null,
        video: null,
      };
    }
    ,

    setCodec(sdp_offer) {
      return sdp_offer;

      var video_start = sdp_offer.indexOf('m=video');

      var video_config = sdp_offer.substring(video_start, sdp_offer.indexOf('\r\n', video_start));
      var split_start = (video_config.indexOf('SAVPF') + 6);

      var video_base = video_config.substring(0, split_start);

      var browser_name = stream.utility.getBrowserName();

      switch (browser_name) {
        case 'chrome':
          video_base += '100 101 102 123 127 122 125 107 108 109 124';

          break;

        case 'firefox':
          video_base += '126 97';

          break;

        default:
          return sdp_offer;

          break;
      }

      sdp_offer = sdp_offer.replace(video_config, video_base);

      return sdp_offer;
    }
    ,

    onIceCandidate(candidate, c2, c3, c4) {
      if (candidate) {
        var message = {
          id: 'onIceCandidate',
          candidate: candidate,
          name: c2,
        };

        this.sendMessage(message);
      }
    }
    ,

    closeCall() {
      this.sendMessage(
        {
          id: 'stop',
        }
      );


      try{
        this.SAVE_STREAM.getTracks().forEach(function(track) {
          track.stop();
        });
      }catch (e) {}

      This.$emit("saveClose")

      if (this.ws) {
        this.ws.close()
        this.ws = null
      }
      stream.calls[This.saveNameUser].peer.close()
      stream.calls[This.saveNameUser].peer.dispose();
      stream.calls[This.saveNameUser].peer = null;
    },

    closeSecondCall(message) {
      try{
        this.SAVE_STREAM.getTracks().forEach(function(track) {
          track.stop();
        });
      }catch (e) {}

      This.$emit("saveSecondClose", message.userName)

      if (this.ws) {
        this.ws.close()
        this.ws == null
      }
      stream.calls[This.saveNameUser].peer.close()
      stream.calls[This.saveNameUser].peer.dispose();
      stream.calls[This.saveNameUser].peer = null;
    },

    infoCloseWS(text){
      this.closeCall()
    }

  }
}

</script>

<style scoped>

</style>
