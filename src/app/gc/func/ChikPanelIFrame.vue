<template>
  <div id="mainChikPanel" class="divChikPanel divChikPaneliframe">
    <chik-dialog ref="chikDialog" :isDialog="false" place="GC" :mediaAttr="mediaAttr" @onclose="onclose"/>
  </div>
</template>
<script>
import ChikDialog from '@/app/ik/ChikDialog.vue'

export default {
  name: "ChikPanelIFrame",
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
      props: {
        mediaAttr: {
          type: Object, default() {
            return {}
          }
        },
      },
    }
  },
  computed: {},
  methods: {
    open() {
      this.$refs.chikDialog.show({})
    },
    onclose() {
      this.$emit('onclose')
    }
  }
}
</script>

<style>
.divChikPaneliframe{
  position: fixed;
  width: 100%;
  height: 100%;
  background: #fff6e1;
  z-index: 91000;
  right: 0px;
  top: 0px;
  padding: 0px;
}
</style>
