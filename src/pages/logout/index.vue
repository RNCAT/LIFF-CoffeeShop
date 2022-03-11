<template>
  <h1 class="text-center">Logged Out!</h1>
</template>

<script>
import liff from '@line/liff'
import axios from 'axios'

export default {
  data() {
    return {
      liffId: import.meta.env.VITE_LIFF_ID_LOGOUT,
    }
  },
  methods: {
    async logout(userId) {
      await axios.delete(`${this.$webhook}/user/${userId}`)

      if (!['none', 'external'].includes(liff.getContext().type)) {
        await liff.sendMessages([{ type: 'text', text: 'logged out' }])
        liff.closeWindow()
      }
    },
  },
  async mounted() {
    await liff.init({ liffId: this.liffId })

    if (!liff.isLoggedIn()) liff.login()

    const { userId } = await liff.getProfile()

    await this.logout(userId)
  },
}
</script>
