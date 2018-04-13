<template lang='pug'>
  div
    hr
    b Site {{site_number}}: {{sites[site_number-1].name}} [{{site_number}} / {{sites.length}}] &nbsp; &nbsp;
      button(v-if='site_number > 1' @click.prevent='previousSite') < 
      span(v-else) | &nbsp;
      span &nbsp; &nbsp;

      button(v-if='site_number < sites.length' @click.prevent='nextSite') > 
      span(v-else) | &nbsp;
    hr
    b {{sites[site_number-1]}}
    hr
</template>

<script>
// import Vue from 'vue'
// import Router from 'vue-router'

import Modal from './../Standard/Modal.vue'
import Messaging from './../Standard/Messaging.vue'

import 'vue-awesome/icons/check-circle'
import 'vue-awesome/icons/warning'
import 'vue-awesome/icons/home'
import 'vue-awesome/icons/edit'
import 'vue-awesome/icons/close'
import 'vue-awesome/icons/question-circle'

export default {
  name: 'ovid',
  components: {
    Messaging,
    Modal
  },
  data () {
    return {
      active_site: null
    }
  },
  props: {
    tour: {
      type: Object
    },
    sites: {
      type: Array
    },
    site_number: {
      type: Number
    },
    next: {
      type: Function
    },
    last: {
      type: Function
    }
  },
  events: {
  },
  created: function () {
    this.active_site = this.site_number
  },
  computed: {
    active: function () {
      if (this.active_site) {
        console.log('use ' + this.active_site)
        return this.active_site
      } else {
        console.log('or ' + this.site_number)
        return this.site_number
      }
    },
    site_index: function () {
      if (this.active_site > 0) {
        return this.active_site
      } else {
        return this.site_number
      }
    }
  },
  methods: {
    nextSite: function () {
      this.next()
      // console.log('next...')
      // if (this.active < this.sites.length) {
      //   this.active_site++
      // } else {
      //   console.log('last site ... no more options')
      // }
    },
    previousSite: function () {
      this.last()
      //   console.log('last...')
      //   if (this.active > 1) {
      //     this.active_site--
      //   } else {
      //     console.log('first site .. cannot go lower...')
      //   }
    }
  }
}
</script>

<style>

</style>
