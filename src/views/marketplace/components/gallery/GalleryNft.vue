<template>
  <b-link class="galleryNFTContainer" :to="assetUrl" v-if="item && item.contractAsset && item.attributes">
    <MediaItemGeneral :classes="'nftGalleryView'" v-on="$listeners" :options="videoOptions" :mediaItem="item.attributes"/>
    <div class="nftGalleryViewText itemHover">
      <h4 style="overlow:clip; font-weight: 500">{{!item.name ? "NFT" : item.name}}</h4>
      <p class="galleryNftDetails">By:
        {{!item.artist ? "An Anonymous User!" : item.artist}}
        <span>{{item.contractAsset.saleData.buyNowOrStartingPrice === 0 ? "Not on Sale" : item.contractAsset.saleData.buyNowOrStartingPrice + " STX"}} </span>
        </p>
    </div>
  </b-link>
  <!-- <div class="">
    <div class="mt-2 mb-2">
      <div v-if="item.contractAsset">
        #{{item.contractAsset.nftIndex}} {{item.name}}
      </div>
      <div class="text-small d-flex justify-content-between">
        <div>
          by <span class="text-warning">{{item.artist}}</span>
        </div>
        <div>
          <div v-if="webWalletNeeded" md="6" sm="12" class="mb-3">
            <b-button v-b-tooltip.hover="{ variant: 'light' }" :title="ttWalletHelp" class="w-100" style="height: 61px;" variant="outline-light"><a :href="webWalletLink" class="text-black" target="_blank">Get Stacks Web Wallet <b-icon class="ml-3" icon="arrow-up-right-square-fill"/></a></b-button>
          </div>
          <div md="6" sm="6" class="mb-3 text-center" v-else-if="getSaleType() === 0">
            NOT SELLING
          </div>
          <div md="6" sm="6" class="mb-3 text-center" v-else-if="getSaleType() > 0 && getSaleType() < 3">
            {{salesButtonLabel}}
          </div>
          <div md="6" sm="6" class="mb-3 text-center" v-else>
            MAKE OFFER
          </div>
        </div>
      </div>
    </div>
  </div> -->
</template>

<script>
import { APP_CONSTANTS } from '@/app-constants'
import MediaItemGeneral from '@/views/marketplace/components/media/MediaItemGeneral'

export default {
  name: 'GalleryNft',
  components: {
    MediaItemGeneral
  },
  props: ['item'],
  data () {
    return {
      dims: { width: 360, height: 360 },
      // likeIconTurquoise: require('@/assets/img/Favorite_button_turquoise_empty.png'),
      // likeIconPurple: require('@/assets/img/Favorite_button_purple_empty.png'),
      explorer: 'https://explorer.stacks.co/txid/'
    }
  },
  methods: {
    marketplaceLink: function () {
      if (!this.item.contractAsset) return null
      return process.env.VUE_APP_MARKETPLACE_URL + '/nfts/' + this.item.contractAsset.contractId + '/' + this.item.contractAsset.nftIndex
    },
    getSaleType: function () {
      return this.item.contractAsset.saleData.saleType
    },
    mintedEvent (data) {
      this.$store.commit('setModalMessage', 'Transaction sent.<br/>You can find your NFTs here by clicking on My NFTs in the account section.<br/><a class="text-black" href="' + this.explorer + data.txId + '?chain=mainnet" target="_blank">Track your transaction here</a>')
      this.$root.$emit('bv::show::modal', 'waiting-modal')
    }
  },
  computed: {
    salesButtonLabel () {
      if (this.webWalletNeeded) return 'stacks wallet needed!'
      const profile = this.$store.getters[APP_CONSTANTS.KEY_PROFILE]
      if (!profile.loggedIn && this.item.contractAsset.saleData.saleType !== 3) return 'login to bid'
      let label = this.$store.getters[APP_CONSTANTS.KEY_SALES_BUTTON_LABEL](this.item.contractAsset.saleData.saleType)
      label += ' ' + this.item.contractAsset.saleData.buyNowOrStartingPrice + ' STX'
      if (this.item.contractAsset.saleData.saleType === 2) {
        const bid = this.$store.getters[APP_CONSTANTS.KEY_BIDDING_NEXT_BID](this.item.contractAsset)
        if (bid) return 'BID: ' + bid.amountFmt + ' STX'
      }
      return label
    },
    webWalletNeeded () {
      const webWalletNeeded = this.$store.getters[APP_CONSTANTS.KEY_WEB_WALLET_NEEDED]
      return webWalletNeeded
    },
    contractAsset () {
      return this.item.contractAsset
    },
    videoOptions () {
      let file = this.item.attributes.artworkFile
      if (!file) {
        file = this.item.attributes.artworkClip
      }
      if (!file) return {}
      const videoOptions = {
        emitOnHover: true,
        playOnHover: false,
        bigPlayer: true,
        assetHash: this.item.assetHash,
        autoplay: false,
        muted: false,
        controls: true,
        showMeta: false,
        dimensions: 'max-width: 100%; max-height: auto;',
        aspectRatio: '1:1',
        poster: (this.item.attributes.coverImage) ? this.item.attributes.coverImage.fileUrl : null,
        sources: [
          { src: this.item.attributes.artworkFile.fileUrl, type: this.item.attributes.artworkFile.type }
        ],
        fluid: false
      }
      return videoOptions
    },
    assetUrl () {
      if (this.item.contractAsset) {
        return '/nfts/' + this.item.contractAsset.contractId + '/' + this.item.contractAsset.nftIndex
      }
      return '#'
    }
  }
}
</script>
<style lang="scss" scoped>

 .galleryNFTContainer:hover{
    .itemHover{
        display:block;
    }
}
.itemHover{
    position: absolute;
    margin-top: 60%;
    // border-radius: 5px;
    display: none;
    padding: 1.5rem;
    background: rgba(255, 255, 255, 0.447);
    height: 30%;
    // width: 80%;
    width: 15vw;
    z-index: 10;
    margin-left: 2rem;
  backdrop-filter: blur(1rem);
}

.galleryNftDetails{
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: space-between;
  margin: auto;
  text-overflow: clip;
  // height: 20px;
  font-size: 50%;
  font-weight: 500;
  margin-right: 10px;
}
</style>
