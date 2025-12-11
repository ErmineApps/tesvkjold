<template>
  <div id="rxrss"></div>
</template>

<script>
import IKSAR_WEBRTC from '@/assets/ap_kurento/js/iksarGCConnectionWebrtc'
import $ from "jquery";
// import $ from 'jquery'

  var DEFAULT_SAVED_VIDEO_FORMAT = 'webm'
  var This
  var participants = {};

  var stream = stream || {}
  var SCREEN_STREAM = null

  export default {
    name: "RoomXRScreenSharing",
    data() {
      return {
        data: null,
        tabData: [],

        senderId: '',
        senderName: '',
        senderNameOldRep: '',
        numRepetition: 1,

        ws: null,
        isActiveLink: false,
        isStart: true,
        isAvtoStart: false,
        dataInfo: false,

        nameRoom: 'нет имени',

        issenderName: true,

        isCallSessionActive: false,

        paramValueRoom: null,
        paramValueUser: null,
        paramValueLang: this.$route.params.lang,

        iksarWebrtc: IKSAR_WEBRTC,

        ro: true,
        usersCall: 'Нет участников',
        nameRoomBig: '',

        isHeightFooter: false,
        isMic: true,
        isVideoStream: true,

        typeMaket: 1,

        isPhone: false,
        timeNode: '',
        isShowInfo: false,
        numUsers: 0,

        isDialogSett: false,
        audioInputSelect: [],
        audioOutputSelect: [],
        videoSelect: [],

        audioInputValue: null,
        audioOutputValue: null,
        videoValue: null,

        isXrOper: false,
        isXrOperVid: false,

        isAddFunc: false, // для отображения дополнительных функций
        isScreenSharing: false, // для вкл\выкл расшаривания экрана
        wsScreen: null, // веб сокет для расшаривания экрана
        serverUrl: '',
        isDevUrl: false,
        typeBrowser: ''
      }
    },

    props: {
      lang: {
        type: String,
        default: 'ru'
      },
      wsURL:'',
      hasCamera:false
    },

    mounted() {
      This = this
      stream.calls = {}
    },

    methods: {

      init(room, senderName, _typeBrowser, serverUrl, isDevUrl) {

        This.senderName = senderName
        This.senderNameOldRep = senderName
        This.paramValueRoom = room

        This.serverUrl = serverUrl
        This.isDevUrl = isDevUrl
        This.typeBrowser = _typeBrowser
        console.log('_typeBrowser',_typeBrowser)

        // {
        //   video: {
        //     cursor: 'always' | 'motion' | 'never',
        //       displaySurface: 'application' | 'browser' | 'monitor' | 'window'
        //   }
        // }

        // "video": true,
        // "audio": false,
        // "preferCurrentTab": true,
        // "surfaceSwitching": "include"

        navigator.mediaDevices.getDisplayMedia({
          video:{ width: { ideal: 1920, max: 1920 }, height: { ideal: 1080, max: 1080 }, frameRate: 10 },
          audio: true
        }).then(screenStream => {
          this.SCREEN_STREAM = screenStream

          var host = document.location.host;
          var pathname = document.location.pathname;

          var url = ''

          // if (host == 'localhost:8081' || host == '192.168.50.96:8081') {
          // } else {
          //   url = host + pathname
          // }

          var hostname = window.location.host;
          console.log("host "+hostname)
          if (hostname.startsWith('10.') || hostname.startsWith('192.168.') || hostname.startsWith('172.16.')) {
            This.wsURL = 'wss://'+window.location.host
            console.log("location.hostname "+hostname)
          }

          this.ws = new WebSocket(this.wsURL+`/ms/groupcall`);

          this.ws.onopen = function () {

            var message = {
              id: 'isLinkAvailable',
              user: This.senderName,
              room: This.paramValueRoom,
              lang: This.lang,
              urlServer: This.serverUrl,
              isDevUrl: This.isDevUrl
            }

            This.sendMessage(message);
          }


          this.ws.onmessage = function (message) {
            var parsedMessage = JSON.parse(message.data);

            if (parsedMessage.id != 'isUsLink')
              // console.info('Received message: ' + message.data);

            switch (parsedMessage.id) {
              case 'isUsers' :
                This.statusUserCall(parsedMessage)
                break;
              case 'isUsLink' :
                This.statusUsers(parsedMessage)
                break;
              case 'userRepetition':
                This.userRepetition()
                break;
              case 'isLinkAvailable' :
                isLinkLive(parsedMessage)
                break;
              case 'existingParticipants':
                This.onExistingParticipants(parsedMessage);
                break;
              case 'newParticipantArrived':
                This.onNewParticipant(parsedMessage);
                break;
              case 'participantLeft':
                This.onParticipantLeft(parsedMessage);
                break;
              case 'isMicUser':
                This.isMicUser(parsedMessage)
                break
              case 'receiveVideoAnswer':
                This.receiveVideoResponse(parsedMessage);
                break;
              case 'iceCandidate':
                participants[parsedMessage.name].peer.addIceCandidate(parsedMessage.candidate, function (error) {
                  if (error) {
                    console.error("Error adding candidate: " + error);
                    return;
                  }
                });
                break;
              case 'stopCall':
                This.closeCall()
                This.$emit("screenSharingClose")
                break;
              default:
                // console.error('Unrecognized message', parsedMessage);
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
          This.$emit("screenSharingClose")
        });
      }
      ,

      ttt() {
      },


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

      statusUsers(mess) {
      },

      statusUserCall(mess) {

        this.openGC()
      }
      ,

      infoCloseWS() {
        if(this.ws){
          this.ws.close()
        }

        this.isStart = false
        this.isActiveLink = false
      }
      ,

      userRepetition() {
        if(This.senderNameOldRep == ''){
          This.senderNameOldRep = This.senderName
        }
        This.senderName = This.senderNameOldRep+'_'+This.numRepetition+''
        This.numRepetition++

        var message = {
          id: 'joinRoom',
          senderName: this.senderName,
          roomId: this.paramValueRoom,
          serverUrl: this.serverUrl,
          isDevUrl: this.isDevUrl,
          device: This.typeBrowser
        }
        this.sendMessage(message);
      },

      openGC() {
        if (This.senderName == '') {
          this.$root.showErr("Не заполнено поле с именем участника сессии")
        } else {
          this.isCallSessionActive = true

          setTimeout(function run() {
            if (This.paramValueRoom && This.ws) {
              var message = {
                id: 'isUsLink',
                room: This.paramValueRoom,
              }
              This.sendMessage(message);
            }
            setTimeout(run, 1000);
          }, 1000);

          var message = {
            id: 'joinRoom',
            senderName: this.senderName,
            roomId: this.paramValueRoom,
            serverUrl: This.serverUrl,
            isDevUrl: This.isDevUrl,
            device: This.typeBrowser
          }

          console.log('del SS isDevUrl '+This.isDevUrl+' url '+This.serverUrl)
          this.sendMessage(message);
        }
      }
      ,


      onNewParticipant(request) {
        this.receiveVideo(request.senderId);
        // document.getElementById('idSpanNumActiveUsers').innerText = '' + This.numUsers;
      }
      ,

      receiveVideoResponse(result) {
        participants[result.senderId].peer.processAnswer(result.sdpAnswer, function (error) {
          if (error) return console.error(error);
        });
      }
      ,

      callResponse(message) {
        if (message.response != 'accepted') {
          console.info('Call not accepted by peer. Closing call');
          stop();
        } else {
          webRtcPeer.processAnswer(message.sdpAnswer, function (error) {
            if (error) return console.error(error);
          });
        }
      }
      ,

      attachSinkId(element, sinkId) {
        if (typeof element.sinkId !== 'undefined') {
          element.setSinkId(sinkId)
            .then(() => {
              console.log(`Success, audio output device attached: ${sinkId}`);
            })
            .catch(error => {
              let errorMessage = error;
              if (error.name === 'SecurityError') {
                errorMessage = `You need to use HTTPS for selecting audio output device: ${error}`;
              }
              console.error(errorMessage);
              // Jump back to first output device in the list as it's the default.
            });
        } else {
          console.warn('Browser does not support output device selection.');
        }
      },

      onExistingParticipants(msg) {

        This.senderId = msg.senderId

        stream.calls[this.senderId] = this.newCallData()

        var participant = stream.calls[this.senderId];//new Participant(this.senderName);
        participant.name = this.senderName
        participants[this.senderId] = participant;

        var video12 = `<video
                  id="${'video_' + This.senderId}"
                  class="winVideoLocal"
                  autoplay="">`

        $('#rxrss').append(video12);

        this.SCREEN_STREAM.getVideoTracks()[0].addEventListener('ended', () => {
          This.$emit("screenSharingClose")
          This.closeCall()
        });

        participant.video = video12
        var constraints = {
          audio: true,
          video: true,
        };

        var options = {
          localVideo: $('#video_' + This.senderId)[0],
          video: {
            fps: 10,
            width: 1920,
            height: 1080
          },
          name: This.senderId,
          mediaConstraints: constraints,
          onicecandidate: this.onIceCandidate
        }

        if (This.hasCamera) {
          // есть видео
          options = {
            localVideo: $('#video_' + This.senderId)[0],
            video: {
              fps: 10,
              width: 1920,
              height: 1080
            },
            name: This.senderId,
            mediaConstraints: constraints,
            onicecandidate: this.onIceCandidate
          }
        } else {
          // нет видео
          options = {
            videoStream: this.SCREEN_STREAM,
            name: This.senderId,
            mediaConstraints: constraints,
            onicecandidate: this.onIceCandidate
          }
        }

        console.log('SS options ', options)
        console.log('SS senderId '+ This.senderId +' roomID ' + This.roomID)

        let self = this
        participant.peer = this.iksarWebrtc.WebRtcPeer.WebRtcPeerSendonly(options,
          function (error) {
            if (error) {
              return console.error(error);
            }
            participant.ofTEST = self.onOfferCall;
            participant.ofOPTION = options;
            participant.stopvideo = true
            // this.generateOffer(self.onOfferCall);
            this.generateOffer(participant.ofTEST, self.senderId);
          });

        msg.data.forEach(this.receiveVideo);
        This.resizeWin()
      }
      ,

      onOfferCall(context, error, offerSdp, senderId) {
        if (error) {
          return console.log('er')
        }

        offerSdp = this.setCodec(offerSdp);

        console.log('SS offerSdp  ', offerSdp)
        console.log('SS name = >', senderId)
        console.log(senderId)

        console.log('Вызов onOfferCall callback-функции SDP, расшаривание экрана')

        var arr = offerSdp.split('\r\n');
        arr.forEach((str, i) => {
          if (/^a=fmtp:\d*/.test(str)) {
            arr[i] = str + ';x-google-max-bitrate=500;x-google-min-bitrate=0;x-google-start-bitrate=500';
          } else if (/^a=mid:(1|video)/.test(str)) {
            arr[i] += '\r\nb=AS:10000';
          }
        });

        offerSdp = arr.join('\r\n'),

        this.sendMessage(
          {
            id: 'receiveVideoFrom',
            senderId: senderId,
            senderName: senderId,
            //id: this.call_message,
            from: context.call_from,
            to: context.call_to,
            sdpOffer: offerSdp,
            dev: context.dev,
            // nik 24.10.2018
            save_video: context.call_save_video,
            send_video: context.call_send_video,
            saved_video_format: context.call_saved_video_format
          }
        );

        let senders = stream.calls[This.senderId].peer.peerConnection.getSenders();

        setTimeout(()=>{

            senders.forEach((sender) => {
              if(sender.track.kind == 'audio'){
                sender.replaceTrack(This.SCREEN_STREAM.getAudioTracks()[0]);
              }else if(sender.track.kind == 'video'){
                sender.replaceTrack(This.SCREEN_STREAM.getVideoTracks()[0]);
                $('#video_' + This.senderId)[0].srcObject = This.SCREEN_STREAM;
              }
            });

          // senders[0].replaceTrack(This.SCREEN_STREAM.getVideoTracks()[0]);
          // $('#video_' + This.senderName.replace(/ /g, ''))[0].srcObject = This.SCREEN_STREAM;
        }, 10)
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

      leaveRoom() {
        this.sendMessage({
          id: 'leaveRoom'
        });
      }
      ,

      receiveVideo(sender) {
      }
      ,

      audoiColor(audioContext, source) {

      },

      setTypeWin() {

      },

      resizeWin() {
      }
      ,

      newCallData() {
        return {
          ws: null,
          peer: null,
          full: false,

          is_dev: false,
          save_video: true,
          send_video: true,
          saved_video_format: DEFAULT_SAVED_VIDEO_FORMAT,

          name: '',

          call_time_timeout: 1000,
          call_time_interval: null,

          bad_fps_sec: 0,
          bad_quality_sec: 0,

          call_time: 0,

          call_time_display: null,
          resolution_display: null,

          win_div: null,
          win_video_div: null,

          video: null,

          preview: null,
          canvas: null,
          canvas_ctx: null,

          active_mode: null,

          chat: [],
          chat_open: false,

          stats: {
            interval: null,
            timeout: 1000,

            time: 0,

            iceAddress: {
              localAddress: null,
              remoteAddress: null,
            },

            codec: {
              audio: null,
              video: null,
            },

            expert: {
              bitrate: null,
              bandwidth: null, // no firefox

              width: null,
              height: null,
              framerate: null, // no firefox
            },

            operator: {
              bandwidth: null, // no firefox
              bitrate: null, // no chrome

              width: null,
              height: null,
              framerate: null, // no firefox
            },
          },

          mouse: {
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
          },
        };
      }
      ,

      getCurrentTimeString(dots) {
      },

      startTime() {
      },

      closeCall() {
        if (this.ws) {
          this.ws.close()
          This.ws = null
        }

        try{
          this.SCREEN_STREAM.getTracks().forEach(function(track) {
            track.stop();
          });
        }catch (e) {}

      },

      infoShow() {

      },

      dialogSett() {

      },

      isOperVid() {
        this.isXrOperVid = !this.isXrOperVid
        if (this.isXrOperVid) {
          $('#video_' + This.senderId).css('width', '100px')
          $('#video_' + This.senderId).css('height', '100px')
        } else {
          $('#video_' + This.senderId).css('width', '100%')
          $('#video_' + This.senderId).css('height', '100%')
        }


        if (this.typeMaket == 1) {
          this.typeMaket = 2

          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            stream.calls['' + keys[i]].full = false
          }
          setTimeout(() => {
            if ('' + keys[0] != This.senderId) {
              stream.calls['' + keys[0]].full = true
            } else {
              stream.calls['' + keys[1]].full = true
            }

            This.resizeWin()
          }, 1000)

        } else {
          this.typeMaket = 1
          var keys = Object.keys(stream.calls);
          for (var i = 0; i < keys.length; ++i) {
            stream.calls['' + keys[i]].full = false
          }
          setTimeout(() => {
            This.resizeWin()
          }, 1000)
        }
      },

      setAddFunc() {
        this.isAddFunc = !this.isAddFunc
      },

    }
  }

</script>

<style scoped>

</style>
