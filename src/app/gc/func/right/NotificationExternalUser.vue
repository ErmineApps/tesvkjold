<template>
  <div style="position: absolute; top: 40%; left: 40%; z-index: 1000000; display: none;" id="notificationExternalUser">
      <v-container v-if="itemsUsersInfo.length > 0">
        <v-row align="center" justify="center">
          <v-col>
            <v-card class="mx-auto" max-width="344" color="#000000f">
              <v-card-text>
                <div>
                  <div class="text-h6 mb-1">
                    <v-icon size="27">fa-user</v-icon>
                    {{itemsUsersInfo[0].senderName}}
                  </div>

                  <div class="text-body-2">
                    Данный пользователь ожидает подключения
                  </div>
                </div>
              </v-card-text>

              <v-card-actions>
                <v-row align="center" justify="center">
                  <v-btn
                    variant="flat"
                    width="150"
                    color="#65bf51"
                    class="white--text"
                    @click="acceptUser(itemsUsersInfo[0])"
                  >
                    <span class="text-white"> Принять </span>
                  </v-btn>
                  <v-btn
                    variant="flat"
                    width="150"
                    color="#bf5151"
                    @click="rejectUser(itemsUsersInfo[0])"
                  >
                    Отклонить
                  </v-btn>
                </v-row>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
  </div>

</template>

<script>
var This
export default {
  name: "NotificationExternalUser",

  props: {
    itemsUsersInfoExternal: [],
    // this.itemsUsersInfoExternal[i] = {
    //   icon: 'fa-user',
    //   avatar: '',
    //   color: '#cb5400',
    //   colorText: 'black',
    //   isscrS: false,
    //   isOper: true,
    //   isMy: false,
    //   senderName: arUsersExternal[i].replace(/^\s/g, ''),
    //   senderId: usersActiveExternalSenderID[i].replace(/^\s/g, ''),
    //   isMic: false
    // }

    //TODO передавать по одному внешнему пользователю
  },

  mounted() {
    console.log('МЫ В mounted() ')
    This = this
  },


  data() {
    return {
      itemsUsersInfo: [],
    }
  },

  methods: {
    acceptUser(item) {
      console.log('add user ', item)
      this.$emit('acceptUser', item.senderId, item.senderName)

      this.itemsUsersInfo.splice(0, 1)

      if(this.itemsUsersInfo.length <= 0){
        $('#notificationExternalUser').hide()
      }

    },
    rejectUser(item) {
      this.$emit('rejectUser', item.senderId, item.senderName)

      this.itemsUsersInfo.splice(0, 1)

      if(this.itemsUsersInfo.length <= 0){
        $('#notificationExternalUser').hide()
      }
    },

    show(userExternal){
      console.log('ЗАШЛИ В ПОКАЗ ')
      this.itemsUsersInfo.push(userExternal)

      console.log(this.itemsUsersInfo)
      console.log(this.itemsUsersInfo[0])
      console.log($('#notificationExternalUser'))

      $('#notificationExternalUser').show()

      console.log($('#notificationExternalUser'))
      console.log(document.querySelector('#notificationExternalUser'))
    }
  },
}
</script>




<style>
.notificationHidden{
  visibility: hidden;
}

.notificationVisible{
  visibility: visible;
}

</style>
