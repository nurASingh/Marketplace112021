<template>
<div class = "Htitle">
  <div class = "title-container">
      <h1>{{content.howsection1[0].howtitle[0].text}}</h1>
      <div class="pCont">
        <p> {{content.howsection1[0].howtext[0].text}}<br/></p></div>
      <!-- {{content.howsection1[0].howtext[1].text}} <br>
      {{content.howsection1[0].howtext[2].text}}<br>
      {{content.howsection1[0].howtext[3].text}}</p>
       -->
      </div>
      <router-link to="/about" ><button style="display: block; margin: auto;" class="button"> About Risidio </button></router-link>
</div>
</template>

<script>
export default {
  name: 'WRuma',
  props: ['content'],
  components: {

  }
}
</script>

<style scoped>
.Htitle{
  min-height: 30vh;
  margin: 200px auto 100px auto;
}
.title-container {
  padding: 20px;
  max-width: 1000px;
  text-align: center;
  margin: auto;
}
.title-container h1{
  font: normal normal 300 37px/30px Montserrat;
}
.title-container p{
  font-family: inherit;
  font-size: 22px;
  font-weight: 500;
  padding-top: 25px;
  padding-bottom: 20px;
}
.pCont p{
  font: normal normal 300 14px/20px Montserrat;
  margin:auto;
  max-width: 570px;
}
button {
  background-color: rgba(80, 177, 181, 0.1);
  margin: 40px auto 0;
  font-size: 1.1rem;
  font-weight: 700;
  color: #5FBDC1;
}

</style>
