<template>
<div id="app" v-if="!loading" :style="'z-index: -40; min-height: 90vh; background-size: contain; background-image:'">
  <div v-if="!configured">
    <RisidioPay :configuration="configuration"/>
  </div>
  <div :key="componentKey" v-else>
    <router-view name="header" style="z-index: 10;"/>
    <main v-if="adminSection()">
      <b-container fluid>
        <b-row align-h="center">
          <b-col md="2" sm="12" class="pt-4 pl-3 text-small px-0 bg-black" style="min-height: 60vh">
            <router-view name="adminNav" />
          </b-col>
          <b-col md="10" sm="12" class="bg-black p-4 text-small">
            <router-view/>
          </b-col>
        </b-row>
      </b-container>
    </main>
    <router-view v-else style="min-height: 99vh;" />
    <router-view name="footer"/>
    <!-- <notifications :duration="10000" classes="r-notifs" position="bottom left" width="50%"/> -->
    <!-- <WaitingModal/>
    <SuccessModal/>
    <MessageTicker :tickerId="tickerId + '-inner'"/> -->
  </div>
  <!-- {{sig}} -->
</div>
</template>
<script>
// import SuccessModal from '@/components/utils/SuccessModal'
// import WaitingModal from '@/components/utils/WaitingModal'
// import RisidioPay from 'risidio-pay'
import { APP_CONSTANTS } from '@/app-constants'
// import MessageTicker from '@/views/marketplace/components/gallery/common/MessageTicker'

const RisidioPay = () => import('risidio-pay')

export default {
  name: 'App',
  components: {
    // MessageTicker,
    // SuccessModal,
    // WaitingModal,
    RisidioPay
  },
  data () {
    return {
      loading: true,
      configured: false,
      componentKey: 0,
      tickerId: 'anti-app',
      sig: null
    }
  },
  mounted () {
    this.setupEventListener()
    // this.resizeContainers()
    this.readPrismicContent()
  },
  methods: {
    adminSection () {
      return this.$route.path.startsWith('/mgmnt')
    },
    setupEventListener () {
      this.$store.commit(APP_CONSTANTS.SET_RPAY_FLOW, { flow: 'config-flow', asset: this.gaiaAsset })
      const $self = this
      this.loading = false
      if (window.eventBus && window.eventBus.$on) {
        window.eventBus.$on('rpayEvent', function (data) {
          if (data.opcode === 'configured') {
            $self.$store.dispatch('initApplication').then(() => {
              document.getElementById($self.tickerId).style.display = 'none'
              $self.$store.dispatch('rpayPurchaseStore/fetchOffers')
              $self.configured = true
            })
          }
          /**
          } else if (data.opcode === 'stx-transaction-finished') {
            const txResult = $self.$store.getters[APP_CONSTANTS.KEY_TRANSACTION_DIALOG_MESSAGE]({ dKey: data.opcode, txId: data.txId })
            $self.$store.commit('setModalMessage', txResult)
          } else if (data.opcode === 'stx-transaction-sent') {
            const txResult = $self.$store.getters[APP_CONSTANTS.KEY_TRANSACTION_DIALOG_MESSAGE]({ dKey: data.opcode, txId: data.txId })
            $self.$store.commit('setModalMessage', txResult)
          } else if (data.opcode === 'stx-transaction-error') {
            const txResult = $self.$store.getters[APP_CONSTANTS.KEY_TRANSACTION_DIALOG_MESSAGE]({ dKey: data.opcode, txId: data.txId })
            $self.$store.commit('setModalMessage', txResult)
          } else if (data.opcode === 'configured-logged-in') {
            $self.$store.commit('rpayAuthStore/setAuthResponse', data.session)
            $self.$store.dispatch('rpayAuthStore/fetchMyAccount')
          }
          **/
        })
      }
    },
    readPrismicContent () {
      this.$prismic.client.getSingle('homepage').then(document => {
        if (document) {
          this.$store.commit('contentStore/addHomeContent', document.data)
        }
      })
      this.$prismic.client.getSingle('marketplacetemplate').then(document => {
        if (document) {
          this.$store.commit('contentStore/addMarket', document.data)
        }
      })
      this.$prismic.client.getSingle('launchcollection').then(document => {
        if (document) {
          this.$store.commit('contentStore/addLaunchCollection', document.data)
        }
      })
      this.$prismic.client.getSingle('howitworks').then(document => {
        if (document) {
          this.$store.commit('contentStore/addHowItWorks', document.data)
        }
      })
      this.$prismic.client.getSingle('hiro_walet').then(document => {
        if (document) {
          this.$store.commit('contentStore/addHiroWallet', document.data)
        }
      })
      this.$prismic.client.getSingle('mainfooter').then((document) => {
        if (document) {
          this.$store.commit('contentStore/addMainFooter', document.data)
        }
        this.$prismic.client.getSingle('dialogs').then(document => {
          if (document) {
            this.$store.commit('contentStore/addDialogs', document.data)
          }
          this.$prismic.client.getSingle('emails').then(document => {
            if (document) {
              this.$store.commit('contentStore/addEmails', document.data)
            }
            this.$prismic.client.getSingle('tooltips').then(document => {
              if (document) {
                this.$store.commit('contentStore/addTooltips', document.data)
              }
              this.$prismic.client.getSingle('about').then(document => {
                if (document) {
                  this.$store.commit('contentStore/addAboutContent', document.data)
                }
                this.$prismic.client.getSingle('collaboration').then(document => {
                  if (document) {
                    this.$store.commit('contentStore/addCollaboration', document.data)
                  }
                })
              })
            })
          })
        })
      })
      this.$prismic.client.query(
        this.$prismic.Predicates.at('document.type', 'charity'),
        { pageSize: 20, page: 1 }
      ).then((response) => {
        this.$store.commit('contentStore/addCharities', response.results)
        this.$prismic.client.query(
          this.$prismic.Predicates.at('document.type', 'information_page'),
          { pageSize: 40, page: 1 }
        ).then((response) => {
          this.$store.commit('contentStore/addInformation', response.results)
          this.$prismic.client.query(
            this.$prismic.Predicates.at('document.type', 'artist'),
            { pageSize: 20, page: 1 }
          ).then((response) => {
            this.$store.commit('contentStore/addArtists', response.results)
          })
        })
      })
    },
    resizeContainers () {
      let resizeTimer
      const $self = this
      window.addEventListener('resize', function () {
        const resizable = $self.$route.meta.resizable
        if (!resizable) {
          return
        }
        $self.loading = true
        clearTimeout(resizeTimer)
        resizeTimer = setTimeout(function () {
          $self.$store.commit('setWinDims')
          $self.componentKey += 1
          $self.loading = false
        }, 400)
      })
    },
    scrollMeTo (data) {
      const element = document.getElementById(data.refName)
      const top = element.offsetTop
      window.scrollTo(0, top)
    }
  },
  computed: {
    configuration () {
      const configuration = this.$store.getters[APP_CONSTANTS.KEY_RPAY_CONFIGURATION]
      return configuration
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
