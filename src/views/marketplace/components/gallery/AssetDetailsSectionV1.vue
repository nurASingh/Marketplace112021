<template>
<div>
  <div class="modal" id="myModal">
  <div class="modal-content">
    <span class="close" v-on:click="close()">&times;</span>
    <div id="threeCanvas"><canvas style="width: 100%; max-height: 600px; min-height: 350px;" /></div>
  </div>
</div>
<section style="margin: auto; margin-top: 5rem; max-width: 1400px; padding: 0 20px;" id="asset-details-section" v-if="gaiaAsset && gaiaAsset.contractAsset" class="text-black">
  <!-- <b-container class=" w-100 center-section" style="min-height: 50vh;"> -->
              <div class="backBtn"><router-link class="backBtn" to="/nft-marketplace/risidio/launch_collection_t1"><b-icon icon="chevron-left" shift-h="-3"></b-icon> Back </router-link></div>
    <b-row style="display: flex; margin: auto" :style="'min-height: ' + videoHeight + 'px'">
      <b-col lg="6" sm="10" class="mb-5">
        <div id="video-column" :style="dimensions">
          <MediaItem :videoOptions="videoOptions" :attributes="gaiaAsset.attributes" :targetItem="targetItem()"/>
          <MediaItemGeneral :classes="'hash1-image'" v-on="$listeners" :options="videoOptions" :mediaItem="gaiaAsset.attributes"/>
          <div class="editions"> <h2>EDITION <span>{{gaiaAsset.contractAsset.tokenInfo.edition}}</span> / {{gaiaAsset.contractAsset.tokenInfo.maxEditions}}</h2></div>
          <div v-if="gaiaAsset.attributes.artworkFile.type.includes('threed')">
            <button class="button filled" v-on:click="openModal(), three()">View 3D</button>
          </div>
          <!-- <MintInfo class="my-5" :item="gaiaAsset" :loopRun="loopRun" /> -->
        </div>
      </b-col>
      <b-col lg="6" sm="10">
        <b-row align-v="stretch" :style="'height: ' + videoHeight - 100 + 'px'">
          <b-col md="12" align-self="end" :key="componentKey">
            <div class="w-100">
              <div class="flex"><h1 class="text-black">{{mintedMessage}}</h1>
              <div style="margin-left: auto;" class="d-flex">
                <b-link router-tag="span" v-b-tooltip.hover="{ variant: 'light' }" :title='salesBadgeLabel' class="text-black" variant="outline-success"><b-icon class="ml-2" icon="question-circle"/></b-link>
                <div style="font-weight: 600; font-size: 1.2rem" class="text-center on-auction-text ml-3 py-3 px-4 bg-secondary text-white">
                  <div style="color: white;">{{salesBadgeLabel}}</div>
                  <div v-if="showEndTime()">{{biddingEndTime()}}</div>
                </div>
              </div>
              </div>
              <div>
                <div class="d-flex justify-content-between">
                  <div class="mt-2">by <span class="cyanText">{{gaiaAsset.artist}}</span> <br/>
                  <div class="mt-2 mb-5" style="font-size: 1.2rem">
                   <span v-if="loopRun.type !== 'punks'">from collection <span class="cyanText">{{loopRun.currentRun}}</span> Minted at {{created()}}</span>{{editionMessage}}</div>
                  </div>
                </div>
              </div>
              <!-- <div class="d-flex justify-content-end">
              </div> -->
              <!-- <div class="w-25">
                <ShareLinks class="mt-4" :socialLinks="getSocialLinks()" :gaiaAsset="gaiaAsset" />
              </div> -->
              <PendingTransactionInfo v-if="pending && pending.txStatus === 'pending'" :pending="pending"/>
              <div style="margin-top: 50px" v-else>
                <b-row v-if="getSaleType() === 0">
                  <b-col md="6" sm="12" v-if="editionsAvailable">
                    <EditionTrigger :item="gaiaAsset" @mintedEvent="mintedEvent" :loopRun="loopRun"/>
                  </b-col>
                  <b-col style="padding: 10px;" v-else>
                    <SquareButton @clickButton="openUpdates()" :item="gaiaAsset" :theme="'light'" :label1="'GET UPDATES'" :icon="'eye'" :text-warning="true"/>
                  </b-col>
                </b-row>
                <b-row v-else>
                  <b-col v-if="webWalletNeeded" md="6" sm="12" class="mb-3">
                      <b-button v-b-tooltip.hover="{ variant: 'light' }" :title="ttWalletHelp" class="w-100" style="height: 61px;" variant="outline-light"><a :href="webWalletLink" class="text-black" target="_blank">Get Stacks Web Wallet <b-icon class="ml-3" icon="arrow-up-right-square-fill"/></a></b-button>
                  </b-col>
                  <b-col class="nftBuyNowSection"  v-else-if="getSaleType() > 0 && getSaleType() < 3">
                  <div style="width: 80%; font-size: 1.5rem">
                    <div style="display: flex;">
                      <span class="buyNowTextPadding" > Price</span><span class="buyNowTextPadding" style="margin-left: auto;">STX<span class=" buyNowTextPadding purpleText">{{salesButtonLabel}}</span>~${{salesButtonLabel}}</span>
                    </div>
                    <!-- <div style="display: flex;">
                      <span class="buyNowTextPadding">Fees</span><span class="buyNowTextPadding" style="margin-left: auto;">STX <span class=" buyNowTextPadding purpleText">6</span> ~$6</span>
                    </div> -->
                    <div style=" display: flex; border-top: 3px solid #5154A1">
                      <!-- <span class="buyNowTextPadding" style="margin-left:auto;">STX <span class="buyNowTextPadding">6</span> ~$6</span> -->
                    </div>
                  </div>
                    <SquareButton v-b-tooltip.hover="{ variant: 'light' }" :item="gaiaAsset" :title="ttBiddingHelp" @clickButton="openPurchaceDialog()"/>
                    <!-- <SquareButton v-b-tooltip.hover="{ variant: 'light' }" :title="ttBiddingHelp" @clickButton="openPurchaceDialog()" :label1="salesButtonLabel"/> -->
                  </b-col>
                </b-row>
                <h3 class="mt-5" >NFT Description:</h3>
               <div v-if="gaiaAsset.description" class="mt-2 w-100 text-black" v-html="preserveWhiteSpace(gaiaAsset.description)">
              </div>
              </div>
              <!-- <div v-if="nftIndex > -1">
                <NftHistory class="text-small mt-5" @setPending="setPending" :nftIndex="nftIndex" :loopRun="loopRun"/>
              </div> -->
              <!-- <b-row class="my-4" v-else>
                <b-col md="6" sm="12" class="mb-3">
                  <div class="more-link m-0" v-scroll-to="{ element: '#artist-section', duration: 1000 }"><b-link class="text-black">Find out more</b-link></div>
                </b-col>
                <b-col md="6" sm="12" class="">
                  <div class="more-link m-0" v-scroll-to="{ element: '#charity-section', duration: 1000 }"><b-link class="text-black">Charity</b-link></div>
                </b-col>
              </b-row> -->
            </div>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
  <b-modal size="lg" id="asset-offer-modal" class="text-left">
    <PurchaseFlow v-if="showRpay === 1" :gaiaAsset="gaiaAsset" :loopRun="loopRun" :forceOfferFlow="forceOfferFlow" @offerSent="offerSent"/>
    <AssetUpdatesModal v-if="showRpay === 2" @registerForUpdates="registerForUpdates"/>
    <template #modal-header="{ close }">
      <div class=" text-warning w-100 d-flex justify-content-end">
        <b-button size="sm" variant="white" @click="close()"  class="m-0 p-1 text-dark" style="max-width: 30px !important; max-height: 30px !important;">
          <img :src="cross" class="filter-black" alt="close" style="max-width: 20px !important; max-height: 20px !important;"/>
        </b-button>
      </div>
    </template>
    <template #modal-footer>
      <div class="w-100 d-flex justify-content-between">
      </div>
    </template>
  </b-modal>
  <b-modal id="result-modal" class="" v-if="confirmOfferDialog">
    <b-row>
      <b-col cols="12" class="w-50">
        <h1>{{confirmOfferDialog[0].text}}</h1>
        <h4 class="text-center mb-5">{{confirmOfferDialog[1].text}}</h4>
        <h4 class="text-center mb-5"><a :href="transactionUrl" target="_blank">Transaction sent to Stacks Blockchain</a></h4>
        <p class="text-center mx-md-5 px-md-5 mb-5">{{confirmOfferDialog[2].text}}</p>
        <div class="mt-5"><a href="#" @click.prevent="back()"><b-icon icon="chevron-left"/> {{confirmOfferDialog[3].text}}</a></div>
      </b-col>
    </b-row>
    <div></div>
    <template #modal-footer class="text-center">
      <div class="w-100">
      </div>
    </template>
  </b-modal>
  <!-- </b-container> -->
</section></div>
</template>

<script>
import { APP_CONSTANTS } from '@/app-constants'
import { DateTime } from 'luxon'
import Vue from 'vue'
import utils from '@/services/utils'
import AssetUpdatesModal from '@/views/marketplace/components/toolkit/purchasing/AssetUpdatesModal'
import PurchaseFlow from '@/views/marketplace/components/toolkit/purchasing/PurchaseFlow'
import MediaItemGeneral from '@/views/marketplace/components/media/MediaItemGeneral'
import SquareButton from '@/components/utils/SquareButton'
import EditionTrigger from '@/views/marketplace/components/toolkit/editions/EditionTrigger'
import MintInfo from '@/views/marketplace/components/toolkit/mint-setup/MintInfo'
import NftHistory from '@/views/marketplace/components/toolkit/nft-history/NftHistory'
import PendingTransactionInfo from '@/views/marketplace/components/toolkit/nft-history/PendingTransactionInfo'
import ShareLinks from '@/components/utils/ShareLinks'
import * as Three from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

export default {
  name: 'AssetDetailsSectionV1',
  components: {
    PendingTransactionInfo,
    // ShareLinks,
    // NftHistory,
    AssetUpdatesModal,
    EditionTrigger,
    PurchaseFlow,
    MediaItemGeneral,
    SquareButton
    // MintInfo
  },
  props: ['gaiaAsset', 'loopRun'],
  data: function () {
    return {
      forceOfferFlow: false,
      // grid: require('@/assets/img/navbar-footer/grid.svg'),
      // cross: require('@/assets/img/navbar-footer/cross.svg'),
      // hammer: require('@/assets/img/auction.svg'),
      showRpay: 0,
      nftIndex: -1,
      pending: null,
      videoHeight: 0,
      componentKey: 0,
      showHash: false,
      assetHash: null,
      txData: null,
      socialmessage: 'This is number one, an art engine and decentralised marketplace'
    }
  },
  watch: {
    '$route' () {
      this.assetHash = this.$route.params.assetHash
      this.componentKey++
    }
  },
  mounted () {
    this.assetHash = this.$route.params.assetHash
    this.nftIndex = Number(this.$route.params.nftIndex)
    const $self = this
    this.$store.commit(APP_CONSTANTS.SET_RPAY_FLOW, { flow: 'purchase-flow', asset: this.gaiaAsset })
    this.resizeContainers()
    if (window.eventBus && window.eventBus.$on) {
      window.eventBus.$on('rpayEvent', function (data) {
        if ($self.$route.name.indexOf('asset-by-') === -1) return
        $self.componentKey++
        $self.$bvModal.hide('asset-offer-modal')
        $self.$bvModal.hide('result-modal')
        $self.txData = data
        if (data.opcode.indexOf('stx-transaction-sent') > -1) {
          if (data.txStatus === 'pending') {
            // $self.$bvModal.show('result-modal')
          }
          $self.update()
        }
      })
    }
    Vue.nextTick(function () {
      const vid = document.getElementById('video-column')
      if (vid) this.videoHeight = vid.clientHeight
    }, this)
  },
  methods: {
    created () {
      if (this.gaiaAsset && this.gaiaAsset.mintInfo && this.gaiaAsset.mintInfo.timestamp) {
        return DateTime.fromMillis(this.gaiaAsset.mintInfo.timestamp).toLocaleString({ weekday: 'short', month: 'short', day: '2-digit', hour: '2-digit', minute: '2-digit' })
      } else if (this.gaiaAsset && this.gaiaAsset.updated) {
        return DateTime.fromMillis(this.gaiaAsset.updated).toLocaleString({ weekday: 'short', month: 'short', day: '2-digit', hour: '2-digit', minute: '2-digit' })
      }
      return ''
    },
    mintedEvent (data) {
      this.$bvModal.hide('edition-modal')
      this.$emit('mintedEvent', data)
    },
    setPending (result) {
      if (this.pending) {
        if (!result || !result.txStatus || result.txStatus === 'pending') {
          this.pending = result
        } else if (this.pending.txStatus === 'pending' && result.txStatus === 'success') {
          if (result.functionName === 'mint-token') {
            const data = { contractId: this.loopRun.contractId, assetHash: result.assetHash }
            this.updateCacheByHash(data)
          } else {
            const data = { contractId: this.loopRun.contractId, nftIndex: result.nftIndex }
            this.updateCacheByNftIndex(data)
          }
        } else if (result.txStatus.startsWith('abort')) {
          // this.$notify({ type: 'danger', title: 'Transaction Info', text: 'Transaction failed - check blockchain for cause.' })
        }
      }
      this.pending = result
    },
    getSocialLinks: function () {
      const content = this.$store.getters[APP_CONSTANTS.KEY_CONTENT_CHARITY_BY_ARTIST_ID](this.artistId)
      if (content && content.length > 0 && content[0].data.social_links && content[0].data.social_links.length > 0) {
        return content.data.social_links
      }
      return [
        {
          // eslint-disable-next-line @typescript-eslint/camelcase
          social_link: [{
            text: 'type=twitter'
          }]
        },
        {
          // eslint-disable-next-line @typescript-eslint/camelcase
          social_link: [{
            text: 'type=facebook'
          }]
        }
      ]
    },
    getMediaItem () {
      const attributes = this.$store.getters[APP_CONSTANTS.KEY_MEDIA_ATTRIBUTES](this.gaiaAsset)
      return attributes
    },
    updateCacheByHash (data) {
      this.$store.dispatch('rpayStacksContractStore/updateCacheByHash', data).then(() => {
        this.$store.dispatch('rpayStacksContractStore/fetchTokenByContractIdAndAssetHash', data)
      })
    },
    updateCacheByNftIndex (data) {
      this.$store.dispatch('rpayStacksContractStore/updateCacheByNftIndex', data).then(() => {
        this.$store.dispatch('rpayStacksContractStore/fetchTokenByContractIdAndNftIndex', data)
      })
    },
    update () {
      this.componentKey++
    },
    resizeContainers () {
      let resizeTimer
      const $self = this
      window.addEventListener('resize', function () {
        clearTimeout(resizeTimer)
        resizeTimer = setTimeout(function () {
          const vid = document.getElementById('video-column')
          if (vid) $self.videoHeight = vid.clientHeight
        }, 400)
      })
    },
    formatNumber: function (number) {
      return utils.formatNumber(number)
    },
    preserveWhiteSpace: function (content) {
      return '<span class="text-description" style="white-space: break-spaces;">' + content + '</span>'
    },
    back: function () {
      this.$bvModal.hide('result-modal')
    },
    offerSent: function () {
      // local notification
    },
    showEndTime: function () {
      return this.gaiaAsset.contractAsset.saleData.saleType === 2
    },
    biddingEndTime: function () {
      return DateTime.fromMillis(this.gaiaAsset.contractAsset.saleData.biddingEndTime).toLocaleString({ weekday: 'short', month: 'short', day: '2-digit', hour: '2-digit', minute: '2-digit' })
    },
    targetItem: function () {
      return this.$store.getters[APP_CONSTANTS.KEY_TARGET_FILE_FOR_DISPLAY](this.gaiaAsset)
    },
    dimensions () {
      const dims = { width: '100%', height: '100%' }
      return 'max-width: ' + dims.height + '; max-height: ' + dims.height + ';'
    },
    poster: function () {
      if (this.gaiaAsset.attributes.coverImage) {
        return this.gaiaAsset.attributes.coverImage.fileUrl
      }
    },
    getArtist: function () {
      if (this.gaiaAsset.artist) {
        return this.gaiaAsset.artist
      } else if (this.gaiaAsset.owner) {
        return this.gaiaAsset.owner.substring(0, this.gaiaAsset.owner.indexOf('.'))
      } else {
        return 'Unknown Artist'
      }
    },
    openUpdates: function () {
      this.showRpay = 2
      this.$bvModal.show('asset-offer-modal', { assetHash: this.assetHash })
    },
    getSaleType: function () {
      return this.gaiaAsset.contractAsset.saleData.saleType
    },
    openPurchaceDialog: function () {
      this.forceOfferFlow = false
      const profile = this.$store.getters[APP_CONSTANTS.KEY_PROFILE]
      if (!profile.loggedIn && this.gaiaAsset.contractAsset.saleData.saleType !== 3) {
        this.$store.dispatch('rpayAuthStore/startLogin').then(() => {
          this.$store.dispatch('rpayCategoryStore/fetchLatestLoopRunForStxAddress', { currentRunKey: process.env.VUE_APP_DEFAULT_LOOP_RUN, stxAddress: profile.stxAddress }, { root: true })
          this.$emit('registerByConnect')
        }).catch((err) => {
          console.log(err)
          // https://chrome.google.com/webstore/detail/stacks-wallet/ldinpeekobnhjjdofggfgjlcehhmanlj
          this.webWalletNeeded = true
        })
      } else {
        if (this.gaiaAsset.contractAsset.saleData.saleType === 0) {
          return
        }
        this.showRpay = 1
        this.$bvModal.show('asset-offer-modal')
      }
    },
    openOfferPurchaceDialog: function () {
      this.forceOfferFlow = true
      this.showRpay = 1
      this.$bvModal.show('asset-offer-modal')
    },
    emailText () {
      const emailText = this.$store.getters[APP_CONSTANTS.KEY_EMAIL_TEXT]('registeremail')
      const answer = (emailText) ? emailText[0].text : 'Interest Registered'
      return answer
    },
    registerForUpdates: function (email) {
      const data = {
        emailContent: this.emailText(),
        status: 1,
        domain: location.host,
        assetHash: this.assetHash,
        email: email
      }
      this.$store.dispatch('rpayPurchaseStore/registerForUpdates', data).then((result) => {
        this.$store.commit('setModalMessage', 'Thanks for registering an interest - we will keep you updated.')
        this.showRpay = 0
        this.$bvModal.hide('asset-offer-modal')
        this.$root.$emit('bv::show::modal', 'success-modal')
        this.message = result
      }).catch(() => {
        this.$store.commit('setModalMessage', 'Thanks for re-registering an interest - we will keep you updated.')
        this.showRpay = 0
        this.$bvModal.hide('asset-offer-modal')
        this.$root.$emit('bv::show::modal', 'success-modal')
      })
    },
    close () {
      const modal = document.getElementById('myModal')
      modal.style.display = 'none'
    },
    openModal () {
      const modal = document.getElementById('myModal')
      modal.style.display = 'block'
    },
    threeObjectHandler () {
      // const selectedFile = document.getElementById('input').files[0]
      // console.log(selectedFile)
      // const binaryData = []
      // binaryData.push(this.attributes.artworkFile)
      // const url = URL.createObjectURL(new Blob(binaryData, { type: '' }))
      const url = this.gaiaAsset.attributes.artworkFile.fileUrl
      console.log(url)
      return {
        url
      }
    },
    threeLights () {
      const ambientLight = new Three.AmbientLight(0xffffff)

      return {
        ambientLight
      }
    },
    threeControls (camera, renderer) {
      const controls = new OrbitControls(camera, renderer.domElement)
    },
    three () {
      const scene = new Three.Scene()
      const canvasSize = document.getElementById('threeCanvas')
      const sizes = {
        width: window.innerWidth * 0.60,
        height: window.innerHeight * 0.8
      }
      const camera = new Three.PerspectiveCamera(75, sizes.width / sizes.height, 0.10, 1000)
      const loader = new GLTFLoader()
      const material = new Three.MeshStandardMaterial({ color: 0xFF6347, wireframe: true })
      const renderer = new Three.WebGLRenderer({ canvas: document.getElementsByTagName('canvas')[0] })

      const controls = this.threeControls(camera, renderer)
      const lights = this.threeLights()
      const url = this.threeObjectHandler()

      scene.background = new Three.Color(0xf2f2f2)
      renderer.setSize(sizes.width, sizes.height)

      // camera.position.setX(100)
      let box = new Three.Box3()
      scene.add(lights.ambientLight)
      let obj = ''
      loader.load(url.url,
        function (gltf) {
          obj = gltf.scene
          obj.material = material
          scene.add(obj)
          console.log('model loaded')
          console.log(obj)
          box = box.setFromObject(obj)
        },
        function (xhr) { // called while loading is progressing
          console.log((xhr.loaded / xhr.total * 100) + '% loaded')
        },
        function (error) {
          console.log('An error happened' + error)
        }
      )
      // These need to be run AFTER the the object has rendered

      // camera.position.setY(0)
      // camera.position.setX(0)
      camera.position.set(5, 0, 0)

      window.addEventListener('resize', () => {
        // Update sizes
        sizes.width = window.innerWidth * 0.6
        sizes.height = window.innerHeight * 0.8

        // Update camera
        camera.aspect = sizes.width / sizes.height
        camera.updateProjectionMatrix()

        // Update renderer
        renderer.setSize(sizes.width, sizes.height)
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
      })

      function animate () {
        obj.rotation.y += 0.02
      }
      function render () {
        renderer.render(scene, camera)
      }
      function start () {
        requestAnimationFrame(start)
        animate()
        render()
        // console.log('start has run once')
        // camera.position.setY(box.min.y)
        // camera.position.setX(box.min.x)
        // camera.position.setZ(box.min.z)
      }
      function fullStart () {
        render()
        start()
        console.log(canvasSize)
        console.log('fullstart has run once ')
      }
      // controls.addEventListener('change', render)
      setTimeout(() => {
        camera.position.setY(box.min.y)
        camera.position.setX(box.min.x)
        camera.position.setZ(box.min.z + (box.max.y * 2))
        console.log('camera adjusted')
      }, 8000)
      fullStart()
    }
  },
  computed: {
    mintedMessage () {
      if (this.gaiaAsset.contractAsset && this.loopRun && this.loopRun.type === 'punks') {
        return this.loopRun.currentRun + ' #' + this.gaiaAsset.contractAsset.nftIndex
      }
      if (this.gaiaAsset.contractAsset) {
        // return '#' + this.gaiaAsset.contractAsset.nftIndex + ' ' + this.gaiaAsset.name
        return this.gaiaAsset.name
      }
      return this.gaiaAsset.name
    },
    editionMessage () {
      if (this.gaiaAsset.contractAsset && this.gaiaAsset.contractAsset.tokenInfo.maxEditions > 1) {
        return '(' + this.gaiaAsset.contractAsset.tokenInfo.edition + ' of ' + this.gaiaAsset.contractAsset.tokenInfo.maxEditions + ')'
      }
      return null
    },
    transactionUrl: function () {
      if (!this.gaiaAsset.mintInfo || !this.gaiaAsset.mintInfo.txId) return '#'
      const stacksApiUrl = process.env.VUE_APP_STACKS_EXPLORER
      return stacksApiUrl + '/txid/' + this.gaiaAsset.mintInfo.txId + '?chain=' + process.env.VUE_APP_NETWORK
    },
    txPending () {
      let transactions = []
      if (this.gaiaAsset.contractAsset) {
        transactions = this.$store.getters[APP_CONSTANTS.KEY_TX_PENDING_BY_TX_ID](this.gaiaAsset.contractAsset.nftIndex)
      } else {
        transactions = this.$store.getters[APP_CONSTANTS.KEY_TX_PENDING_BY_ASSET_HASH](this.gaiaAsset.assetHash)
      }
      return transactions
    },
    editionsAvailable: function () {
      return this.gaiaAsset.contractAsset.tokenInfo.edition === 1 && this.gaiaAsset.contractAsset.editionCounter < this.gaiaAsset.contractAsset.tokenInfo.maxEditions
    },
    webWalletLink () {
      if (this.$browserDetect.isFirefox) {
        return this.$store.getters[APP_CONSTANTS.KEY_WEB_WALLET_LINK_FIREFOX]
      }
      return this.$store.getters[APP_CONSTANTS.KEY_WEB_WALLET_LINK_CHROME]
    },
    usdAmount () {
      try {
        const tickerRates = this.$store.getters[APP_CONSTANTS.KEY_TICKER_RATES]
        const rate = tickerRates.find((o) => o.currency === 'USD')
        const offer = this.$store.getters[APP_CONSTANTS.KEY_HIGHEST_OFFER_ON_ASSET](this.gaiaAsset.contractAsset.tokenInfo.assetHash)
        const currentOffer = (offer && offer.amount) ? offer.amount : 0
        const minimumOffer = Math.max(currentOffer, (this.gaiaAsset.contractAsset.saleData.reservePrice))
        const amountUsd = Number(utils.toDecimals(rate.stxPrice * minimumOffer)).toLocaleString()
        return 'Current highest offer: ' + amountUsd + ' USD'
      } catch (e) {
        return null
      }
    },
    currentCost: function () {
      return this.gaiaAsset.contractAsset.tokenInfo.editionCost
    },
    salesButtonLabel () {
      if (this.webWalletNeeded) return 'GET STACKS WALLET'
      const profile = this.$store.getters[APP_CONSTANTS.KEY_PROFILE]
      if (!profile.loggedIn && this.gaiaAsset.contractAsset.saleData.saleType !== 3) return 'LOGIN TO BID'
      // const label = this.$store.getters[APP_CONSTANTS.KEY_SALES_BUTTON_LABEL](this.gaiaAsset.contractAsset.saleData.saleType)
      if (this.gaiaAsset.contractAsset.saleData.saleType === 1) {
        // return 'Buy: ' + this.gaiaAsset.contractAsset.saleData.buyNowOrStartingPrice + ' STX'
        return this.gaiaAsset.contractAsset.saleData.buyNowOrStartingPrice
      } else if (this.gaiaAsset.contractAsset.saleData.saleType === 2) {
        const bid = this.$store.getters[APP_CONSTANTS.KEY_BIDDING_NEXT_BID](this.gaiaAsset.contractAsset)
        if (bid) return 'BID: ' + bid.amountFmt + ' STX'
      }
      return 'NOT SELLING'
    },
    salesBadgeLabel () {
      const label = this.$store.getters[APP_CONSTANTS.KEY_SALES_BADGE_LABEL](this.gaiaAsset.contractAsset)
      if (label === ('NOT FOR SALE' || 'NOT SELLING')) {
        return 'Not On Sale'
      }
      return label
    },
    confirmOfferDialog () {
      const dialog = this.$store.getters[APP_CONSTANTS.KEY_DIALOG_CONTENT]('confirm-offer')
      return dialog
    },
    ttWalletHelp () {
      const tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-wallet-help')
      return (tooltip) ? tooltip[0].text : ''
    },
    ttBiddingHelp () {
      const tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-bidding-help')
      return (tooltip) ? tooltip[0].text : ''
    },
    ttOfferingHelp () {
      const tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-offering-help')
      return (tooltip) ? tooltip[0].text : ''
    },
    ttOnAuction () {
      let tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-not-selling')
      if (this.gaiaAsset.contractAsset.saleData.saleType === 1) {
        tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-buy-now')
      } else if (this.gaiaAsset.contractAsset.saleData.saleType === 2) {
        tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-on-auction')
      } else if (this.gaiaAsset.contractAsset.saleData.saleType === 3) {
        tooltip = this.$store.getters[APP_CONSTANTS.KEY_TOOL_TIP]('tt-make-offer')
      }
      return (tooltip) ? tooltip[0].text : ''
    },
    configuration () {
      const configuration = this.$store.getters[APP_CONSTANTS.KEY_RPAY_CONFIGURATION]
      return configuration
    },
    videoOptions () {
      const videoOptions = {
        emitOnHover: true,
        playOnHover: false,
        bigPlayer: true,
        assetHash: this.assetHash,
        autoplay: false,
        muted: false,
        controls: true,
        showMeta: false,
        dimensions: 'max-width: 100%; max-height: auto;',
        aspectRatio: '1:1',
        poster: (this.gaiaAsset.attributes.coverImage) ? this.gaiaAsset.attributes.coverImage.fileUrl : null,
        sources: [
          { src: this.gaiaAsset.attributes.artworkFile.fileUrl, type: this.gaiaAsset.attributes.artworkFile.type }
        ],
        fluid: false
      }
      return videoOptions
    },
    isOwner: function () {
      const profile = this.$store.getters[APP_CONSTANTS.KEY_PROFILE]
      if (!this.gaiaAsset.contractAsset || !profile || !profile.loggedIn) return false
      return profile.stxAddress === this.gaiaAsset.contractAsset.owner
    },
    owner () {
      return this.gaiaAsset.contractAsset.owner
    },
    webWalletNeeded () {
      const webWalletNeeded = this.$store.getters[APP_CONSTANTS.KEY_WEB_WALLET_NEEDED]
      return webWalletNeeded
    }
  }
}
</script>

<style lang="scss" scoped>
#threeCanvas{
  display:block;
}
.modal {
  display: none; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  padding-top: 100px; /* Location of the box */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}
.close{
  margin: auto;
  font-size: 30px;
}
/* Modal Content */
.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 63%;
}
.button{ margin: auto;}
.more-link {
  border: 1pt solid #fff;
  padding: 3px 10px;
  text-align: center;
  font-size: 1.2rem;
}
.on-auction-text {
  text-transform: capitalize;
  font-weight: 700;
  font-size: 1.5rem;
}
#asset-offer-modal .modal-content {
  text-align: left !important;
}
.backBtn{
  color: #170A6D;
  font-weight: 700;
  margin-bottom: 30px;
}
.button{
  margin-top: 20px;
}
.editions{
  background-color: #170A6D;
  padding: 5px;
  align-items: center;
  display: grid;
  place-items: center;
}
.editions h2{
  place-items: center;
  color: white;
  font-weight: 200;
  margin: 0;
}
.editions h2 span {
  font-weight: 500;
}
</style>
