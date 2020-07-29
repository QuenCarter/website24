<template>
  <div id="app">
    <PageHeader />
    
    <VideoSection />
    <PageFooter />

  </div>
</template>

<script>
  import PageFooter from './components/PageFooter.vue'
  import PageHeader from './components/PageHeader.vue'
  import VideoSection from './components/VideoSection'

  export default {
    name: 'App',
    components: {
      PageFooter, PageHeader, VideoSection
    }
  }
</script>

<style>
  @font-face {
    font-family: "Brandon_Medium";
    src: url("/fonts/BRANDON_MED.otf") format("opentype");
  }
  @font-face {
    font-family: "Brandon_Light";
    src: url("/fonts/BRANDON_LIGHT.otf") format("opentype");
  }
  

  #app {
    display: flex;
    flex-direction: column;
  }

  h1 {
    letter-spacing: .1em;
    font-family: 'Brandon_Medium';
  }

  h3 {
    letter-spacing: .05em;
  }
  body{
    margin: 0;
    box-sizing: border-box;
    color:#393C3E;
    font-family: "Brandon_Light";
    letter-spacing: .025em;

  }
  .padding{
    padding: 0 5vw;
  }
  
</style>