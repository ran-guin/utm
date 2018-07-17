<template lang='pug'>
  div
    PrivateHeader.header(:demo="demo")
    div.body.container
      h3 Overview
      p UTM - Universal Touring Machine
      ul
        li - Audio Tours (Galleries, Museums, City Guides)
      hr
      p &nbsp;
    PublicFooter.footer

</template>

<script>

import Modal from './../Standard/Modal'
import PrivateHeader from './PrivateHeader.vue'
import PublicFooter from './PublicFooter.vue'

export default {
  name: 'AboutOvid',
  data () {
    return {
      DB_options: {
        title: 'Scope of Databases used by OVID',
        openButton: 'Database Overview',
        htmlContent: "<img src='/static/images/ovid_db.png' style='width:100%'/>'"
      },
      OVID_options: {
        title: 'Data maintained within OVID',
        openButton: 'OVID Data Schema',
        htmlContent: "<img src='/static/images/ovid_schema.png' style='width:100%'/>'"
      }
    }
  },
  components: {
    Modal,
    PrivateHeader,
    PublicFooter
  },

  methods: {
  }
}
</script>

<style scoped>
</style>
