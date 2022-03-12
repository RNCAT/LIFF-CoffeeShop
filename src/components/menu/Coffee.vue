<template>
  <b-card
    :img-src="coffee.imageUrl"
    img-alt="Image"
    img-top
    :title="coffee.name"
    :sub-title="priceText"
    class="mb-2"
  >
    <b-row>
      <b-col md="8">
        <b-form-radio v-model="coffee.type" name="some-radios" value="hot">Hot</b-form-radio>
        <b-form-radio v-model="coffee.type" name="some-radios" value="ice">Ice</b-form-radio>
      </b-col>
      <b-col md="4">
        <b-form-select v-model="coffee.sweet" :options="sweetOptions" size="sm" class="mt-3"></b-form-select>
      </b-col>
    </b-row>
    <br />
    <b-card-text v-if="coffee.qty > 0">Quantity: {{ coffee.qty }}</b-card-text>
    <div class="d-grid gap-2">
      <b-button
        block
        variant="primary"
        @click="addToCart(coffee)"
        :disabled="!coffee.type || !coffee.sweet || coffee.sweet === 1"
      >Add to cart</b-button>
      <b-button block variant="danger" @click="deleteFromCart(coffee)" v-if="coffee.qty > 0">Remove</b-button>
    </div>
  </b-card>
</template>

<script>
export default {
  props: ['coffee', 'btnstatus'],
  data() {
    return {
      sweetOptions: [
        { value: null, text: 'Sweet' },
        { value: '0', text: '0%' },
        { value: '50', text: '50%' },
        { value: '100', text: '100%' },
        { value: '150', text: '150%', },
      ],
      priceText: `฿${this.coffee.price}`
    }
  },
  methods: {
    async addToCart(item) {
      this.$emit('add:coffee', item)
      console.log(this.coffee.sweet);
    },
    async deleteFromCart(item) {
      this.$emit('delete:coffee', item)
    }
  },
}
</script>