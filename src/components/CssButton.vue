<template>
  <button class="button-delete">Delete</button>
</template>

<script>
export default {
  name: "CssButton",
  props: {}
};
</script>


<style lang="scss">
$gray-dark: #84817e;
$orange: #f2a54d;

.button-delete {
  background-color: $orange;
  border: 2px solid $gray-dark;
  padding: 0.5rem 0.6rem 0.5rem 1.2rem;
  font-weight: bold;

  &:after {
    content: "\f00d";
    font-family: "Font Awesome 5 Free";
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;
    display: inline-block;
    font-style: normal;
    font-variant: normal;
    text-rendering: auto;
    line-height: 29px;
    margin-left: 0.6rem;
    background: #fff;
    padding: 0.7rem 1.15rem;
    border-radius: 50%;
    font-size: 1.7rem;
    vertical-align: sub;
  }
}
</style>