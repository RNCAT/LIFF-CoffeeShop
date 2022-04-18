<template>
  <b-card border-variant="primary" class="text-center">
    <h2>{{ reward.name }}</h2>
    <p>points: {{ reward.point }}</p>
    <div class="d-grid gap-2">
      <b-button block variant="primary" @click="selectReward(reward)" :disabled="!isEnough"> แลก </b-button>
    </div>
  </b-card>
</template>

<script>
export default {
  props: ['reward', 'userPoint'],
  methods: {
    selectReward(reward) {
      this.$emit('select:reward', reward)
    },
  },

  computed: {
    isEnough() {
      return this.userPoint >= this.reward.point
    },
  },
}
</script>
