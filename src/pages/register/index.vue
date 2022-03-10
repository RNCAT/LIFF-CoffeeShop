<template>
  <b-card :img-src="profileImage" img-alt="Image" img-top>
    <h5 class="text-center">Customer Info</h5>
    <b-row class="my-2">
      <b-form-input v-model="form.lineName" type="text" placeholder="Line User" disabled required></b-form-input>
    </b-row>
    <b-row class="my-2">
      <b-form-input v-model="form.firstName" type="text" placeholder="Firstname" required></b-form-input>
    </b-row>
    <b-row class="my-2">
      <b-form-input v-model="form.lastName" type="text" placeholder="Lastname" required></b-form-input>
    </b-row>
    <b-row class="my-2">
      <b-form-input v-model="form.phone" type="tel" placeholder="Phone" required></b-form-input>
    </b-row>
    <b-row class="my-2">
      <b-form-textarea
        v-model="form.address"
        placeholder="Address..."
        rows="3"
        max-rows="4"
        required
      ></b-form-textarea>
    </b-row>
    <div class="d-grid gap-2">
      <b-button @click="register" block variant="outline-primary">Register</b-button>
    </div>
  </b-card>
</template>

<script>
import liff from '@line/liff'
import axios from 'axios'
import profileImage from '../../assets/profile.png'

export default {
  data() {
    return {
      liffId: import.meta.env.VITE_LIFF_ID_REGISTER,
      webhook: import.meta.env.VITE_WEBHOOK_API_URI,
      profileImage: '',
      form: {
        lineId: "",
        lineName: "",
        firstName: "",
        lastName: "",
        phone: "",
        address: "",
      },
    }
  },
  methods: {
    async getUserProfile() {
      const { displayName, userId } = await liff.getProfile()

      this.form.lineId = userId
      this.form.lineName = displayName
    },

    async register() {
      await axios.post(`${this.webhook}/user/${this.form.lineId}`, this.form)


      if (!['none', 'external'].includes(liff.getContext().type)) {
        await liff.sendMessages([{ type: 'text', text: 'registered' }])
        liff.closeWindow()
      }
    }
  },
  async mounted() {
    this.profileImage = profileImage

    await liff.init({ liffId: this.liffId })

    if (!liff.isLoggedIn()) liff.login()

    this.getUserProfile()
  }
}
</script>
