<template>
<b-container>
  <b-row class="p-5 row mt-2">
    <b-col cols="12" style="text-align: center;">
      <h1> Confirm </h1>
      <h4 style="font: normal normal 500 16px Montserrat"> You're going to buy</h4>
      <h4> {{gaiaAsset.name}}</h4>
      <h4 style="margin-top: 20px;"><span class="mr-5 text-black"> For {{contractAsset.saleData.buyNowOrStartingPrice}} STX</span></h4>
    </b-col>
    <b-col cols="12" class=" ">
      <RatesListing :message="''" :amount="contractAsset.saleData.buyNowOrStartingPrice"/>
    </b-col>
    <b-row class="text-small mt-5">
      <b-col cols="3" class="text-right">
        Transfer from:
      </b-col>
      <b-col cols="9">
        <span class="stx-address">{{contractAsset.owner}}</span>
      </b-col>
      <b-col cols="3" class="text-right">
        to
      </b-col>
      <b-col cols="9">
        <span class="stx-address">{{profile.stxAddress}}</span>
      </b-col>
    </b-row>
    <b-col class="mt-5" cols="12" v-if="iAmOwner">
      You own this NFT!
    </b-col>
    <b-col class="mt-5" cols="12" v-else>
      <b-button class="button" @click="$emit('buyNow')">BUY NOW</b-button>
    </b-col>
  </b-row>
</b-container>
</template>

<script>
import RatesListing from '@/views/marketplace/components/toolkit/RatesListing'
import { APP_CONSTANTS } from '@/app-constants'

export default {
  name: 'PurchaseBuyNow',
  components: {
    RatesListing
  },
  props: ['contractAsset', 'gaiaAsset'],
  data () {
    return {
      loading: true,
      formSubmitted: false,
      errorMessage: null,
      defaultRate: null
    }
  },
  mounted () {
    this.loading = false
  },
  methods: {
    rateMessage: function () {
      return 'Buy now for' + this.contractAsset.saleData.buyNowOrStartingPrice + ' STX'
    }
  },
  computed: {
    profile () {
      const profile = this.$store.getters['rpayAuthStore/getMyProfile']
      return profile
    },
    fbet () {
      return this.$store.getters[APP_CONSTANTS.KEY_FORMATTED_BIDDING_END_TIME](this.contractAsset)
    },
    iAmOwner () {
      return this.contractAsset && this.contractAsset.owner === this.profile.stxAddress
    }
  }
}
</script>
<style lang="scss" scoped>
.input-group-text {
  background: #fff;
  color: #000;
}
.button{
  margin: auto;
}
</style>
