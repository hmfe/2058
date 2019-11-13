<template>
  <div id="app" class="text-center">
    <header>
      <div class="top-header">
        <img src="./assets/logo.png" alt="hm logo" />
        <h1 class="header">arbetsprov</h1>
      </div>
    </header>

    <h2 class="h5 mb">Eric Björklund</h2>

    <div class="mb text-left section-half">
      <CssButton />
    </div>

    <div class="section-half">
      <Search />
    </div>
  </div>
</template>

<script>
import CssButton from "./components/CssButton.vue";
import Search from "./components/Search.vue";

export default {
  name: "app",
  components: {
    CssButton,
    Search
  }
};
</script>

<style lang="scss">
#app {
  width: 100%;
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  margin-left: auto;

  @media (min-width: 576px) {
    max-width: 540px;
  }

  @media (min-width: 768px) {
    max-width: 720px;
  }

  @media (min-width: 992px) {
    max-width: 960px;
  }

  @media (min-width: 1200px) {
    max-width: 1140px;
  }
}

.mb {
  margin-bottom: 4rem !important;
}

.section-half {
  position: relative;
  width: 100%;

  @media (min-width: 992px) {
    flex: 0 0 50%;
    max-width: 50%;
    margin-left: 25%;
  }
}
</style>
