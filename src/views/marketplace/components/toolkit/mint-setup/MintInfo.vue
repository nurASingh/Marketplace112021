<template>
<div>
  <div class="text-black" v-if="item && item.contractAsset">
    <b-alert class="text-small" show :variant="variant()">
      <b-row class="text-small">
        <b-col cols="12">
          <!-- <span class="text-small text-bold">NFT #{{item.contractAsset.nftIndex}} -->
              <!-- Click here to see Blockchain information -->
            <!-- : Edition {{item.contractAsset.tokenInfo.edition}} of {{item.contractAsset.tokenInfo.maxEditions}} -->
          <!-- </span> -->
        </b-col>
        <b-col cols="12">
          <span class="pointer" @click="showRoyalties = !showRoyalties"><b-link router-tag="span" v-b-tooltip.hover="{ variant: 'primary' }"  :title="'Click here to see Royalty Information'">{{item.contractAsset.owner}}<b-icon class="ml-2" font-scale="1.3" icon="question-circle"/></b-link></span>
        </b-col>
      </b-row>
      <b-row v-if="showRoyalties">
        <b-col cols="12" class="mt-3">
          <h6 style="font-weight: 700;">Royalty Payments</h6>
          <ListBeneficiaries :loopRun="loopRun" :item="item" />
        </b-col>
      </b-row>
    </b-alert>
  </div>
</div>
</template>

<script>
import { APP_CONSTANTS } from '@/app-constants'
import ListBeneficiaries from '@/views/marketplace/components/toolkit/ListBeneficiaries'

export default {
  name: 'MintInfo',
  components: {
    ListBeneficiaries
  },
  props: ['item', 'loopRun'],
  data () {
    return {
      showRoyalties: false
    }
  },
  methods: {
    variant () {
      let v = (this.iAmOwner) ? 'warning' : 'light'
      if (this.$route.name === 'asset-by-hash' || this.$route.name === 'asset-by-index') {
        v = (this.iAmOwner) ? 'light' : 'dark'
      }
      return v
    }
  },
  computed: {
    minted: function () {
      return !this.item.contractAsset && this.item.mintInfo && this.item.mintInfo.txId && this.item.mintInfo.txStatus === 'success'
    },
    ttStacksAddress () {
      const tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-stacks-address')
      return (tooltip) ? tooltip[0].text : ''
    },
    publicAssetDetails () {
      return (this.$route.name === 'asset-by-hash' || this.$route.name === 'asset-by-index')
    },
    profile () {
      const profile = this.$store.getters['rpayAuthStore/getMyProfile']
      return profile
    },
    iAmOwner () {
      return this.item.contractAsset && this.item.contractAsset.owner === this.profile.stxAddress
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
