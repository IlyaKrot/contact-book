<template>
  <div id="app">
    <div class="app__header">
      <h1 class="app__title">CONTACT BOOK</h1>
    </div>
    <div class="app__container">
      <router-view/>
    </div>
    <div class="back-arrow" v-scroll="handleScroll" @click="backScroll">
      <img src="http://cdn.onlinewebfonts.com/svg/download_185769.png" alt="arrow">
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  methods: {
    handleScroll(evt, el) {
      if (window.scrollY > 50) {
        el.setAttribute(
          'style',
          'opacity: 0.7; transform: translate(0, 0px)'
        )
      } else {
        el.setAttribute(
          'style',
          'opacity: 0; transform: translate(0, 30px)'
        )
      }
    },
    backScroll() {
      window.scroll(0, 0);
    }
  }
}
</script>

<style lang="less">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: #EDEEF0;
  padding-bottom: 5px;
  transition: all 0.5s;
  min-height: 100vh;
}

body {
  margin: 0;
  padding: 0;
}

@media screen and (max-width: 900px) {
  .app__container {
    width: 100%;
    margin: 0 auto;
    margin-top: 15px; 
    border-radius: 5px;
  }
}

@media screen and (min-width: 899px) {
  .app__container {
    width: 50%;
    margin: 0 auto;
    margin-top: 15px; 
    border-radius: 5px;
  }
}

.app__header {
  padding: 5px;
  background: #fff;
  box-shadow: 0 2px 15px -10px gray;
  color: #42b983;
}

ul {
  padding: 0;
}

li {
  text-decoration: none;
  list-style: none;
}

.back-arrow{
  position: fixed;
  bottom: 50px;
  left: 10vw;
  width: 40px;
  height: 40px;
  opacity: 0;
  transition: 0.3s all;

  &:hover {
    cursor: pointer;
  }

  img {
    height: 100%;
    width: 100%;

    &:active {
      transform: scale(0.9);
    }
  }
}
</style>
