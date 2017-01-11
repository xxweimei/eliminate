<template>
  <div id="app">
    <header></header>
    <div class="body">
      <div class="left"></div>
      <aside class="center">
        <board></board>
      </aside>
      <div class="right"></div>
    </div>
    <footer>
      <!--<css-test></css-test>-->
    </footer>
  </div>
</template>

<script>
  import Board from './components/Board'
  import CssTest from './components/CssTest'

  export default {
    name: 'app',
    components: {
      Board,CssTest
    }
  }
</script>

<style>
  #app {
    display: flex;
    flex-direction: column;
  }

  header,footer {
    flex: 1;
    min-height: 5rem;
  }

  .body {
    flex: 1;
    justify-content:center;
    display: flex;
  }

  .left,.right,.center {
    flex: 1;
  }
</style>
