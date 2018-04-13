<template lang='pug'>
  div.publicFooter
    div

      <!-- span.navbar-centre(v-html="centre") -->
      div.navbar-right(v-html="right")
</template>


<script>
export default {
  // template: require('./main.pug'),

  data () {
    return {
    }
  },
  props: {
    left: {
      type: String
    },
    right: {
      type: String,
      default: "<a href='mailto:subir_guin@hotmail.ca?Subject=T4U%20website%20Contact' style='color:white'>Contact Us</a>"
    },
    centre: {
      type: String
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .publicFooter {
    color: black;
    background-color: #6c6;
  }

  publicFooter span a {
    color: black;
  }

  img.logo-s {
    max-height: 70px;
    margin-top: -15px;
    padding-right: 50px;
  }

  a {
    color: black;
  }
 /* publicFooter span a:hover {
    color: red;
  }*/
</style>
