<template>
  <div>
    <b-card bg-variant="dark" text-variant="white" class="text-center">
      <h2 class="text-center">Your points</h2>
      <h1 class="text-center">{{ userPoint }}</h1>
    </b-card>
    <Reward
      v-for="reward in rewardList"
      :key="reward.id"
      :reward="reward"
      :userPoint="userPoint"
      v-on:select:reward="selectReward"
    />
  </div>
</template>

<script>
import liff from '@line/liff'
import axios from 'axios'
import Reward from '../../components/reward/Reward.vue'
import rewardList from '../../assets/mock-reward.json'

export default {
  components: {
    Reward,
  },
  data() {
    return {
      liffId: import.meta.env.VITE_LIFF_ID_REWARD,
      lineId: null,
      userPoint: null,
      rewardList,
    }
  },

  methods: {
    async getUserProfile() {
      const { userId } = await liff.getProfile()

      this.lineId = userId
    },

    async getUserPoint() {
      const { data } = await axios.get(`${this.$webhook}/user/${this.lineId}/points`)

      this.userPoint = data.point

      console.log(this.userPoint)
    },

    async selectReward(reward) {
      await axios.post(`${this.$webhook}/reward`, {
        name: reward.name,
        point: reward.point,
        lineId: this.lineId,
      })

      liff.closeWindow()
    },
  },

  async mounted() {
    await liff.init({ liffId: this.liffId, withLoginOnExternalBrowser: true })

    await this.getUserProfile()

    await this.getUserPoint()
  },
}
</script>

<style scoped>
div {
  margin-top: 10%;
}
</style>
