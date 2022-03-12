<template>
  <b-card
    :img-src="bakery.imageUrl"
    img-alt="Image"
    img-top
    :title="bakery.name"
    :sub-title="bakeryPrice"
    class="mb-2"
  >
    <br />
    <div class="d-grid gap-2">
      <b-button block variant="primary" @click="addToCart(bakery)">Add to cart</b-button>
      <b-button block variant="danger" @click="deleteFromCart(bakery)" v-if="bakery.qty > 0">Remove</b-button>
    </div>
  </b-card>
</template>

<script>
export default {
  props: ['bakery'],
  data() {
    return {
      bakeryPrice: `฿${this.bakery.price}`
    }
  },
  methods: {
    async addToCart(item) {
      this.$emit('add:bakery', item)
    },
    async deleteFromCart(item) {
      this.$emit('delete:bakery', item)
    }
  },
}
</script>