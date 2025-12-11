<template>
  <div>
    <video style="display:none" playsinline id="video"></video>
    <img id="idImgBack" :src="$props.background" class="imgFon-sett xr-gc-video-background" alt="">
    <canvas v-show="false" id="canvas-stream-mask" class="videoPreGCvDivV"
            v-bind:style="{'width':'100%', '':''}"></canvas>
    <canvas
      class="videoPreGCvDivV"
      v-show="false"
      id="canvas-stream-mask-2"
      v-bind:style="{'background-image': 'url('+$props.background+')','width':'100%'}"></canvas>
    <!--      v-bind:style="{'background-image': 'url('+$props.background+')','width':'100%'}"></canvas>-->
    <!--          v-bind:style="{'background-image': 'url('+fon1_+')','width':'100%'}"></canvas>-->
    <p id="info"></p>
  </div>
</template>

<script>
// import * as bodyPix from "@tensorflow-models/body-pix";
// import fon1 from "../../assets/img/fon/f1mini.jpg"
// import fon2 from "../../assets/img/fon/f2.jpg"
// import fon3 from "../../assets/img/fon/f3.jpg"
// import fon4 from "../../assets/img/fon/f4.jpg"

export default {
  name: "StreamMask",
  props: {
    await: Boolean,
    show: Boolean,
    multiple: Boolean,
    backgroundBlurAmount: Number,
    background: String,//'https://statically.io/images/misc/clouds.jpg',
    videoConstraints: [Object, Boolean],
    isCall: Boolean,
    nameUser: String,
    isExternalUser: false
  },
  data() {
    return {
      actualSegmentation: null,
      actualSegmentationStep: 0,
      state: {
        video: null
      },
      camera: null,
      instanced: false,
      net: null,
      net_configs: {
        architecture: "MobileNetV1",
        outputStride: 16,
        multiplier: 1,
        quantBytes: 4
      },
      bokeh_configs: {
        backgroundBlurAmount: 100,
        edgeBlurAmount: 10
      },
      // fon1_:fon1,
      // fon2_:fon2,
      // fon3_:fon3,
      // fon4_:fon4,
    };
  },
  async mounted() {
    if (!this.$props.await) {
      this.init();
    }
  },
  created() {
  },
  methods: {
    async init() {
      // if (!this.instanced) {
      //   this.doLoad();
      //   this.net = await bodyPix.load(this.net_configs);
      //   await this.loadVideo(this.camera);
      //   await this.getVideoInputs();
      //   this.segmentBodyInRealTime();
      //   this.instanced = true;
      //   if (this.$props.await) {
      //     return this.getStream();
      //   } else {
      //     return this.getStream();
      //   }
      // }
      // else {
      //   await this.loadVideo(this.camera);
      //   await this.getVideoInputs();
      //   this.segmentBodyInRealTime();
      //   if (this.$props.await) {
      //     return this.getStream();
      //   } else {
      //     return this.getStream();
      //   }
      // }
    },
    //
    //load video
    async loadVideo(cameraLabel) {
      // try {
      //   this.state.video = await this.setupCamera(cameraLabel);
      // } catch (e) {
      //   let info = document.getElementById("info");
      //   info.textContent =
      //     "this browser does not support video capture," +
      //     "or this device does not have a camera";
      //   info.style.display = "block";
      //   throw e;
      // }
      //
      // this.state.video.onloadeddata = () => {
      //   this.state.video.play();
      // };
    },
    //
    //setup camera
    async setupCamera(cameraLabel) {
      // if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
      //   throw new Error(
      //     "Browser API navigator.mediaDevices.getUserMedia not available"
      //   );
      // }
      //
      // const videoElement = this.video;
      //
      // this.stopExistingVideoCapture();
      //
      // let videoConstraints = {};
      // if (!this.$props.videoConstraints) {
      //   videoConstraints = await this.getConstraints(cameraLabel);
      // } else {
      //   videoConstraints = this.$props.videoConstraints;
      // }
      //
      // const stream = await navigator.mediaDevices.getUserMedia({
      //   audio: false,
      //   video: videoConstraints
      // });
      // videoElement.srcObject = stream;
      //
      // return new Promise(resolve => {
      //   videoElement.onloadedmetadata = () => {
      //     videoElement.width = videoElement.videoWidth;
      //     videoElement.height = videoElement.videoHeight;
      //     resolve(videoElement);
      //   };
      // });
    },
    //
    // get constraints
    async getConstraints(cameraLabel) {
      // let deviceId;
      // let facingMode;
      //
      // if (cameraLabel) {
      //   deviceId = await this.getDeviceIdForLabel(cameraLabel);
      //   // on mobile, use the facing mode based on the camera.
      //   facingMode = this.isMobile() ? this.getFacingMode(cameraLabel) : null;
      // }
      //
      // return {deviceId, facingMode};
    },
    isAndroid() {
      // return /Android/i.test(navigator.userAgent);
    },

    isiOS() {
      // return /iPhone|iPad|iPod/i.test(navigator.userAgent);
    },

    isMobile() {
      // return this.isAndroid() || this.isiOS();
    },

    stopExistingVideoCapture() {
      if (this.state.video && this.state.video.srcObject) {
        this.state.video.srcObject.getTracks().forEach(track => {
          track.stop();
        });
        this.state.video.srcObject = null;
      }
    },
    // on mobile, facing mode is the preferred way to select a camera.
    // Here we use the camera label to determine if its the environment or
    // user facing camera
    getFacingMode(cameraLabel) {
      if (!cameraLabel) {
        return "user";
      }
      if (cameraLabel.toLowerCase().includes("back")) {
        return "environment";
      } else {
        return "user";
      }
    },
    async getDeviceIdForLabel(cameraLabel) {
      // const videoInputs = await this.getVideoInputs();
      //
      // for (let i = 0; i < videoInputs.length; i++) {
      //   const videoInput = videoInputs[i];
      //   if (videoInput.label === cameraLabel) {
      //     return videoInput.deviceId;
      //   }
      // }
      //
      // return null;
    },
    async getVideoInputs() {
      // if (!navigator.mediaDevices || !navigator.mediaDevices.enumerateDevices) {
      //   return [];
      // }
      //
      // const devices = await navigator.mediaDevices.enumerateDevices();
      //
      // const videoDevices = devices.filter(
      //   device => device.kind === "videoinput"
      // );
      //
      // return devices;
    },
    //
    //
    // segmentBodyInRealTime() {
    //
    //   const canvas1 = this.c1
    //   const canvas2 = this.c2
    //
    //   const idImgBack = document.getElementById("idImgBack");
    //
    //
    //   const video = this.state.video;
    //   // since images are being fed from a webcam
    //   const flipHorizontal = false;
    //
    //   const backgroundBlurAmount1 = 11;
    //   const backgroundBlurAmount2 = 100;
    //
    //   // const edgeBlurAmount = this.bokeh_configs.edgeBlurAmount;
    //   const edgeBlurAmount1 = 7;
    //   const edgeBlurAmount2 = 7;
    //
    //
    //   const bodySegmentationFrame = async () => {
    //     if (this.$props.background == '') {
    //       let multiPersonSegmentation = await this.estimateSegmentation();
    //
    //       bodyPix.drawBokehEffect(
    //         canvas1,
    //         video,
    //         multiPersonSegmentation,
    //         backgroundBlurAmount1,
    //         edgeBlurAmount1,
    //         flipHorizontal
    //       );
    //     }
    //     else {
    //
    //       let multiPersonSegmentation = await this.estimateSegmentation();
    //
    //       const maskBackground = true;
    //
    //       const foregroundColor = {r: 0, g: 0, b: 0, a: 0};
    //       const backgroundColor = this.$props.background //{r: 0, g: 0, b: 0, a: 255};
    //       const backgroundDarkeningMask = bodyPix.toMask(
    //         multiPersonSegmentation, foregroundColor, backgroundColor);
    //
    //       const maskBlurAmount = 3;
    //       const flipHorizontal = false;
    //
    //       bodyPix.drawMask(
    //         canvas2, video, backgroundDarkeningMask, 1, maskBlurAmount, flipHorizontal);
    //
    //       if(this.$props.isCall){
    //         var wOf = document.getElementById('video_' + this.$props.nameUser).offsetWidth
    //         var hOf = document.getElementById('video_' + this.$props.nameUser).offsetHeight
    //       }else{
    //         var wOf = document.getElementById("videoPreGC").offsetWidth
    //         var hOf = document.getElementById("videoPreGC").offsetHeight
    //       }
    //
    //
    //       // document.getElementById("canvas-stream-mask-2").getContext('2d').drawImage(video, 0, 0, wOf, hOf);
    //       var imageData = document.getElementById("canvas-stream-mask-2").getContext('2d').getImageData(0, 0, wOf, hOf);
    //       var pixel = imageData.data;
    //
    //       // for (var p = 0; p < pixel.length; p += 4) {
    //       //   try {
    //       //     if (multiPersonSegmentation.data[p / 4] == 0) {
    //       //       pixel[p + 3] = 0;
    //       //     }
    //       //   } catch (e) {
    //       //
    //       //   }
    //       // }
    //
    //
    //       // const segmentation  =  await this.segmentPerson(video, {
    //       //   internalResolution: "full",
    //       //   segmentationThreshold: 0.2,
    //       //   nmsRadius: 1
    //       // });
    //       // const segmentation = await this.net.segmentPerson(video, {
    //       //   internalResolution: 1,
    //       // })
    //       //
    //       // const foregroundColor = { r: 0, g: 0, b: 0, a: 0 } // red
    //       // const backgroundColor = { r: 0, g: 255, b: 0, a: 255 } // green this.$props.background//
    //       // const coloredPartImage = bodyPix.toMask(
    //       //   segmentation, // segmentation of the image
    //       //   foregroundColor, // forground color
    //       //   backgroundColor
    //       // )
    //       //
    //       // const opacity = 0.7 // Opacity of the color and the original image
    //       // const maskBlurAmount = 4 // Value for blur amount between the two segments forground and background.
    //
    //       // bodyPix.drawMask(
    //       //   canvas2,
    //       //   video,
    //       //   coloredPartImage,
    //       //   opacity,
    //       //   maskBlurAmount,
    //       //   flipHorizontal
    //       // )
    //
    //       // for (var p = 0; p<pixel.length; p+=4)
    //       // {
    //       //   try{
    //       //     if (multiPersonSegmentation.data[p/4] == 0) {
    //       //       pixel[p+3] = 0;
    //       //     }
    //       //   }catch (e){
    //       //
    //       //   }
    //       // }
    //
    //       // document.getElementById("canvas-stream-mask-2").getContext('2d').imageSmoothingEnabled = true;
    //       // document.getElementById("canvas-stream-mask-2").getContext('2d').putImageData(imageData,0,0);
    //
    //
    //       // $(`#canvas-stream-mask-2`).show()
    //
    //
    //       // var imageData = document.getElementById("canvas-stream-mask-2").getContext("2d").getImageData(0,0, canvas2.targetWidth, canvas2.targetHeight);
    //       // var pixel = imageData.data;
    //
    //       // var wOf = 320
    //       // var hOf = 240
    //       // // var wOf = document.getElementById("videoPreGCSett").offsetWidth
    //       // // var hOf = document.getElementById("videoPreGCSett").offsetHeight
    //       //
    //       //
    //       // this.net.segmentPerson(video,{
    //       //   maxDetections: 5,
    //       //   internalResolution: "low",
    //       //   segmentationThreshold: 0.8
    //       // }).catch(err=>{}).then(personsegmentation=>{
    //       //
    //       //
    //       //   // bodyPix.drawBokehEffect(
    //       //   //   canvas2,
    //       //   //   video,
    //       //   //   multiPersonSegmentation,
    //       //   //   backgroundBlurAmount1,
    //       //   //   edgeBlurAmount1,
    //       //   //   flipHorizontal
    //       //   // );
    //       //
    //       // });
    //
    //
    //       // for (var p = 0; p <pixel.length; p+=4)
    //       // {
    //       //   if (multiPersonSegmentation.data [p / 4] == 0) {
    //       //     pixel [p + 3] = 0;
    //       //   }
    //       // }
    //       // this.ctx2.imageSmoothingEnabled = true;
    //       // this.ctx2.putImageData (imageData, 0,0);
    //
    //       // bodyPix.drawBokehEffect(
    //       //   canvas2,
    //       //   video,
    //       //   multiPersonSegmentation,
    //       //   backgroundBlurAmount1,
    //       //   edgeBlurAmount1,
    //       //   flipHorizontal
    //       // );
    //
    //     }
    //
    //     requestAnimationFrame(bodySegmentationFrame);
    //   };
    //
    //   bodySegmentationFrame();
    // },
    //
    // async estimateSegmentation() {
    //   return await this.net.segmentPerson(this.state.video, {
    //     maxDetections: 7,
    //     internalResolution: "high",
    //     segmentationThreshold: 0.8
    //   });
    // },
    //
    // async estimateSegmentation2() {
    //   return await this.net.segmentPerson(this.state.video, {
    //     maxDetections: 5,
    //     internalResolution: "medium",
    //     segmentationThreshold: 0.8
    //   });
    // },
    //
    // getStream() {
    //   if (this.$props.background == '') {
    //     return this.c1.captureStream(20);
    //     // return this.c1.focus(true)
    //   } else {
    //     return this.c2.captureStream(20);
    //     // return this.c2.focus(true)
    //   }
    //
    //   // return this.video.captureStream(20);
    // },
    // stop() {
    //   this.stopExistingVideoCapture();
    // },
    // doLoad() {
    //   this.video = document.getElementById("video");
    //   this.c1 = document.getElementById("canvas-stream-mask");
    //   this.ctx1 = this.c1.getContext("2d");
    //   this.c2 = document.getElementById("canvas-stream-mask-2");
    //   this.ctx2 = this.c2.getContext("2d");
    // }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#canvas-stream-mask-2 {
  /*background-repeat: no-repeat;*/
  transform: scaleX(-1);
}

h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  /*display: inline-block;*/
  margin: 0 10px;
}

a {
  color: #42b983;
}

#canvas-stream-mask {
  /*transform: scaleX(-1);*/
}

.videoPreGCvDivV {
  object-fit: cover;
  height: 100%;
}

</style>
