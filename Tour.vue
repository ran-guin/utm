<template lang='pug'>
  div
    PrivateHeader.header(:payload='payload')
    div.container
      span
        div.navbar-right
          a(href='#' onclick='return false' @click='toggleMap')
            span(v-if='map_is_open')
              h3 Close Map
            span(v-else)
              h3 Map
        div(v-if='site_number')
          h3
            button(@click.prevent='homeTour')
              b {{tour.name}} Tour (site: {{site_number}})
          Site(:tour='tour' :sites='sites' :site_number='site_number' :next='nextSite' :last='previousSite')
        div(v-else)
          h3 {{tour.name}} Tour [{{this.sites.length}} / {{this.markers.length}} sites] : {{$route.params.id}}
          hr
      div(v-if='map_is_open')
        Map(id: 'tourMap' title='tour map' :markers='markers' :index='index' :map="map_options" :onClick='gotoSite')
        p &nbsp;
      div(v-else)
        ul
          li(v-for="site in this.sites")
            button(@click.prevent="gotoSite(site.site_number)")
              b {{site.name}}
            i &nbsp; &nbsp; [{{site.latitude}} x {{site.longitude}}]
    PublicFooter.footer
</template>

<script>
// import Vue from 'vue'
// import Router from 'vue-router'

import DataGrid from './../Standard/DataGrid.vue'

import Messaging from './../Standard/Messaging.vue'
import RecursiveList from './../Standard/RecursiveList.vue'
import Map from './../Standard/Map.vue'
import Modal from './../Standard/Modal.vue'

import PrivateHeader from './PrivateHeader.vue'
import PublicFooter from './PublicFooter.vue'

import axios from 'axios'
import config from '@/config.js'

import 'vue-awesome/icons/check-circle'
import 'vue-awesome/icons/warning'
import 'vue-awesome/icons/home'
import 'vue-awesome/icons/edit'
import 'vue-awesome/icons/close'
import 'vue-awesome/icons/question-circle'

import Site from './Site.vue'

export default {
  name: 'ovid',
  components: {
    DataGrid,
    Messaging,
    Modal,
    Map,
    Site,
    RecursiveList,
    PrivateHeader,
    PublicFooter
  },
  data () {
    return {
      // ** Test Data **
      modalOptions: { openButton: 'TEST Modal', content: 'Hi there' },
      tour_id: null,
      DBtours: [
        {id: 1, name: 'Vienna', latitude: 48.203, longitude: 16.37},
        {id: 2, name: 'Vienna Shopping District', latitude: 48.203, longitude: 16.37}
      ],
      DBsites: [
        {id: 1, tour_id: 1, 'name': 'Danube Bridge', site_number: 1, latitude: 48.2032581, longitude: 16.36, map_position: ''},
        {id: 2, tour_id: 1, 'name': 'Opera House', site_number: 2, latitude: 48.2032582, longitude: 16.37, map_position: ''},
        {id: 3, tour_id: 1, 'name': 'Augarten', site_number: 3, latitude: 48.2032583, longitude: 16.38, map_position: ''},
        {id: 4, tour_id: 1, 'name': 'Shopping District', site_number: 4, latitude: 48.2032584, longitude: 16.39, map_position: ''},
        {id: 5, tour_id: 2, 'name': 'Armani', site_number: 4, latitude: 48.2032584, longitude: 16.39, map_position: ''},
        {id: 6, tour_id: 2, 'name': 'Rolex', site_number: 4, latitude: 48.2032584, longitude: 16.39, map_position: ''}
      ],
      centre: {lat: 48.203, lng: 16.37},

      site_number: null,
      tour: null,
      markers: [],
      sites: [],

      siteURL: config.siteMirrorUrl,
      opensites: {'idnull': true, 'id0': true, 'id1': true},
      options: { table: 'site', nameKey: 'alias', stored: true, label: 'site' },
      map_options: {
        style: 'height: 600px; width: 800px;',
        openButton: 'Map',
        url: 'https://maps.google.ca',
        center: {lat: 48.203, lng: 16.37}
      },
      map_is_open: false,
      payload: {}
    }
  },
  props: {
    id: {
      type: String
    }
  },
  events: {
  },
  created: function () {
    // Load Payload //
    // var payload = config.demo_payload
    // var payload = this.$store.getters.payload
    // console.log('payload: ' + JSON.stringify(payload))
    // this.$store.commit('setHash', {key: 'payload', value: payload})
    // var showPayload = this.$store.getters.getHash('payload')
    // console.log('payload: ' + JSON.stringify(showPayload))
    // this.payload = showPayload

    axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

    if (this.$route.params.id) {
      this.tour_id = parseInt(this.$route.params.id)
      console.log('retrieve tour: ' + this.$route.params.id)
      // ** Load Tour
      for (var t = 0; t < this.DBtours.length; t++) {
        if (parseInt(this.DBtours[t].id) === parseInt(this.$route.params.id)) {
          console.log('found tour ' + t)
          this.tour = this.DBtours[t]
          t = this.DBtours.length
        }
      }

      // ** Mark sites in specified tour **
      for (var i = 0; i < this.DBsites.length; i++) {
        console.log(i + ': ' + this.DBsites[i].tour_id + this.DBsites[i].tour_id.constructor + ' vs ' + this.$route.params.id + this.$route.params.id.constructor)
        if (this.DBsites[i].tour_id && this.DBsites[i].tour_id.toString() === this.$route.params.id) {
          var site = this.DBsites[i]
          var siteNum = i + 1
          var markSite = {
            type: 'Site',
            id: site.id,
            position: {lat: site.latitude, lng: site.longitude},
            label: {text: siteNum.toString()}
          }
          this.$set(this.sites, this.sites.length, site)

          console.log('add marker...')
          this.$set(this.markers, i, markSite)
          this.$set(this.markers[i], 'name', site.name)
        }
      }
    } else {
      console.log('retrieve all tours ...')
      // ** Mark tours only **
      for (var T = 0; T < this.DBtours.length; T++) {
        var tour = this.DBtours[T]
        var markTour = {
          type: 'Tour',
          id: tour.id,
          position: {lat: tour.latitude, lng: tour.longitude},
          label: {text: tour.name}
        }
        this.$set(this.markers, T, markTour)
      }
    }

    var _this = this
    axios.get(this.siteURL)
    .then(function (result, err) {
      if (err) {
        console.log('set error...')
        _this.$store.commit('setError', {context: 'Searching For ' + _this.scope, err: err})
        console.log('axios error: ' + err)
      } else {
        _this.DBsites = result.data

        console.log('axios returned value(s): ' + JSON.stringify(_this.DBsites))
        _this.$store.commit('setHash', {key: 'sites', value: _this.DBsites})
      }
    })
    .catch(function (err) {
      console.log('ERROR: ' + err)
    })
  },
  mounted: function () {
    console.log('mounted')
    var payload = this.$store.getters.payload
    console.log('payload: ' + JSON.stringify(payload))
    this.payload = payload
  },
  computed: {
    index: function () {
      if (this.site_number) {
        return this.site_number - 1
      } else { return 0 }
    },
    storedsites: function () {
      var C = this.$store.getters.getHash('sites') || []
      console.log('load sites: ' + JSON.stringify(C))
      return C
    }
  },
  methods: {
    toggleMap: function () {
      this.map_is_open = !this.map_is_open
    },
    gotoSite: function (number) {
      this.$set(this, 'site_number', number)
      console.log('go to site: ' + number + ' or ' + this.site_number)
      // this.site_number = number
    },
    nextSite: function () {
      console.log('next...' + this.site_number)
      if (this.site_number < this.sites.length) {
        this.site_number++
      } else {
        console.log('last site ... no more options')
      }
    },
    previousSite: function () {
      console.log('last...' + this.site_number)
      if (this.site_number > 1) {
        this.site_number--
      } else {
        console.log('first site .. cannot go lower...')
      }
    },
    siteModal: function (record) {
      console.log('retrieve more schedule info from record: ' + JSON.stringify(record))

      this.$store.dispatch('setModalData', record)
      this.$store.getters.toggleModal('info-modal')
    },
    homeTour: function () {
      // this.site_number = null
      console.log('set site: ' + this.site_number)
      this.$set(this, 'site_number', null)
      console.log('site: ' + this.site_number)
    }
  }
}
</script>

<style>
.page {
  /*margin-top: -20px;*/
  height: 100%;
  width: 100%;
}
.body {
  min-height: calc(100vh - 150px);
}
.header {
  height: 80px;
  background-color: #3a3;
}
.footer {
  height: 70px;
  /*padding: 10px;*/
  background-color: #ccc;
}

  .body {
    height: auto;
  }
  
  .pageWrapper {
    margin: 0px;
  }

  .flexWrapper {
    display: flex;
    flex-flow: row no-wrap;
  }

  .flexChild {
    flex: 1;
    justify-content: space-around;
  }

  .mainBlock {
    padding: 20px;
    align-content: top;
  }

  .user-section, .scheduled-section, .coverage-section {
    width: 80%;
    margin-left: 10%;
    margin-right: 10%;
    margin-top: 40px;
    border: 1px solid black;
    padding: 10px;
  }

  .block {
    padding: 0px;
    /*border: 1px solid black;*/
    min-height: 200px;
  }
  .block-header {
    padding: 10px;
    background-color: #ccc;
  }
  .block-subheader {
    padding: 10px;
    background-color: #ddd;
  }
  .block-body {
    padding: 20px;
    background-color: #eee;
  }
  .block-footer {
    padding: 20px;
    background-color: '#eee';
  }
  .coverageBlock {
    border: solid black 3px;
    padding: 20px;
    min-width: 200px;
    max-width: 400px;
  }
  .mainBlock {
    border-top: solid black 3px;
    border-right: solid black 3px;
    padding: 20px;
    transition: opacity 1s ease;
  }
  .mainSection {
    transition: visibility 0s linear 0s, opacity 1000ms;
  }

  .footer {
    padding-left: 40px;
    padding-right: 40px;
    padding-top: 15px;
    padding-bottom: 15px;
  }
  .logo-s {
    max-height: 70px;
    margin-top: -15px;
    padding-right: 50px;
  }

</style>
