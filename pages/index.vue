<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8>
      <v-card min-width="400">
        <v-snackbar v-model="snackbar">
          {{ message }}
          <v-btn @click="snackbar = false" color="pink" text>
            Close
          </v-btn>
        </v-snackbar>
        <v-card-title>Nuxt Chat</v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
              v-model="name"
              :counter="16"
              :rules="nameRules"
              label="Name"
              required
            ></v-text-field>
            <v-text-field
              v-model="room"
              :rules="roomRules"
              label="Room"
              required
            ></v-text-field>
            <v-btn
              :disabled="!valid"
              @click="submit"
              color="primary"
              class="mr-4"
            >
              Enter
            </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  layout: 'empty',
  head: {
    title: 'Welcome to chat'
  },
  sockets: {
    connect: () => {
      console.log('socket connected')
    }
  },
  data: () => ({
    valid: true,
    name: '',
    nameRules: [
      (v) => !!v || 'Name is required',
      (v) => (v && v.length <= 16) || 'Name must be less than 16 characters'
    ],
    snackbar: false,
    message: '',
    room: '',
    roomRules: [(v) => !!v || 'Room is required']
  }),

  mounted() {
    const { message } = this.$route.query
    if (message === 'noUser') {
      this.message = 'User not found'
    } else if (message === 'leftChat') {
      this.message = 'You leave Chat'
    }

    this.snackbar = !!this.message
  },

  methods: {
    ...mapMutations(['setUser']),
    submit() {
      if (this.$refs.form.validate()) {
        const user = {
          name: this.name,
          room: this.room
        }

        this.$socket.emit('userJoined', user, (data) => {
          if (typeof data === 'string') {
            console.error(data)
          } else {
            user.id = data.userId
            this.setUser(user)
            this.$router.push('/chat')
          }
        })
      }
    }
  }
}
</script>
