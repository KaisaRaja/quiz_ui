<template>
  <div id="app" class="container">
    <h1 class="title">Viktoriin</h1>
    <p v-if="showIntroText" class="intro-text">
      Vasta viiele küsimusele Eesti kohta ja saa teada enda tulemus!</p>
    <QuizComponent @first-answer="hideIntroText"/>
  </div>
</template>

<script>
import QuizComponent from './components/QuizComponent.vue';

export default {
  components: {
    QuizComponent
  },
  data() {
    return {
      showIntroText: true
    };
  },
  methods: {
    hideIntroText() {
      this.showIntroText = false;
    }
  }
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
}
.container {
  text-align: center;
  padding: 20px;
  width: 100%;
  max-width: 600px;
}
  .title {
    color: black;
    font-size: 48px;
    font-weight: bold;
  }

  .intro-text {
    font-size: 20px;
    color: white;
    margin-top: 2px;
    margin-bottom: 10px;
    background-color: black;
    padding: 10px 20px;
    border-radius: 20px;
    display: inline-block;
  }
</style>
