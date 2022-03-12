<template>
  <b-card>
    <b-tabs content-class="mt-3" justified lazy>
      <b-tab title="Coffee" active>
        <p>Coffee</p>
        <b-overlay :show="coffeeOverlay" rounded="sm">
          <b-col v-for="coffee in coffees" :key="coffee.coffeeId">
            <Coffee :coffee="coffee" />
          </b-col>
        </b-overlay>
      </b-tab>
      <b-tab title="Bakery">
        <p>Bakery</p>
      </b-tab>
    </b-tabs>
  </b-card>
</template>

<script>
import liff from "@line/liff"
import axios from "axios"
import Coffee from '../../components/menu/Coffee.vue'

export default {
  components: {
    Coffee
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
    }
  },
  methods: {
    async getUserProfile() {
      const { userId, displayName, pictureUrl } = await liff.getProfile()

      this.lineId = userId
      this.lineName = displayName
      this.pictureURL = pictureUrl
    },

  },
  async mounted() {
    const { data } = await axios.get('https://my-json-server.typicode.com/RNCAT/fakedb/Products')
    this.coffees = data.coffees
    this.bakeries = data.bakeries


    this.coffeeOverlay = false
  }
}
</script>