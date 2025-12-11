<template>
  <div id="mainChikPanel" class="divChikPanel" style="display: none">
    <chik-dialog ref="chikDialog" place="GC" :isDialog="false" :mediaAttr="mediaAttr" @onclose="onclose"/>
  </div>
</template>
<script>
import ChikDialog from '@/app/ik/ChikDialog.vue'

export default {
  name: "chikPanel",
  components: {'chik-dialog': ChikDialog},
  mounted() {},
  props: {
    mediaAttr: {
      type: Object, default() {
        return {}
      }
    },
  },
  data() {
    return {
      test: { "senderId": 21645,
        "operators": [{
          "roomId": 1019505002,
          "senderId": 21645,
          "label": "Оператор_11",
          "prId": "1113622002",
          "resultId": "1682690963849",
          "stepId": "1113662002"
        } ] }
    }
  },
  computed: {},
  methods: {
    open() {

      console.log('mediaAttr ',this.mediaAttr)

      this.$refs.chikDialog.show({"mode": "NORMAL"})
    },
    onclose() {
      this.$emit('onclose')
    }
  }
}
</script>

<style>

.divChikPanel {
  position: fixed;
  width: 400px;
  height: 100%;
  background: #fff6e1;
  z-index: 91000;
  right: 0px;
  top: 0px;
  padding: 0px;
}

/* отключаем красивый border */
.divChikPanel .xr-chik-dialog .xr-dialog-card {
  border: none;
  /*border-left: ridge 3px #ffc033;*/
  border-radius: 0px;
}

</style>
