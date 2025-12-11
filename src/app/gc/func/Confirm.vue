<!--
Диалог подтверждения Да/Нет
-->
<template>
  <v-dialog
    class="xr-confirm"
    v-model="dialog"
    :max-width="widthDialog"
    persistent
    scrollable
    @keydown.esc="cancel"
    @keydown.enter="agree"
    :style="{ zIndex: options.zIndex }"
  >
    <v-card>
      <v-card-title>
        {{ title }}
      </v-card-title>
      <table>
        <tr>
          <td v-if="options.alert">
            <i style="font-size:40px; color:darkorange; padding-left:10px" class="fas fa-exclamation-triangle"></i>
          </td>
          <td>
            <v-card-text
              class="py-4 my-4"
              v-html="message"
            />
          </td>
        </tr>
      </table>
      <v-divider/>
      <v-card-actions v-if="!isInfo" style="background-color:#f2f2f2">
        <v-spacer></v-spacer>
        <v-btn v-if="isAgree && !isInfo" @click="agree" width="120" color="primary" v-t="'yes'"></v-btn>
        <v-btn v-if="isAgree && !isInfo" @click="cancel" width="120" v-t="'no'"></v-btn>
        <!--        <v-btn @click="cancel" width="120" v-t="'no'"></v-btn>-->
        <v-btn v-if="!isAgree && !isInfo" @click="cancel" width="120" v-t="'сlose'"></v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  data: () => ({
    dialog: false,
    resolve: null,
    reject: null,
    message: null,
    title: null,
    isAgree: false,
    isInfo: false,
    options: {},
    type: 0,
    messageSend: '',
    widthDialog: 570
  }),

  methods: {
    open(title, message, type, options, messageSend) {
      this.dialog = true
      this.title = title
      this.message = message
      this.options = options
      this.messageSend = messageSend
      this.type = type

      if(type == 3){
        this.isInfo = true
      }else{
        this.isAgree = true
        this.isInfo = false
      }
      if (this.options == null){
        this.isInfo = true
        this.options = {}
      }

      try{
        this.options.width = this.options.width != null ? '' + options.width + 'px' : '500px'
        this.options.height = this.options.height != null ? '' + options.height + 'px' : '200px'
        this.options.zIndex = this.options.zIndex == null ? 200 : this.options.zIndex
        this.options.alert = this.options.alert == null ? false : this.options.alert
      }catch (e) {

      }

      if (this.options.rawHtml !== true) {
        this.message = this.message.split("\n").join("<br>")
      }

      return new Promise((resolve, reject) => {
        this.resolve = resolve
        this.reject = reject
      })
    },

    agree() {
      if(this.type == 4){
        this.$emit('agreeSS')
      }else{
        this.$emit('agree', this.type, this.messageSend)
      }

      this.dialog = false
    },

    cancel() {
      this.$emit('cancel')
      this.dialog = false
    }

  }
}
</script>

<style scoped>
.v-dialog > .v-card > .v-card__title {
  padding: 10px 16px;
}
</style>

