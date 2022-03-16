<template>
  <b-card>
    <b-tabs v-model="tabIndex" content-class="mt-3" justified lazy>
      <b-tab title="Coffee">
        <b-overlay :show="coffeeOverlay" rounded="sm">
          <b-col v-for="coffee in coffees" :key="coffee.coffeeId">
            <Coffee
              :coffee="coffee"
              v-on:add:coffee="addToCart"
              v-on:delete:coffee="deleteFromCart"
            />
          </b-col>
        </b-overlay>
      </b-tab>
      <b-tab title="Bakery">
        <b-overlay :show="bakeryOverlay" rounded="sm">
          <b-col v-for="bakery in bakeries" :key="bakery.bakeryId">
            <Bakery
              :bakery="bakery"
              v-on:add:bakery="addToCart"
              v-on:delete:bakery="deleteFromCart"
            />
          </b-col>
        </b-overlay>
      </b-tab>
    </b-tabs>
    <br />
    <div class="d-grid gap-2">
      <b-button block variant="danger" @click="closeMenu()">Close</b-button>
    </div>
  </b-card>
</template>

<script>
import liff from "@line/liff"
import axios from "axios"
import Coffee from '../../components/menu/Coffee.vue'
import Bakery from "../../components/menu/Bakery.vue"

export default {
  components: {
    Coffee,
    Bakery
  },
  data() {
    return {
      liffId: null,
      lineId: '',
      lineName: '',
      pictureURL: '',
      inInClient: false,
      coffees: '',
      bakeries: '',
      tab: 'coffee',
      btnstatus: true,
      overlay: true,
      snackbar: false,
      text: `Added to cart!`,
      coffeeOverlay: true,
      bakeryOverlay: true,
      tabIndex: 0
    }
  },
  methods: {
    async getUserProfile() {
      const { userId, displayName, pictureUrl } = await liff.getProfile()

      this.lineId = userId
      this.lineName = displayName
      this.pictureURL = pictureUrl
    },

    async getProducts() {
      const { data } = await axios.get(`${this.$webhook}/products/${this.lineId}`)
      this.coffees = data.data.coffees
      this.bakeries = data.data.bakeries

      this.coffeeOverlay = false
      this.bakeryOverlay = false
    },

    async addToCart(item) {
      try {
        const data = { data: item, lineId: this.lineId }

        await axios.post(`${this.$webhook}/cart`, data)

        item.qty++
        tthis.text = `${item.name} is added to cart !`
        this.snackbar = true
        this.btnstatus = false
      } catch (error) {
        this.text = error.message
        this.snackbar = true
        this.btnstatus = false
      }
    },

    async deleteFromCart(item) {
      try {
        const data = { data: item, lineId: this.lineId }

        await axios.delete(`${this.$webhook}/cart`, {
          data: data,
        })

        item.qty = 0
        this.text = `${item.name} is deleted from cart !`
        this.snackbar = true
        this.btnstatus = false
      } catch (error) {
        this.text = error.message
        this.snackbar = true
        this.btnstatus = false
      }
    },

    async closeMenu() {
      await liff.sendMessages([{
        type: 'text',
        text: 'แสดงข้อมูลสินค้าในตระกร้า'
      }])

      liff.closeWindow()
    }

  },
  async mounted() {

    (this.$route.query.tab !== undefined) ? this.tab = this.$route.query.tab : this.tab = 'coffee'


    if (this.tab === 'coffee') {
      this.liffId = import.meta.env.VITE_LIFF_ID_COFFEE
    } else {
      this.liffId = import.meta.env.VITE_LIFF_ID_BAKERY
      this.tabIndex = 1
    }

    await liff.init({ liffId: this.liffId, withLoginOnExternalBrowser: true })

    await this.getUserProfile()

    this.getProducts()
  }
}
</script>