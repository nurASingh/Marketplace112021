<template>
<!-- <div v-if="content"> -->
  <div class="footer">
  <footer id="footer" class="text-black">
    <div class="container">
      <div class="row m-0 d-flex">
        <div class="col-lg-5 col-12 d-flex flex-column justify-content-center align-items-lg-start align-items-center">
          <router-link to="/" class="navbar-brand text-black"><img :src="logo" alt="risidio-logo" class="footer-logo"/></router-link>
          <p class="footer__pitch">{{content.footertext[0].text}}</p>
        </div>
        <div class="col-lg-7 col-12 d-flex columns3-content__container">
            <div class="footer__column-content ml-lg-auto">
              <div> Marketplace</div>
              <div v-on:click="topFunction()"><router-link to="/nft-marketplace">Gallery</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/how-it-works">How It Works</router-link></div>
              <div class="text-secondary">leveller</div>
              <div class="text-secondary">leveller</div>
            </div>
            <div v-if="profile.loggedIn" class="footer__column-content">
              <div >Your NFTs</div>
              <div v-on:click="topFunction()"><router-link to="/my-nfts">All Your Nft's</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/create">Add an NFT</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/qa">FAQ</router-link></div>
              <div class="text-secondary">leveller</div>
            </div>
            <div class="footer__column-content">
              <div>Risidio</div>
              <div v-on:click="topFunction()"><router-link to="/get-in-touch">Who We Are</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/get-in-touch">What We Do</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/get-in-touch">Work With Us</router-link></div>
              <div v-on:click="topFunction()"><router-link to="/get-in-touch">Get in Touch</router-link></div>
            </div>
        </div>
      </div>
      <div class="footer__divider"></div>
      <div class="col-12 d-lg-flex d-none justify-content-between footer__bottom-row">
        <div>
          &copy; &nbsp; Risidio Ltd. All right reserved.
        </div>
        <div>
          <!-- <a href="https://discord.gg/qvuxB9P"><img :src="discordLogo" alt="discord-logo"></a>
          <a href="https://www.instagram.com/risidio_official/"><img :src="instagramLogo" alt="instagram-logo"></a>
          <a href="https://twitter.com/Risidio1"><img :src="twitterLogo" alt="twitter-logo"></a>
          <a href="https://www.youtube.com/channel/UCrMDxxhMvpeyErw7m92IIXA/featured"><img :src="youtubeLogo" alt="youtube-logo"></a> -->
        </div>
        <div>
          <span>Privacy Policy</span>
          <span>Terms Of Use</span>
        </div>
      </div>
      <!-- MOBILE DESIGN LAST ROW -->
      <div class="col-12 d-flex d-lg-none flex-column align-items-center footer__bottom-row">
        <div>
          <!-- <a href="https://discord.gg/qvuxB9P"><img :src="discordLogo" alt="discord-logo"></a>
          <a href="https://www.instagram.com/risidio_official/"><img :src="instagramLogo" alt="instagram-logo"></a>
          <a href="https://twitter.com/Risidio1"><img :src="twitterLogo" alt="twitter-logo"></a>
          <a href="https://www.youtube.com/channel/UCrMDxxhMvpeyErw7m92IIXA/featured"><img :src="youtubeLogo" alt="youtube-logo"></a> -->
        </div>
        <div>
          &copy; &nbsp; Risidio Ltd. All right reserved.
        </div>
        <div>
          <span>Privacy Policy</span>
          <span>Terms Of Use</span>
        </div>
      </div>
    </div>
  </footer>
</div>
</template>
<script>
import { APP_CONSTANTS } from '@/app-constants'

export default {
  name: 'Main-Footer',
  components: {
  },
  props: {
    backgroundColor: String,
    type: String
  },
  data () {
    return {
      logo: require('@/assets/img/risidio_white.png')
      // year: new Date().getFullYear(),
      // discordLogo: require('@/assets/img/discord-logo.svg'),
      // instagramLogo: require('@/assets/img/instagram-logo.svg'),
      // twitterLogo: require('@/assets/img/twitter-logo.svg'),
      // youtubeLogo: require('@/assets/img/youtube-logo.svg')
    }
  },
  methods: {
    // getContent (contentType) {
    //   const content = this.$store.getters['contentStore/getHomepage']
    //   if (content) {
    //     let sitecontent = ''
    //     content.footer[0][contentType].forEach(function (option) {
    //       sitecontent += option.text
    //     })
    //     return sitecontent
    //   }
    //   return {}
    // },
    // topFunction () {
    //   const scrollTop = window.scrollTo({ top: 0, behavior: 'smooth' })
    //   scrollTop()
    // }
  },
  computed: {
    profile () {
      const profile = this.$store.getters[APP_CONSTANTS.KEY_PROFILE]
      return profile
    },
    content () {
      const content = this.$store.getters[APP_CONSTANTS.KEY_CONTENT_FOOTER]
      return content
    }
  }
}
</script>
<style>
/* FOOTER STYLE */
footer {
  color: white !important;
  background-color: #5154A1;
}

footer .container {
  padding-top: 90px;
  padding-bottom: 50px;
}

footer a {
  color: #fff !important;
}

/* FOOTER LOGO COLUMN */
.footer-logo {
  width: auto;
  height: 71px;
}

footer .navbar-brand {
  width: auto;
  height: 80px;
}

.footer__pitch {
  margin: 30px 0;
  font-size: 1.2rem;
  color: #fff;
}

/* FOOTER CONTENT COLUMN */
.columns3-content__container {
  align-items: flex-end;
}

.footer__column-content:not(:last-child) {
  padding-right: 100px;
}

.footer__column-content {
  font-size: 1.2rem;
}

.footer__column-content div:first-child {
  font-weight: 300;
  color: #E3E3E3;
  margin-bottom: 15px;
}

.footer__column-content div {
  margin-bottom: 9px;
}

/* FOOTER DIVIDER AND BOTTOM ROW */
.footer__divider {
  margin: 60px 15px 50px;
  border-top: 1px solid #E3E3E3;
  opacity: 0.2;
}

.footer__bottom-row {
  font-size: 1.2rem;
  font-weight: 300;
}

.footer__bottom-row a, .footer__bottom-row a:hover {
  color: #fff;
}

.footer__bottom-row a:not(:last-child) {
  margin-right: 15px;
}

.footer__bottom-row span:first-child {
  margin-right: 40px;
}

/* FOOTER ON MOBILE */
@media only screen and (max-width: 991px) {
  footer .container {
    padding-top: 60px;
    text-align: center;
  }
  .footer__pitch {
    margin: 30px 0 60px;
    text-align: center;
  }
  .columns3-content__container {
    justify-content: center;
    align-items: flex-start;
  }
  .footer__divider {
    margin: 40px 15px;
  }
  .footer__bottom-row div:first-child {
    margin-bottom: 40px;
  }
  .footer__bottom-row div:nth-child(2) {
    margin-bottom: 20px;
  }
  .footer__bottom-row span:first-child {
    margin-right: 49px;
  }
}

@media only screen and (max-width: 500px) {
  .footer__column-content:not(:last-child) {
    padding-right: 40px;
  }
}

@media only screen and (max-width: 400px) {
  .footer__column-content:not(:last-child) {
    padding-right: 25px;
  }
}

@media only screen and (max-width: 359px) {
  .footer__column-content {
    font-size: 0.9rem;
  }
}
</style>
