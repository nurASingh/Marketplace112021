<template>
<b-modal size="lg" id="success-modal">
  <b-container>
    <b-row>
      <b-col cols="8">
        <div v-html="modalMessage"></div>
      </b-col>
    </b-row>
  </b-container>
    <template #modal-header="{ close }">
      <div class="text-black text-warning w-100 d-flex justify-content-end">
        <b-button size="sm" @click="close()"  class="m-0 p-1 text-dark" style="background: #fff; border:none; max-width: 30px !important; max-height: 30px !important;">
          <img class="filter-black" :src="cross" alt="close" style="max-width: 20px !important; max-height: 20px !important;"/>
        </b-button>
      </div>
    </template>
  <template #modal-footer class="text-center"><div class="w-100"><p class="footer-text"></p></div></template>
</b-modal>
</template>

<script>
import { APP_CONSTANTS } from '@/app-constants'

export default {
  name: 'SuccessModal',
  components: {
  },
  data () {
    return {

    }
  },
  methods: {
  },
  computed: {
    modalMessage () {
      return this.$store.getters[APP_CONSTANTS.KEY_MODAL_MESSAGE]
    }
  }
}
</script>
<style lang="scss" >
</style>
